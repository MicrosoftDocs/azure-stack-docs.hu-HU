---
title: Azure-csatlakozású központi telepítési döntések az Azure Stack hub integrált rendszereihez
description: A központi telepítési tervezési döntések meghatározása a Azure Stack hub integrált rendszereinek Azure-beli csatlakoztatásához, beleértve a számlázást és az identitást.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f929654eb723f900ebe8a2beefd84afb064e7056
ms.sourcegitcommit: 4f1d22747c02ae280609174496933fca8c04a6cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102606380"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Azure-csatlakozású üzembe helyezési tervezési döntések az Azure Stack hub integrált rendszereihez
Miután eldöntötte, [hogyan integrálja Azure stack hubot a hibrid felhőalapú környezetbe](azure-stack-connection-models.md), véglegesítheti a Azure stack hub telepítési döntéseit.

Az Azure-hoz csatlakoztatott Azure Stack hub üzembe helyezése azt jelenti, hogy Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) van az identitás-tárolóhoz. A számlázási modellből is választhat: utólagos használat vagy kapacitás alapján. A csatlakoztatott üzemelő példány az alapértelmezett beállítás, mivel lehetővé teszi, hogy az ügyfelek a lehető legtöbbet hozzanak ki Azure Stack központból, különösen az Azure-t és Azure Stack hub-t is magában foglaló hibrid felhőalapú forgatókönyvek esetén.

## <a name="choose-an-identity-store"></a>Identitás-tároló kiválasztása
A csatlakoztatott üzemelő példányok közül választhat az Azure AD vagy az Identity Store-AD FS közül. A leválasztott, internetkapcsolat nélküli központi telepítés csak AD FS használatát tudja használni.

Az Identity Store-beli választáshoz nem tartozik a bérlői virtuális gépek (VM-EK). A bérlői virtuális gépek kiválaszthatják, hogy melyik Identity Store-t szeretnék konfigurálni a csatlakozáshoz, attól függően, hogy hogyan lesznek konfigurálva: Azure AD, Windows Server Active Directory tartományhoz csatlakoztatott, munkacsoport stb. Ez nem kapcsolódik az Azure Stack hub Identity Provider döntéséhez.

Ha például az IaaS-bérlői virtuális gépeket Azure Stack hub-ra telepíti, és szeretné, hogy a vállalati Active Directory-tartományhoz kapcsolódjanak, és ott is használhassák a fiókokat, akkor továbbra is használható. A fiókokhoz itt kiválasztott Azure AD Identity Store-t nem kell használnia.

### <a name="azure-ad-identity-store"></a>Azure AD Identity Store
Az Azure AD az Identity Store-hoz való használatához két Azure AD-fiókra van szükség: egy globális rendszergazdai fiókra és egy számlázási fiókra. Ezek a fiókok ugyanazok a fiókok vagy más fiókok lehetnek. Ha korlátozott számú Azure-fiókkal rendelkezik, előfordulhat, hogy az üzleti igények két fiókot használhatnak:

1. **Globális rendszergazdai fiók** (csak a csatlakoztatott üzemelő példányokhoz szükséges). Ez egy Azure-fiók, amellyel alkalmazásokat és egyszerű szolgáltatásokat hozhat létre az Azure AD Azure Stack hub infrastruktúra-szolgáltatásaihoz. Ennek a fióknak rendszergazdai jogosultságokkal kell rendelkeznie ahhoz a címtárhoz, amelyre a Azure Stack hub rendszer telepítve lesz. Ez lesz a "Cloud Operator" globális rendszergazdája az Azure AD-felhasználó számára, és a következő feladatokhoz használható:

    - Alkalmazások és egyszerű szolgáltatások kiépítése és delegálása minden olyan Azure Stack hub-szolgáltatáshoz, amelyeknek működniük kell az Azure AD-vel és a Graph APIával.
    - A szolgáltatás-rendszergazdai fiókkal. Ez a fiók az alapértelmezett szolgáltatói előfizetés tulajdonosa (amelyet később módosíthat). A fiókkal bejelentkezhet a Azure Stack hub felügyeleti portálra, és a használatával ajánlatokat és csomagokat hozhat létre, kvótákat állíthat be, és más felügyeleti funkciókat is elvégezhet Azure Stack központban.

> [!IMPORTANT]
> - A globális rendszergazdai fiók nem szükséges Azure Stack hub futtatásához, és le lehet tiltani a telepítést követően.
> - Gondoskodjon a globális rendszergazdai fiók védelméről az [itt dokumentált ajánlott eljárások](/azure/security/fundamentals/identity-management-best-practices)követésével.


