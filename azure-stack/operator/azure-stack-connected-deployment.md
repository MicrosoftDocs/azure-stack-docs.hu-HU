---
title: Azure-csatlakozású üzembe helyezési döntések Azure Stack integrált rendszerekhez | Microsoft Docs
description: A központi telepítési tervezési döntések meghatározása az Azure-hoz csatlakoztatott Azure Stack integrált rendszerek, beleértve a számlázást és az identitást.
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
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fa90091f93556cd313fa8e4e21bfe0fd24011e38
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159146"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure-csatlakozású üzembe helyezési tervezési döntések Azure Stack integrált rendszerekhez
Miután eldöntötte, [Hogyan integrálhatja a Azure stackt a hibrid felhőalapú környezetbe](azure-stack-connection-models.md), véglegesítheti Azure stack telepítési döntéseit.

Az Azure-hoz csatlakoztatott Azure Stack üzembe helyezése azt jelenti, hogy Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehet az identitás-tárolóhoz. A számlázási modellből is választhat: utólagos használat vagy kapacitás alapján. A csatlakoztatott üzemelő példány az alapértelmezett beállítás, mivel lehetővé teszi, hogy az ügyfelek a lehető legtöbbet hozzanak ki Azure Stack közül, különösen az Azure-t és a Azure Stackt is magában foglaló hibrid felhőalapú forgatókönyvek esetén.

## <a name="choose-an-identity-store"></a>Identitás-tároló kiválasztása
A csatlakoztatott üzemelő példányok közül választhat az Azure AD vagy az Identity Store-AD FS közül. A leválasztott, internetkapcsolat nélküli központi telepítés csak AD FS használatát tudja használni.

Az Identity Store-beli választáshoz nem tartozik a bérlői virtuális gépek (VM-EK). A bérlői virtuális gépek kiválaszthatják, hogy melyik Identity Store-t szeretnék konfigurálni a kapcsolódáshoz a konfigurálásuk módjától függően: Az Azure AD, a Windows Server Active Directory tartományhoz csatlakoztatott, munkacsoportos és így tovább. Ez nem kapcsolódik a Azure Stack Identity Provider döntéséhez.

Ha például a IaaS-bérlő virtuális gépeket a Azure Stackon helyezi üzembe, és szeretné, hogy egy vállalati Active Directory-tartományhoz kapcsolódjanak, és ott is használhassák a fiókokat, akkor továbbra is használható. A fiókokhoz itt kiválasztott Azure AD Identity Store-t nem kell használnia.

### <a name="azure-ad-identity-store"></a>Azure AD Identity Store
Az Azure AD az Identity Store-hoz való használatához két Azure AD-fiókra van szükség: egy globális rendszergazdai fiókra és egy számlázási fiókra. Ezek a fiókok ugyanazok a fiókok vagy más fiókok lehetnek. Ha korlátozott számú Azure-fiókkal rendelkezik, előfordulhat, hogy az üzleti igények két fiókot használhatnak:

1. **Globális rendszergazdai fiók** (csak a csatlakoztatott üzemelő példányokhoz szükséges). Ez egy Azure-fiók, amellyel alkalmazásokat és egyszerű szolgáltatásokat hozhat létre Azure Stack infrastruktúra-szolgáltatásokhoz az Azure AD-ben. Ennek a fióknak rendszergazdai jogosultságokkal kell rendelkeznie ahhoz a címtárhoz, amelyre a Azure Stack rendszer telepítve lesz. Ez lesz a "Cloud Operator" globális rendszergazdája az Azure AD-felhasználó számára, és a következő feladatokhoz használható:

    - Alkalmazások és egyszerű szolgáltatások kiépítése és delegálása minden olyan Azure Stack szolgáltatáshoz, amelyeknek működniük kell az Azure AD-vel és a Graph APIval.
    - A szolgáltatás-rendszergazdai fiókkal. Ez a fiók az alapértelmezett szolgáltatói előfizetés tulajdonosa (amelyet később módosíthat). Ezzel a fiókkal bejelentkezhet a Azure Stack felügyeleti portálra, és a használatával ajánlatokat és csomagokat hozhat létre, kvótákat állíthat be, és más felügyeleti funkciókat is elvégezhet Azure Stackokban.

