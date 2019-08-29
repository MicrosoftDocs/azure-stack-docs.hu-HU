---
title: Az Azure leválasztott telepítési döntései Azure Stack integrált rendszerek esetében | Microsoft Docs
description: A többcsomópontos Azure Stack Azure-beli üzemelő példányokra vonatkozó központi telepítési tervezési döntések meghatározása.
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
ms.openlocfilehash: 11bf3bd4cc670d45fc4a4c9d1421fc0c25440726
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118676"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure leválasztott üzembe helyezési tervezési döntései Azure Stack integrált rendszerek esetében
Miután eldöntötte, [hogyan integrálja Azure stack a hibrid felhőalapú környezetbe](azure-stack-connection-models.md), véglegesítheti Azure stack telepítési döntéseit.

Azure Stack az internethez való kapcsolódás nélkül is telepítheti és használhatja. A leválasztott központi telepítés azonban csak egy AD FS Identity Store-ra és a Capacity-alapú számlázási modellre korlátozódik. Mivel a bérlős Azure Active Directory (Azure AD) használatát igényli, a bérlős nem támogatott a leválasztott központi telepítések esetén. 

Válassza ezt a lehetőséget, ha:
- Olyan biztonsági vagy egyéb korlátozásokkal rendelkezik, amelyek megkövetelik a Azure Stack telepítését olyan környezetben, amely nem kapcsolódik az internethez.
- Szeretné letiltani az adatokat (beleértve a használati adatokat is) az Azure-ba történő küldéshez.
- Kizárólag a vállalati intranetre telepített privát felhőalapú megoldásként szeretné használni a Azure Stackt, és nem érdeklik a hibrid forgatókönyvek.

> [!TIP]
> Néha ez a típusú környezet a *tengeralattjáró*-forgatókönyvnek is nevezik.

A leválasztott központi telepítés nem korlátozza, hogy később összekapcsolja Azure Stack példányát az Azure-ba hibrid bérlői virtuálisgép-forgatókönyvek esetén. Ez azt jelenti, hogy az üzembe helyezés során nem kapcsolódik az Azure-hoz, vagy nem kívánja az Azure AD-t az identitás-tárolóként használni.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>A leválasztott üzemelő példányokban nem párosított vagy nem elérhető funkciók 
Azure Stack úgy lett tervezve, hogy az Azure-hoz való csatlakozáskor a legjobban működjön, ezért fontos megjegyezni, hogy vannak olyan funkciók és funkciók, amelyek vagy a leválasztott módban nem állnak rendelkezésre vagy teljesen elérhetetlenné válnak. 

|Funkció|Leválasztott üzemmódra gyakorolt hatás|
|-----|-----|
|VM üzembe helyezése DSC bővítménnyel a virtuális gép üzembe helyezésének konfigurálásához|A nem párosított DSC-bővítmény az internetet keresi a legújabb WMF-hez.|
|Virtuális gép üzembe helyezése Docker-bővítménnyel a Docker-parancsok futtatásához|A korlátozott – a Docker a legújabb verzióra fogja ellenőriznie az internetet, és ez az ellenőrzési művelet sikertelen lesz.|
|Dokumentációs hivatkozások a Azure Stack portálon|Nem érhető el – olyan hivatkozások, mint például a visszajelzések, a Súgó, a gyors bevezetés stb., amelyek az internetes URL-címet használják, nem fognak működni.|
|Riasztások szervizelése/enyhítése, amely egy online szervizelési útmutatóra hivatkozik|Nem érhető el – az internetes URL-címet használó riasztási szervizelési hivatkozások nem működnek.|
|Piactér – katalógus-csomagok kiválasztásának és hozzáadásának lehetősége közvetlenül az Azure Marketplace-ről|Nem párosítva – ha a Azure Stackt leválasztott módban (internetkapcsolat nélkül) telepíti, nem töltheti le a Marketplace-elemeket a Azure Stack portál használatával. A [Marketplace Syndication eszközzel](azure-stack-download-azure-marketplace-item.md) azonban letöltheti a piactér elemeit egy internetkapcsolattal rendelkező gépre, majd átviheti azokat a Azure stack-környezetbe.|
|Azure Active Directory összevonási fiókok használata Azure Stack központi telepítés kezelésére|Nem érhető el – ez a funkció az Azure-hoz való kapcsolódást igényli. Ehelyett helyi Active Directory-példánnyal rendelkező AD FS kell használni.|
|App Services|A korlátozott WebApps a frissített tartalomhoz internet-hozzáférésre lehet szükség.|
|Parancssori felület (CLI)|A korlátozott – a CLI a hitelesítés és az egyszerű szolgáltatások kiépítés terén csökkentett funkcionalitással rendelkezik.|
|Visual Studio – Cloud Discovery|A nem párosított Cloud Discovery a különböző felhők felderítésére vagy a nem megfelelő működésre is kiterjednek.|
|Visual Studio – AD FS|Csak a Visual Studio Enterprise és a Visual Studio Code támogatja a AD FS hitelesítést.
Telemetria|Nem érhető el – a Azure Stack telemetria adatai, valamint a telemetria-adatoktól függő harmadik féltől származó katalógusok csomagjai.|
|Tanúsítványok|Nem érhető el – az internetkapcsolat szükséges a visszavont tanúsítványok listájának (CRL) és az online tanúsítványállapot-protokoll (OSCP) szolgáltatásai számára a HTTPS kontextusában.|
|Key-Vault|Nem párosított – A Key Vault gyakori használati esete, ha egy alkalmazás olvasási titkokat használ futásidőben. Ehhez az alkalmazáshoz egy egyszerű szolgáltatásnév szükséges a címtárban. Azure Active Directory a normál felhasználók (nem rendszergazdák) alapértelmezés szerint adhatnak hozzá egyszerű szolgáltatásokat. Az AD-ben (az ADFS használatával) nem. Ez egy akadályt mutat a teljes körű élményben, mivel az egyiknek mindig egy címtár-rendszergazdával kell eljárnia bármely alkalmazás hozzáadásához.| 

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
- További információ a használati esetekről, a vásárlásról, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack](https://azure.microsoft.com/overview/azure-stack/) termék oldal.
- Az Azure Stack integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a tanulmányt: [Azure Stack: Az Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)kiterjesztése. 
- Ha többet szeretne megtudni Microsoft Azure Stack csomagolásról és díjszabásról, [töltse le a. PDF fájlt](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)