2. **Számlázási fiók** (a csatlakoztatott és a leválasztott üzemelő példányokhoz egyaránt szükséges). Ez az Azure-fiók az Azure Stack hub integrált rendszer és az Azure kereskedelmi háttérrendszer közötti számlázási kapcsolat létesítésére szolgál. Ez az a fiók, amelyet Azure Stack hub-díjakért kell fizetni. Ez a fiók a piactéren és más hibrid forgatókönyvekben található elemek ajánlatára is használható.

### <a name="ad-fs-identity-store"></a>AD FS Identity Store
Válassza ezt a lehetőséget, ha saját identitás-tárolót szeretne használni, például a vállalati Active Directoryt a szolgáltatás-rendszergazdai fiókjaihoz.  

## <a name="choose-a-billing-model"></a>Számlázási modell kiválasztása
Kiválaszthatja az **Ön által használt fizetés** vagy a **kapacitás** számlázási modelljét. Az utólagos használatú számlázási modell telepítéseknek képesnek kell lenniük arra, hogy a használatot az Azure-hoz legalább 30 naponta egyszer kell jelenteni. Ezért a használaton kívüli számlázási modell csak a csatlakoztatott üzemelő példányok esetében érhető el.  

### <a name="pay-as-you-use"></a>Használatalapú fizetés
Az utólagos használatú számlázási modell használata esetén az Azure-előfizetésért kell fizetnie. Csak az Azure Stack hub-szolgáltatások használatakor kell fizetnie. Ha ezt a modellt választja, szüksége lesz egy Azure-előfizetésre és az ehhez az előfizetéshez hozzárendelt fiók-AZONOSÍTÓra (például: serviceadmin@contoso.onmicrosoft.com ). Az EA, CSP és CSP megosztott szolgáltatások előfizetései támogatottak. A használati jelentéskészítés [Azure stack hub-regisztráció](azure-stack-registration.md)során van konfigurálva.

> [!NOTE]
> A legtöbb esetben a nagyvállalati ügyfelek az EA-előfizetéseket fogják használni, és a szolgáltatók a CSP-vagy CSP közös szolgáltatások előfizetéseit használják.

Ha CSP-előfizetést fog használni, tekintse át az alábbi táblázatot a használni kívánt CSP-előfizetés azonosításához, mivel a helyes módszer a pontos CSP-forgatókönyvtől függ:

|Eset|Tartomány-és előfizetési lehetőségek|
|-----|-----|
|Ön egy **közvetlen CSP-partner** vagy egy **közvetett CSP-szolgáltató**, és az Azure stack hubot fogja használni|Használjon CSP megosztott szolgáltatások előfizetést.<br>     vagy<br>Hozzon létre egy Azure AD-bérlőt egy leíró névvel a partner Centerben. A &lt; Szervezete például>CSPAdmin egy hozzá társított Azure CSP-előfizetéssel.|
|Ön egy **közvetett CSP-viszonteladó**, és az Azure stack hubot fogja használni|Kérje meg a közvetett CSP-szolgáltatót, hogy hozzon létre egy Azure AD-bérlőt a szervezete számára a partner Center használatával társított Azure CSP-előfizetéssel.|

### <a name="capacity-based-billing"></a>Kapacitás alapú számlázás
Ha úgy dönt, hogy a kapacitás számlázási modelljét használja, akkor a rendszer kapacitása alapján meg kell vásárolnia egy Azure Stack hub kapacitási csomag SKU-t. A megfelelő mennyiség megvásárlásához ismernie kell a Azure Stack hub-beli fizikai magok számát.

A kapacitás számlázásához Nagyvállalati Szerződés (EA) Azure-előfizetés szükséges a regisztrációhoz. Ennek az az oka, hogy a regisztráció a piactéren elérhető elemek rendelkezésre állását állítja be, amelyhez Azure-előfizetés szükséges. Az előfizetés nem használatos Azure Stack hub-használathoz.

## <a name="learn-more"></a>Tudjon meg többet
- További információ a használati esetekről, a beszerzésről, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack hub](https://azure.microsoft.com/overview/azure-stack/) terméke oldal.
- Az Azure Stack hub integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a következő tanulmányt: [Azure stack hub: az Azure kiterjesztése](https://azure.microsoft.com/resources/videos/azure-friday-azure-stack-an-extension-of-azure/). 
- Ha többet szeretne megtudni a Microsoft Azure Stack hub csomagolásáról és díjszabásáról, [töltse le a. PDF fájlt](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Következő lépések
[Datacenter hálózati integráció](azure-stack-network.md)
