---
title: Az Azure csatlakoztatott telepítési döntések meghozatalában az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Tervezési megfontolások a több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező központi telepítések központi telepítési határozza meg.
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
ms.openlocfilehash: 1d5d6399887d5567806e8a9131444b5eada2f719
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131190"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure csatlakoztatott üzembe helyezési, tervezési megfontolások az Azure Stack integrált rendszerek
Miután döntött [hogyan fogja integrálja az Azure Stack a hibridfelhő-környezet](azure-stack-connection-models.md), az Azure Stack telepítési döntések is véglegesítése.

Kapcsolódik az Azure üzembe helyezése az Azure Stack azt jelenti, hogy is vagy az Azure Active Directory (Azure AD), vagy az Active Directory összevonási szolgáltatások (AD FS) számára az ügyfélidentitás-tárolóval. Emellett vagy számlázási modell közül választhat: használatalapú mint-akkor-használható, vagy kapacitás-alapú. Egy csatlakoztatott központi telepítés az alapértelmezett beállítás, mert lehetővé teszi a felhasználóknak lekérése különösen a legnagyobb érték a tartományon kívül az Azure Stack, hibrid felhő forgatókönyvekről, az Azure és az Azure Stackben. 

## <a name="choose-an-identity-store"></a>Válassza ki az ügyfélidentitás-tárolóval
Egy csatlakoztatott üzembe helyezéssel az Azure AD vagy az AD FS-hez az ügyfélidentitás-tárolóval, választhat. Egy kapcsolat nélküli telepítés internetkapcsolat nélküli is csak az AD FS használata.

Identitás-store tetszőleges nem befolyásolta bérlői virtuális gépeket (VM). Bérlő virtuális gépek kiválaszthatja, melyik ügyfélidentitás-tárolóval, a csatlakozás attól függően, hogyan lehet konfigurálni szeretne: Az Azure AD-ben a Windows Server Active Directory-tartományhoz, munkacsoportban stb. Ez az Azure Stack identitás szolgáltató döntésével kapcsolódik. 

Például ha telepíti az IaaS bérlői virtuális gépeknek az Azure Stack felett, és szeretné, hogy csatlakozzon a vállalati Active Directory-tartomány, és onnan fiókok használata, továbbra is ehhez. Nem szükségesek, azok a fiókok az Azure ad-ben ügyfélidentitás-tárolóval, az itt használandó.

### <a name="azure-ad-identity-store"></a>Az Azure AD ügyfélidentitás-tárolóval
Használhatja az Azure AD az ügyfélidentitás-tárolóval, a két Azure AD-fiók szükséges: egy globális rendszergazdai fiókot és a egy számlázási fiók. Ezek a fiókok lehet ugyanazt a fiókot, vagy külön fiókot. Amíg ugyanazzal a fiókkal használatával lehet egyszerűbb és hasznos, ha csak korlátozott számú Azure-fiókok, az üzleti igényeknek megfelelően utalhat a két fiók használatával:

1. **A globális rendszergazdai fiókot** (csatlakoztatott központi telepítések esetén csak szükséges). Ez az Azure-fiók, amellyel alkalmazások és az Azure Stack-infrastruktúra-szolgáltatások egyszerű szolgáltatások létrehozása az Azure Active Directoryban. Ennek a fióknak rendelkeznie kell directory rendszergazdai jogosultságokkal arra a könyvtárra, amely alatt az Azure Stack rendszer lesz telepítve. A "felhő-operátor" válik globális rendszergazda az Azure ad-bérlővel, majd fogja használni: 
    - Alkalmazások üzembe helyezése és a delegált és szolgáltatásnevek kommunikáljon az Azure Active Directory és a Graph API az Azure Stack-szolgáltatásokhoz. 
    - A szolgáltatás-rendszergazdai fiókkal. Ez a tulajdonosa az alapértelmezett szolgáltatója előfizetést (ez később módosítható). Ezzel a fiókkal, az Azure Stack rendszergazdai portálon be tud jelentkezni, és használhatja ajánlatokat és csomagokat, kvótákat és más felügyeleti funkciók végrehajtása az Azure Stackben.
