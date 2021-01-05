---
title: Az Azure leválasztott telepítési döntései az Azure Stack hub integrált rendszereihez
description: Ismerkedjen meg az Azure-beli, Azure Stack hub integrált rendszerek központi telepítésével és a megfontolandó tervezési döntésekkel.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: wfayed
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 98633108390ee24416c9383ed8171439a4ee1459
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870851"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Az Azure leválasztott üzembe helyezési tervezési döntései az Azure Stack hub integrált rendszereihez
Miután eldöntötte, [hogyan integrálja Azure stack hubot a hibrid felhőalapú környezetbe](azure-stack-connection-models.md), befejezheti a Azure stack hub telepítési döntéseit.

Azure Stack hub-t az internethez való kapcsolódás nélkül telepítheti és használhatja. A leválasztott központi telepítés azonban csak egy Active Directory összevonási szolgáltatások (AD FS) (AD FS) Identity Store-ra és a Capacity-alapú számlázási modellre korlátozódik. Mivel a bérlős Azure Active Directory (Azure AD) használatát igényli, a bérlős nem támogatott a leválasztott központi telepítések esetén.

Válassza ezt a lehetőséget, ha:
- Olyan biztonsági vagy egyéb korlátozásokkal rendelkezik, amelyek megkövetelik Azure Stack hub központi telepítését olyan környezetben, amely nem kapcsolódik az internethez.
- Le kívánja tiltani az adatokat (beleértve a használati adatokat is) az Azure-ba történő küldéshez.
- A Azure Stack hub-t kizárólag a vállalati intranetre telepített privát felhőalapú megoldásként kívánja használni, és nem érdeklik a hibrid forgatókönyvek.

> [!TIP]
> Néha ez a fajta környezet egy *tengeralattjáró-forgatókönyvnek* is nevezik.

A leválasztott központi telepítés nem korlátozza, hogy később összekapcsolja az Azure Stack hub-példányt az Azure-ba hibrid bérlői virtuálisgép-forgatókönyvek esetén. Ez azt jelenti, hogy az üzembe helyezés során nem kapcsolódik az Azure-hoz, vagy nem kívánja az Azure AD-t az identitás-tárolóként használni.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>A leválasztott üzemelő példányokban nem párosított vagy nem elérhető funkciók 
Azure Stack hub úgy lett kialakítva, hogy a legjobban működjön az Azure-hoz való csatlakozáskor, ezért fontos megjegyezni, hogy vannak olyan funkciók és funkciók, amelyek vagy a leválasztott módban nem állnak rendelkezésre vagy teljesen elérhetetlenné válnak.

|Szolgáltatás|Leválasztott üzemmódra gyakorolt hatás|
|-----|-----|
|VM üzembe helyezése DSC bővítménnyel a virtuális gép üzembe helyezésének konfigurálásához|A nem párosított DSC-bővítmény az internetet keresi a legújabb WMF-hez.|
|Virtuális gép üzembe helyezése Docker-bővítménnyel a Docker-parancsok futtatásához|A korlátozott – a Docker a legújabb verzióra fogja ellenőriznie az internetet, és ez az ellenőrzési művelet sikertelen lesz.|
|Dokumentációs hivatkozások az Azure Stack hub portálon|Nem érhető el – olyan hivatkozások, mint például a visszajelzések, a Súgó és az internetes URL-címet használó gyors bevezetés nem fog működni.|
|Riasztások szervizelése/enyhítése, amely egy online szervizelési útmutatóra hivatkozik|Nem érhető el – az internetes URL-címet használó riasztási szervizelési hivatkozások nem működnek.|
|Piactér – katalógus-csomagok kiválasztásának és hozzáadásának lehetősége közvetlenül az Azure Marketplace-ről|Nem párosítva – ha a Azure Stack hubot leválasztott módban telepíti, a Azure Stack hub portál használatával nem töltheti le a Marketplace-elemeket. A [Marketplace Syndication eszközzel](azure-stack-download-azure-marketplace-item.md) azonban letöltheti a piactér elemeit egy internetkapcsolattal rendelkező gépre, majd átviheti azokat a Azure stack hub-környezetbe.|
|Az Azure AD összevonási fiókok használata Azure Stack hub központi telepítésének kezeléséhez|Nem érhető el – ez a funkció az Azure-hoz való kapcsolódást igényli. Ehelyett helyi Active Directory-példánnyal rendelkező AD FS kell használni.|
|App Services|A korlátozott WebApps a frissített tartalomhoz internet-hozzáférésre lehet szükség.|
|Parancssori felület (CLI)|A korlátozott – a parancssori felület csökkentett funkcionalitást biztosít az egyszerű szolgáltatások hitelesítéséhez és üzembe helyezéséhez.|
|Visual Studio – Cloud Discovery|A nem párosított Cloud Discovery a különböző felhők felderítését vagy a nem megfelelő működést eredményezi.|
|Visual Studio – AD FS|Csak a Visual Studio Enterprise és a Visual Studio Code támogatja a AD FS hitelesítést.
Telemetria|Nem érhető el – az Azure Stack hub és a külső gyártótól származó telemetria adatai, amelyek a telemetria-adatoktól függenek.|
|Tanúsítványok|Nem érhető el – az internetkapcsolat szükséges a visszavont tanúsítványok listájának (CRL) és az online tanúsítványállapot-protokoll (OSCP) szolgáltatásai számára a HTTPS kontextusában.|
|Key Vault|Nem párosított – A Key Vault gyakori használati esete, ha egy alkalmazás olvasási titkokat használ futásidőben. Ebben a használati esetben az alkalmazásnak szüksége van egy egyszerű szolgáltatásnév használatára a címtárban. Az Azure AD-ben a normál felhasználók (nem rendszergazdák) alapértelmezés szerint adhatnak hozzá egyszerű szolgáltatásokat. Az Azure AD-ben (AD FS használatával) nem. Ez az inpair akadályt mutat a teljes körű élményben, mivel az egyiknek mindig egy címtár-rendszergazdának kell lennie az alkalmazások hozzáadásához.

## <a name="learn-more"></a>További információ
- További információ a használati esetekről, a beszerzésről, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack hub](https://azure.microsoft.com/overview/azure-stack/) terméke oldal.
- Az Azure Stack hub integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a következő tanulmányt: [Azure stack hub: az Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Ha többet szeretne megtudni a Microsoft Azure Stack hub csomagolásáról és díjszabásáról, [töltse le a. PDF fájlt](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)
