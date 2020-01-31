---
title: Azure Stack hub identitás-architektúrája
description: Ismerje meg az Azure Stack hub identitás-architektúráját, valamint az Azure AD és a AD FS közötti különbségeket.
author: ihenkel
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: a3846deb266b610a1f09b32df549e49c88f19b76
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890306"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Azure Stack hub identitás-architektúrája

Azure Stack hub-hoz használandó identitás-szolgáltató kiválasztásakor ismernie kell az Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehetőségei közötti lényeges különbségeket.

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

Alapértelmezés szerint a Azure Stack hub telepítésekor és az Azure AD használatakor a Azure Stack hub egy egybérlős topológiát használ.

Az egybérlős topológia akkor hasznos, ha:
- Minden felhasználó ugyanahhoz a bérlőhöz tartozik.
- A szolgáltató Azure Stack hub-példányt üzemeltet a szervezet számára.

![Azure Stack hub egybérlős topológiája az Azure AD-vel](media/azure-stack-identity-architecture/single-tenant.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- Azure Stack hub az összes alkalmazást és szolgáltatást ugyanahhoz az Azure AD-bérlői címtárhoz regisztrálja.
- Azure Stack hub csak az adott könyvtárból származó felhasználókat és alkalmazásokat hitelesíti, beleértve a jogkivonatokat is.
- A rendszergazdák (felhőalapú operátorok) és a bérlői felhasználók identitása ugyanabban a címtár-bérlőben található.
- Ha engedélyezni szeretné, hogy egy másik címtár felhasználója hozzáférhessen ehhez az Azure Stack hub-környezethez, meg kell [hívnia a felhasználót vendégként](azure-stack-identity-overview.md#guest-users) a bérlői címtárba.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: több-bérlős topológia

A Felhőbeli operátorok úgy konfigurálhatják Azure Stack hubot, hogy a bérlők egy vagy több szervezettől hozzáférjenek az alkalmazásokhoz. A felhasználók az Azure Stack hub felhasználói portálon keresztül férhetnek hozzá az alkalmazásokhoz. Ebben a konfigurációban a felügyeleti portál (amelyet a felhőalapú operátor használ) egyetlen címtárban lévő felhasználókra korlátozódik.

A több-bérlős topológia akkor hasznos, ha:

- A szolgáltató engedélyezni szeretné, hogy több szervezet felhasználói hozzáférhessenek Azure Stack hubhoz.

![Azure Stack hub több-bérlős topológiája az Azure AD-vel](media/azure-stack-identity-architecture/multi-tenant.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- Az erőforrásokhoz való hozzáférésnek szervezeti alapon kell lennie.
- Az egyik szervezet felhasználói nem tudnak hozzáférést biztosítani az erőforrásokhoz a szervezeten kívüli felhasználók számára.
- A rendszergazdák identitásai (felhőalapú operátorok) a felhasználók identitása különálló címtár-bérlőn is lehetnek. Ez az elkülönítés az identitás-szolgáltató szintjén biztosítja a fiók elkülönítését.
 
### <a name="ad-fs"></a>AD FS

A AD FS topológiára akkor van szükség, ha az alábbi feltételek bármelyike teljesül:

- Azure Stack hub nem csatlakozik az internethez.
- Azure Stack hub csatlakozni tud az internethez, de Ön úgy dönt, hogy a AD FS használja az identitás-szolgáltatóhoz.
  
![Azure Stack hub-topológia AD FS használatával](media/azure-stack-identity-architecture/adfs.png)

Ez a topológia a következő jellemzőkkel rendelkezik:

- Ahhoz, hogy támogassa ennek a topológiának a használatát az éles környezetben, integrálnia kell a beépített Azure Stack hub AD FS példányt egy olyan meglévő AD FS-példánnyal, amelyet Active Directory egy összevonási megbízhatósági kapcsolaton keresztül támogat.
- A Graph szolgáltatást Azure Stack hubhoz integrálhatja meglévő Active Directory-példánnyal. Használhatja a OData-alapú Graph API szolgáltatást is, amely támogatja az Azure AD-Graph API konzisztens API-kat.

  A Active Directory-példánnyal való kommunikációhoz a Graph API felhasználói hitelesítő adatokat igényel az Active Directory-példánytól, amely csak olvasási jogosultsággal rendelkezik.
  - A beépített AD FS példány a Windows Server 2016-es verzióján alapul.
  - A AD FS-és Active Directory-példányok Windows Server 2012-es vagy újabb verzión alapulnak.
  
  A Active Directory-példány és a beépített AD FS-példány között az interakciók nem korlátozódnak az OpenID Connect használatára, és bármilyen kölcsönösen támogatott protokollt használhatnak.
  - A felhasználói fiókok létrehozása és kezelése a helyszíni Active Directory-példányban történik.
  - Az alkalmazások egyszerű szolgáltatásait és regisztrációit a beépített Active Directory-példány kezeli.

## <a name="next-steps"></a>Következő lépések

- [Az identitás áttekintése](azure-stack-identity-overview.md)
- [Adatközpont-integráció – identitás](azure-stack-integrate-identity.md)
