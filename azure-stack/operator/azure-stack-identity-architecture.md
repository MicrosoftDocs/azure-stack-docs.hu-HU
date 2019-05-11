---
title: Identitásarchitektúra az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg az identitás architektúra is használhatja az Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: e09451c1bf49a2de075388f555cb7b80e70db63f
ms.sourcegitcommit: 426380a3a27954cd609ba52d1066d9d69f5267fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65532320"
---
# <a name="identity-architecture-for-azure-stack"></a>Identitásarchitektúra az Azure Stackhez

Ha az Azure Stack használata Identitásszolgáltatóként, tisztában kell lennie a fontos különbségek a beállításokat az Azure Active Directory (Azure AD) és az Active Directory összevonási szolgáltatások (AD FS) között.

## <a name="capabilities-and-limitations"></a>Képességei és korlátozásai 
Az Ön által választott identitásszolgáltató korlátozhatja a beállításokat, beleértve a több-bérlős támogatás. 

  

|Funkció vagy forgatókönyv        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Csatlakozik az internethez     |Igen       |Választható|
|Több-bérlős támogatás     |Igen       |Nem      |
|A piactéren az ajánlat elemek |Igen       |Igen. Használatát igényli a [offline Marketplace szindikálási](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) eszközt.|
|Az Active Directory Authentication Library (ADAL) támogatása |Igen |Igen|
|Azure CLI-vel, a Visual Studio és a PowerShell hálózatdiagnosztikai eszközök támogatása  |Igen |Igen|
|Az Azure Portalon keresztül szolgáltatásnevek létrehozása     |Igen |Nem|
|Szolgáltatásnevek létrehozása tanúsítványokkal      |Igen |Igen|
|Szolgáltatásnevek létrehozása titkos kulcsok (kulcsok)    |Igen |Igen|
|Alkalmazások használhatják a Graph szolgáltatás           |Igen |Nem|
|Alkalmazások identitásszolgáltató használhatják a bejelentkezéshez |Igen |Igen. Szükséges összevonni a helyszíni alkalmazások az AD FS-példányok. |

## <a name="topologies"></a>Topológiák
A következő részekben bemutatjuk a különböző identitás topológiákat, amelyet használhat.

### <a name="azure-ad-single-tenant-topology"></a>Az Azure AD: egybérlős topológia 
Alapértelmezés szerint az Azure Stack telepítése és használata az Azure Active Directory, Azure Stack használja egy egybérlős topológiát. 

Egy egybérlős topológia a következő esetekben hasznos:
- Minden felhasználó ugyanahhoz a bérlőhöz tartoznak.
- A szolgáltató egy szervezet az Azure Stack példányt üzemeltetnek. 

![Az Azure Stack egybérlős topológia az Azure ad-vel](media/azure-stack-identity-architecture/single-tenant.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Az Azure Stack regisztrálja minden alkalmazást, és ugyanazt az Azure AD-szolgáltatások directory bérlővel. 
- Az Azure Stack hitelesíti, csak a felhasználók és az alkalmazások a könyvtárban, beleértve a jogkivonatokat. 
- Rendszergazdák (a felhő üzemeltetői) és a bérlői felhasználók identitásainak ugyanahhoz a directory-bérlőhöz vannak. 
- Ahhoz, hogy egy felhasználó egy másik címtárból az Azure Stack-környezet elérésére, kell [meghívása a felhasználó vendégként](azure-stack-identity-overview.md#guest-users) , a bérlő címtárát. 

### <a name="azure-ad-multi-tenant-topology"></a>Az Azure AD: több-bérlős topológia
Felhő üzemeltetői konfigurálhatja egy vagy több szervezet bérlők által az alkalmazásoknak hozzáférést az Azure Stack. Felhasználók a felhasználói portálon keresztül férhetnek hozzá az alkalmazásokhoz. Ebben a konfigurációban a felügyeleti portálon (a felhő üzemeltetője által használt) korlátozódik a felhasználók számára egy egy címtárból. 

Egy több-bérlős topológia a következő esetekben hasznos:
- A szolgáltató szeretné, hogy a felhasználók több szervezet az Azure Stack eléréséhez.

![Az Azure Stack több-bérlős topológia az Azure ad-vel](media/azure-stack-identity-architecture/multi-tenant.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Erőforrásokhoz való hozzáférés a szervezet alapon kell lennie. 
- Lehet, hogy egy szervezet felhasználóitól származó nem lehet a szervezeten kívüli felhasználók erőforrásokhoz való hozzáférést. 
- A felhasználók identitásainak külön címtárbérlő identitások a rendszergazdák számára (a felhő üzemeltetői) is lehet. Ez a fajta elkülönítés el vannak különítve fiók az identity provider szintjén. 
 
### <a name="ad-fs"></a>AD FS  
Az AD FS-topológia kötelező megadni, ha a következő feltételek valamelyike teljesül:
- Az Azure Stack nem csatlakozik az internethez.
- Az Azure Stack csatlakozni az internethez, de úgy dönt, hogy az AD FS használata az identitásszolgáltató.
  
![Az AD FS az Azure Stack-topológia](media/azure-stack-identity-architecture/adfs.png)

Ez a topológia szolgáltatások a következő jellemzőkkel:
- Éles környezetben ez a topológia támogatása, integrálnia kell az Azure Stack az AD FS beépített példányt egy meglévő AD FS-példánnyal alapját az Active Directory összevonási megbízhatósági keresztül. 
- A Graph szolgáltatás az Azure Stackben integrálható a meglévő Active Directory-példányból. Az OData-alapú Graph API szolgáltatás, amely támogatja az API-k konzisztensek legyenek az Azure AD Graph API is használható. 

  A Graph API használatához az Active Directory-példánnyal, megköveteli a felhasználói hitelesítő adatokat az Active Directory-példányból, amelyek csak olvasható engedélyek. 
  - A beépített AD FS-példányt a Windows Server 2016-on alapul. 
  - Az AD FS és az Active Directory-példányok Windows Server 2012 vagy újabb rendszeren kell alapulniuk. 
  
  Az Active Directory-példányból, és a beépített AD FS-példány között interakciók nem korlátozott, az OpenID Connect, és bármely kölcsönösen támogatott protokollt használhatják. 
  - Felhasználói fiókok létrehozása és kezelése a helyszíni Active Directory-példányában.
  - Az egyszerű szolgáltatások és alkalmazások regisztrációját kezeli a beépített Active Directory-példányban.



## <a name="next-steps"></a>További lépések
- [Az identitások áttekintése](azure-stack-identity-overview.md)   
- [Adatközpont integrációja - identitás](azure-stack-integrate-identity.md)
