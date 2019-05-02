---
title: Csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stackben
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cbffe391c6295ede6c985f38a836198a1ffba84c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986201"
---
# <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Az erőforrások kezelése az Azure Stack Development Kit kell kapcsolódnia. Ez a cikk ismerteti a lépéseket a csomag való kapcsolódáshoz szükséges. A következő kapcsolati lehetőségek bármelyikét használhatja:

* A távoli asztal: lehetővé teszi, hogy a gyors elérését a csomag egyetlen egyidejű felhasználó.
* Virtuális magánhálózat (VPN): lehetővé teszi több egyidejű felhasználók, az Azure Stack-infrastruktúra (a konfiguráció szükséges hozzá) kívül az ügyfelek elérését.

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Csatlakozás az Azure Stack a távoli asztallal
Távoli asztali kapcsolattal több párhuzamos felhasználó is működjön a portállal-erőforrások kezeléséhez.

1. Nyissa meg a távoli asztali kapcsolatot, és csatlakozzon a development Kitet. Adja meg **AzureStack\AzureStackAdmin** a felhasználónevet, valamint az Azure Stack-telepítés során megadott rendszergazdai jelszót.  

2. A fejlesztői számítógépről kit, nyissa meg a Kiszolgálókezelőt, kattintson a **helyi kiszolgáló**, és kapcsolja ki az Internet Explorer fokozott biztonsági, majd zárja be a Kiszolgálókezelőt.

3. Lépjen a portál megnyitása (https://portal.local.azurestack.external/) , és jelentkezzen be a felhasználói hitelesítő adatokkal.


## <a name="connect-to-azure-stack-with-vpn"></a>Csatlakozás az Azure Stackhez VPN

Egy osztott alagút az Azure Stack Development Kit virtuális magánhálózati (VPN) kapcsolatot létesíthet. A VPN-kapcsolaton keresztül férhet hozzá a felügyeleti portálon, a felhasználói portálra, és eszközökkel, például az Azure Stack-erőforrások kezelése a Visual Studio és a PowerShell helyi telepítése. VPN-kapcsolat mindkét Azure aktív regisztrált alkalmazástulajdonost használata támogatott, és az Active Directory összevonási Services(AD FS)-alapú telepítések. VPN-kapcsolatok engedélyezése csatlakozni az Azure Stack egyszerre több ügyfélnek. 

> [!NOTE] 
> A VPN-kapcsolat nem biztosít az Azure Stack-infrastruktúra virtuális gépekhez való kapcsolódás. 

### <a name="prerequisites"></a>Előfeltételek

* Telepítés [Azure Stack-kompatibilis Azure PowerShell](../operator/azure-stack-powershell-install.md) a helyi számítógépen.  
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>VPN-kapcsolat konfigurálása

A csomagban található VPN-kapcsolat létrehozásához nyisson meg egy rendszergazda jogú PowerShell-munkamenetből a helyi Windows-alapú számítógépről, és futtassa a következő szkriptet (ügyeljen arra, hogy frissítse az IP-címmel és jelszóval értékeket környezete számára):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Ha a beállítás sikeres, az **azurestack** a VPN-kapcsolatok listájában.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Kapcsolódás az Azure Stack-példány, az alábbi két módszer egyikével:  

* Használatával a `Connect-AzsVpn` parancsot: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure Stack gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** alakzatot a helyi számítógép tanúsítványtárolójába. (a kérdés jelenhet meg a PowerShell-munkamenet ablakban mögött). 

* Nyissa meg a helyi számítógép **hálózati beállítások** > **VPN** > kattintson **azurestack** > **csatlakozás**. A bejelentkezési parancssorba írja be a felhasználónevet (AzureStack\AzureStackAdmin) és a jelszót.

### <a name="test-the-vpn-connectivity"></a>A VPN-kapcsolat tesztelése

A portál kapcsolat teszteléséhez, nyisson meg egy webböngészőt, és keresse meg a felhasználói portálra (https://portal.local.azurestack.external/), jelentkezzen be, és erőforrások létrehozásához.  

## <a name="next-steps"></a>További lépések



