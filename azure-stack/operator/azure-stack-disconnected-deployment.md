---
title: Az Azure leválasztott telepítési döntései Azure Stack integrált rendszerek esetében | Microsoft Docs
description: Ismerje meg az Azure-Azure Stack integrált rendszerek központi telepítését és a megfontolandó tervezési döntéseket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: f4b8f73987df3067c8d69504934884ec4329cacf
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829152"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure leválasztott üzembe helyezési tervezési döntései Azure Stack integrált rendszerek esetében
Miután eldöntötte, [Hogyan integrálhatja a Azure stackt a hibrid felhőalapú környezetbe](azure-stack-connection-models.md), befejezheti Azure stack telepítési döntéseit.

Azure Stack az internethez való kapcsolódás nélkül is telepítheti és használhatja. A leválasztott központi telepítés azonban csak egy Active Directory összevonási szolgáltatások (AD FS) (AD FS) Identity Store-ra és a Capacity-alapú számlázási modellre korlátozódik. Mivel a bérlős Azure Active Directory (Azure AD) használatát igényli, a bérlős nem támogatott a leválasztott központi telepítések esetén.

Válassza ezt a lehetőséget, ha:
- Olyan biztonsági vagy egyéb korlátozásokkal rendelkezik, amelyek megkövetelik a Azure Stack telepítését olyan környezetben, amely nem kapcsolódik az internethez.
- Le kívánja tiltani az adatokat (beleértve a használati adatokat is) az Azure-ba történő küldéshez.
- Azt szeretné, hogy a Azure Stack tisztán a vállalati intranetre telepített privát felhőalapú megoldást használja, és nem érdeklik a hibrid forgatókönyvek.

> [!TIP]
> Néha ez a fajta környezet egy *tengeralattjáró-forgatókönyvnek*is nevezik.

A leválasztott központi telepítés nem korlátozza, hogy később összekapcsolja Azure Stack-példányát az Azure-ba hibrid bérlői virtuálisgép-forgatókönyvek esetén. Ez azt jelenti, hogy az üzembe helyezés során nem kapcsolódik az Azure-hoz, vagy nem kívánja az Azure AD-t az identitás-tárolóként használni.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>A leválasztott üzemelő példányokban nem párosított vagy nem elérhető funkciók 
Azure Stack úgy lett tervezve, hogy az Azure-hoz való csatlakozáskor a legjobban működjön, ezért fontos megjegyezni, hogy vannak olyan funkciók és funkciók, amelyek vagy a leválasztott módban nem állnak rendelkezésre vagy teljesen elérhetetlenné válnak.

|Funkció|Leválasztott üzemmódra gyakorolt hatás|
|-----|-----|
|VM üzembe helyezése DSC bővítménnyel a virtuális gép üzembe helyezésének konfigurálásához|A nem párosított DSC-bővítmény az internetet keresi a legújabb WMF-hez.|
|Virtuális gép üzembe helyezése Docker-bővítménnyel a Docker-parancsok futtatásához|A korlátozott – a Docker a legújabb verzióra fogja ellenőriznie az internetet, és ez az ellenőrzési művelet sikertelen lesz.|
|Dokumentációs hivatkozások a Azure Stack portálon|Nem érhető el – olyan hivatkozások, mint például a visszajelzések, a Súgó és az internetes URL-címet használó gyors bevezetés nem fog működni.|
|Riasztások szervizelése/enyhítése, amely egy online szervizelési útmutatóra hivatkozik|Nem érhető el – az internetes URL-címet használó riasztási szervizelési hivatkozások nem működnek.|
|Piactér – katalógus-csomagok kiválasztásának és hozzáadásának lehetősége közvetlenül az Azure Marketplace-ről|Korlátozott – ha a Azure Stackt leválasztott módban telepíti, a Azure Stack portál használatával nem töltheti le a Marketplace-elemeket. A [Marketplace Syndication eszközzel](azure-stack-download-azure-marketplace-item.md) azonban letöltheti a piactér elemeit egy internetkapcsolattal rendelkező gépre, majd átviheti azokat a Azure stack-környezetbe.|
|Azure Stack központi telepítésének kezelése az Azure AD összevonási fiókok használatával|Nem érhető el – ez a funkció az Azure-hoz való kapcsolódást igényli. Ehelyett helyi Active Directory-példánnyal rendelkező AD FS kell használni.|
|App Services|A korlátozott WebApps a frissített tartalomhoz internet-hozzáférésre lehet szükség.|
|Parancssori felület (CLI)|A korlátozott – a parancssori felület csökkentett funkcionalitást biztosít az egyszerű szolgáltatások hitelesítéséhez és üzembe helyezéséhez.|
|Visual Studio – Cloud Discovery|A nem párosított Cloud Discovery a különböző felhők felderítését vagy a nem megfelelő működést eredményezi.|
|Visual Studio – AD FS|Csak a Visual Studio Enterprise és a Visual Studio Code támogatja a AD FS hitelesítést.
Telemetria|Nem érhető el – a telemetria adatai Azure Stack és bármely harmadik féltől származó, a telemetria-adatoktól függő katalógus-csomaghoz.|
|Tanúsítványok|Nem érhető el – az internetkapcsolat szükséges a visszavont tanúsítványok listájának (CRL) és az online tanúsítványállapot-protokoll (OSCP) szolgáltatásai számára a HTTPS kontextusában.|
|Key Vault|Nem párosított – A Key Vault gyakori használati esete, ha egy alkalmazás olvasási titkokat használ futásidőben. Ebben a használati esetben az alkalmazásnak szüksége van egy egyszerű szolgáltatásnév használatára a címtárban. Az Azure AD-ben a normál felhasználók (nem rendszergazdák) alapértelmezés szerint adhatnak hozzá egyszerű szolgáltatásokat. Az Azure AD-ben (AD FS használatával) nem. Ez az inpair akadályt mutat a teljes körű élményben, mivel az egyiknek mindig egy címtár-rendszergazdának kell lennie az alkalmazások hozzáadásához.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
- További információ a használati esetekről, a vásárlásról, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack](https://azure.microsoft.com/overview/azure-stack/) termék oldal.
- Az Azure Stack integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a tanulmányt: [Azure Stack: Az Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)kiterjesztése. 
- Ha többet szeretne megtudni a Microsoft Azure Stack csomagolásról és a díjszabásról, [töltse le a. PDF fájlt](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)