2. **Számlázási fiók** (is csatlakoztathatók, illetve központi telepítések esetében szükséges). Az Azure-fiók az Azure Stackkel integrált rendszereknél és az Azure kereskedelmi háttérrendszer között a számlázási kapcsolat létrehozására szolgál. Ez az a fiók, amely az Azure Stack díjat kell fizetnie. Ez a fiók lesz az ajánlatot a piactérről, és egyéb hibrid forgatókönyvek elemeinek is. 

### <a name="ad-fs-identity-store"></a>Az AD FS ügyfélidentitás-tárolóval
Válassza ezt a lehetőséget, ha azt szeretné használni a saját ügyfélidentitás-tárolóval, például a vállalati Active Directory, a szolgáltatás-rendszergazdai fiókok.  

## <a name="choose-a-billing-model"></a>Válasszon ki egy számlázási modellt
Választhat **Használatalapú mint-akkor-használható** vagy a **kapacitás** számlázási modellt. Használatalapú mint-akkor-használható számlázási modell-üzembehelyezések jelentés használati-kapcsolaton keresztül az Azure-bA 30 nap során legalább egyszer képesnek kell lennie. A Használatalapú mint-akkor-használható számlázási modell ezért csak csatlakoztatott központi telepítéseknél használható.  

### <a name="pay-as-you-use"></a>Használatalapú mint-akkor-használható
A Használatalapú mint-akkor-használható számlázási modell használatáért az Azure-előfizetéshez. Ön csak az Azure Stack-szolgáltatások használatakor kell fizetnie. Ha ez a modell használata mellett dönt, szüksége lesz a Azure-előfizetés és az adott előfizetéshez társított fiók azonosítója (például serviceadmin@contoso.onmicrosoft.com). EA, CSP és CSL előfizetéseket támogatja. Használati jelentések készítése során van konfigurálva [Azure Stack-regisztráció](azure-stack-registration.md).

> [!NOTE]
> A legtöbb esetben a vállalati felhasználók EA-előfizetések fogja használni, és szolgáltatók CSP vagy CSL előfizetést fogja használni.

Ha egy CSP-előfizetéssel, mely CSP-előfizetés használatára az alábbi táblázatban áttekintheti, mint a megfelelő módszer a pontos CSP forgatókönyvtől függ:

|Forgatókönyv|Tartomány- és előfizetés-beállításai|
|-----|-----|
|Ön egy **közvetlen CSP-Partner** vagy egy **közvetett CSP-szolgáltató**, és akkor fog működni az Azure Stack|Használjon CSL (közös szolgáltatási réteg) előfizetést.<br>     vagy<br>Hozzon létre egy Azure AD-bérlőt egy leíró nevet a Partner Center. Például &lt;a szervezet > CSPAdmin Azure CSP-előfizetéssel társítva.|
|Ön egy **közvetett CSP-viszonteladó**, és akkor fog működni az Azure Stack|Kérje meg a közvetett CSP-szolgáltatót, hogy a szervezet Azure AD-bérlő létrehozása az Azure CSP-előfizetéssel társítva használja Partnerközpontban.|

### <a name="capacity-based-billing"></a>A kapacitás alapján számlázás
Ha úgy dönt, hogy a kapacitás számlázási modellt használja, meg kell vásárolnia egy Azure Stack kapacitás megtervezése alapuló Termékváltozat a kapacitás, a rendszer. Meg kell tudni, hogy a megfelelő mennyiséget megvásárolható az Azure stack fizikai Processzormagok száma. 

A kapacitás számlázási szükséges egy nagyvállalati szerződés (EA) Azure-előfizetést a regisztrációhoz. A hiba oka, hogy regisztrációs beállítja elemek rendelkezésre állását a Marketplace-en, amely egy Azure-előfizetés szükséges. Az előfizetés nem használja az Azure Stack használatának.

## <a name="learn-more"></a>Részletek
- További információ a használati esetek, vásárlás, partnerek és OEM hardverszállítók: a [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) termékoldalán.
- Az ütemterv és a rendelkezésre állás földrajzi információ az Azure Stack integrált rendszerek, tekintse meg a: [Azure Stack: Azure-bővítmény](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- A Microsoft Azure Stack csomagolás és a díjszabással kapcsolatos további [töltse le a .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Adatközpont hálózati integráció](azure-stack-network.md)