2. **Számlázási fiók** (a csatlakoztatott és a leválasztott üzemelő példányokhoz egyaránt szükséges). Ez az Azure-fiók a Azure Stack integrált rendszer és az Azure kereskedelmi háttérrendszer közötti számlázási kapcsolat létesítésére szolgál. Ez az a fiók, amelyet Azure Stack díjakért kell fizetni. Ez a fiók a piactéren és más hibrid forgatókönyvekben található elemek ajánlatára is használható.

### <a name="ad-fs-identity-store"></a>AD FS Identity Store
Válassza ezt a lehetőséget, ha saját identitás-tárolót szeretne használni, például a vállalati Active Directoryt a szolgáltatás-rendszergazdai fiókjaihoz.  

## <a name="choose-a-billing-model"></a>Számlázási modell kiválasztása
Kiválaszthatja az **Ön által használt fizetés** vagy a **kapacitás** számlázási modelljét. Az utólagos használatú számlázási modell telepítéseknek képesnek kell lenniük arra, hogy a használatot az Azure-hoz legalább 30 naponta egyszer kell jelenteni. Ezért a használaton kívüli számlázási modell csak a csatlakoztatott üzemelő példányok esetében érhető el.  

### <a name="pay-as-you-use"></a>Használatalapú fizetés
Az utólagos használatú számlázási modell használata esetén az Azure-előfizetésért kell fizetnie. Csak a Azure Stack szolgáltatások használatakor kell fizetnie. Ha ezt a modellt választja, szüksége lesz egy Azure-előfizetésre és az ehhez az előfizetéshez hozzárendelt fiók-AZONOSÍTÓra serviceadmin@contoso.onmicrosoft.com(például:). Az EA, CSP és CSL-előfizetések támogatottak. A használati jelentéskészítés [Azure stack regisztráció](azure-stack-registration.md)során van konfigurálva.

> [!NOTE]
> A legtöbb esetben a nagyvállalati ügyfelek az EA-előfizetéseket fogják használni, és a szolgáltatók CSP-vagy CSL-előfizetéseket használnak.

Ha CSP-előfizetést fog használni, tekintse át az alábbi táblázatot a használni kívánt CSP-előfizetés azonosításához, mivel a helyes módszer a pontos CSP-forgatókönyvtől függ:

|Forgatókönyv|Tartomány-és előfizetési lehetőségek|
|-----|-----|
|Ön **közvetlen CSP-partner** vagy **közvetett CSP-szolgáltató**, és a Azure stack fogja használni|Használjon CSL (Common Service Layer) előfizetést.<br>     vagy<br>Hozzon létre egy Azure AD-bérlőt egy leíró névvel a partner Centerben. A szervezete &lt;például > CSPAdmin egy hozzá társított Azure CSP-előfizetéssel.|
|Ön egy **közvetett CSP-viszonteladó**, és a Azure stack fogja használni|Kérje meg a közvetett CSP-szolgáltatót, hogy hozzon létre egy Azure AD-bérlőt a szervezete számára a partner Center használatával társított Azure CSP-előfizetéssel.|

### <a name="capacity-based-billing"></a>Kapacitás alapú számlázás
Ha úgy dönt, hogy a kapacitás számlázási modelljét használja, akkor a rendszer kapacitása alapján meg kell vásárolnia egy Azure Stack kapacitási csomag SKU-t. A megfelelő mennyiség megvásárlásához ismernie kell a Azure Stack fizikai magok számát.

A kapacitás számlázásához Nagyvállalati Szerződés (EA) Azure-előfizetés szükséges a regisztrációhoz. Ennek az az oka, hogy a regisztráció a piactéren elérhető elemek rendelkezésre állását állítja be, amelyhez Azure-előfizetés szükséges. Az előfizetés nem használatos Azure Stack használatra.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
- További információ a használati esetekről, a vásárlásról, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack](https://azure.microsoft.com/overview/azure-stack/) termék oldal.
- Az Azure Stack integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a tanulmányt: [Azure Stack: Az Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)kiterjesztése. 
- Ha többet szeretne megtudni a Microsoft Azure Stack csomagolásról és a díjszabásról, [töltse le a. PDF fájlt](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)