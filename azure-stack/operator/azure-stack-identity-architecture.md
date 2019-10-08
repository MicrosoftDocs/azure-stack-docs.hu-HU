---
title: A Azure Stack identitás-architektúrája | Microsoft Docs
description: Ismerje meg a Azure Stack identitás-architektúráját, valamint az Azure AD és a AD FS közötti különbségeket.
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
ms.openlocfilehash: 364028183445df7e74828605439bfd7b3784f01f
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019406"
---
# <a name="identity-architecture-for-azure-stack"></a>Azure Stack identitás-architektúrája

Ha a Azure Stackhoz használandó identitás-szolgáltatót választ, meg kell ismernie az Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehetőségei közötti lényeges különbségeket.

## <a name="capabilities-and-limitations"></a>Képességek és korlátozások

A választott identitás-szolgáltató korlátozhatja a beállításokat, beleértve a több-bérlős támogatást is.

|Képesség vagy forgatókönyv        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Csatlakozik az internethez     |Igen       |Választható|
|Több-bérlős támogatás támogatása     |Igen       |Nem      |
|Ajánlati elemek a piactéren |Igen       |Igen (az [Offline Marketplace Syndication](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) eszköz használatát igényli)|
|Active Directory-hitelesítési tár támogatása (ADAL) |Igen |Igen|
|Támogatás olyan eszközökhöz, mint az Azure CLI, a Visual Studio és a PowerShell  |Igen |Igen|
|Egyszerű szolgáltatás létrehozása a Azure Portal     |Igen |Nem|
|Egyszerű szolgáltatások létrehozása tanúsítványokkal      |Igen |Igen|
|Egyszerű szolgáltatás létrehozása a Secrets (kulcsok) használatával    |Igen |Igen|
|Az alkalmazások használhatják a Graph szolgáltatást           |Igen |Nem|
|Az alkalmazások használhatják az Identitáskezelő szolgáltatást a bejelentkezéshez |Igen |Igen (az alkalmazásoknak a helyszíni AD FS-példányokkal való összevonása szükségesek) |

## <a name="topologies"></a>Topológiák

A következő fejezetek a különböző identitás-topológiákat tárgyalják.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: egybérlős topológia

Alapértelmezés szerint a Azure Stack telepítésekor és az Azure AD használatakor a Azure Stack egyetlen bérlős topológiát használ.

Az egybérlős topológia akkor hasznos, ha:
- Minden felhasználó ugyanahhoz a bérlőhöz tartozik.
- A szolgáltató Azure Stack példányt üzemeltet a szervezet számára.

![Egybérlős topológia Azure Stack az Azure AD-vel](media/azure-stack-identity-architecture/single-tenant.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- Azure Stack regisztrálja az összes alkalmazást és szolgáltatást ugyanahhoz az Azure AD-bérlői címtárhoz.
- Azure Stack csak a címtárban lévő felhasználókat és alkalmazásokat hitelesíti, beleértve a jogkivonatokat is.
- A rendszergazdák (felhőalapú operátorok) és a bérlői felhasználók identitása ugyanabban a címtár-bérlőben található.
- Ha engedélyezni szeretné, hogy egy felhasználó egy másik címtárból hozzáférhessen ehhez a Azure Stack-környezethez, meg kell [hívnia a felhasználót vendégként](azure-stack-identity-overview.md#guest-users) a bérlői címtárba.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: több-bérlős topológia

A Felhőbeli operátorok úgy konfigurálhatják Azure Stack, hogy a bérlők egy vagy több szervezettől hozzáférjenek az alkalmazásokhoz. A felhasználók a Azure Stack felhasználói portálon keresztül férhetnek hozzá az alkalmazásokhoz. Ebben a konfigurációban a felügyeleti portál (amelyet a felhőalapú operátor használ) egyetlen címtárban lévő felhasználókra korlátozódik.

A több-bérlős topológia akkor hasznos, ha:

- A szolgáltató lehetővé szeretné tenni, hogy több szervezet felhasználói hozzáférhessenek Azure Stackhoz.

![Több-bérlős topológia Azure Stack az Azure AD-vel](media/azure-stack-identity-architecture/multi-tenant.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- Az erőforrásokhoz való hozzáférésnek szervezeti alapon kell lennie.
- Az egyik szervezet felhasználói nem tudnak hozzáférést biztosítani az erőforrásokhoz a szervezeten kívüli felhasználók számára.
- A rendszergazdák identitásai (felhőalapú operátorok) a felhasználók identitása különálló címtár-bérlőn is lehetnek. Ez az elkülönítés az identitás-szolgáltató szintjén biztosítja a fiók elkülönítését.
 
### <a name="ad-fs"></a>AD FS

A AD FS topológiára akkor van szükség, ha az alábbi feltételek bármelyike teljesül:

- Azure Stack nem csatlakozik az internethez.
- Azure Stack csatlakozhat az internethez, de Ön úgy dönt, hogy a AD FS használja az identitás-szolgáltatóhoz.
  
![Topológia Azure Stack AD FS használatával](media/azure-stack-identity-architecture/adfs.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- A topológia éles környezetben való használatának támogatásához integrálnia kell a beépített Azure Stack AD FS példányt egy olyan meglévő AD FS-példánnyal, amelyet Active Directory egy összevonási megbízhatósági kapcsolaton keresztül támogat.
- A Graph szolgáltatást Azure Stack a meglévő Active Directory-példánnyal integrálhatja. Használhatja a OData-alapú Graph API szolgáltatást is, amely támogatja az Azure AD-Graph API konzisztens API-kat.

  A Active Directory-példánnyal való kommunikációhoz a Graph API felhasználói hitelesítő adatokat igényel az Active Directory-példánytól, amely csak olvasási jogosultsággal rendelkezik.
  - A beépített AD FS példány a Windows Server 2016-es verzióján alapul.
  - A AD FS-és Active Directory-példányok Windows Server 2012-es vagy újabb verzión alapulnak.
  
  A Active Directory-példány és a beépített AD FS-példány között az interakciók nem korlátozódnak az OpenID Connect használatára, és bármilyen kölcsönösen támogatott protokollt használhatnak.
  - A felhasználói fiókok létrehozása és kezelése a helyszíni Active Directory-példányban történik.
  - Az alkalmazások egyszerű szolgáltatásait és regisztrációit a beépített Active Directory-példány kezeli.

## <a name="next-steps"></a>További lépések

- [Az identitás áttekintése](azure-stack-identity-overview.md)
- [Adatközpont-integráció – identitás](azure-stack-integrate-identity.md)
