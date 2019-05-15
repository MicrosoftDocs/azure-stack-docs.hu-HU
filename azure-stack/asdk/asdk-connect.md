---
title: Csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a ASDK.
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
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 39814c48af0a362bd62f7d7276a32d886628317b
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617586"
---
# <a name="connect-to-the-asdk"></a>Csatlakozás a ASDK

Erőforrások kezelése, először csatlakoznia kell a az Azure Stack Development Kit (ASDK). Ez a cikk a szükséges lépéseket a következő kapcsolati lehetőségek használatával csatlakozni a ASDK is ismertetünk:

* [A távoli asztali kapcsolat (RDP)](#connect-with-rdp). Ha távoli asztali kapcsolattal csatlakozik, egyetlen felhasználó gyorsan csatlakozhatnak a development Kitet.
* [Virtuális magánhálózat (VPN)](#connect-with-vpn). Ha egy VPN-en keresztül csatlakozik, több felhasználó egyidejűleg csatlakozhat az Azure Stack portálon kívül az Azure Stack-infrastruktúra-ügyfelekről. Egy VPN-kapcsolat néhány beállítás szükséges.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Csatlakozás az Azure Stack RDP-vel

Több párhuzamos felhasználó kezelhetik az erőforrásokat az Azure Stack felügyeleti portálon vagy a felhasználói portál a távoli asztali kapcsolaton keresztül közvetlenül az ASDK állomásról. 

> [!TIP]
> Ez a beállítás engedélyezi, hogy RDP újra amíg a ASDK számítógép be van jelentkezve jelentkezzen be a ASDK gazdagépen létrehozott virtuális gépeket. 

1. Nyissa meg a távoli asztali kapcsolat (mstc.exe), és a fejlesztői csomag gazdagép számítógép IP-címét egy olyan fiókkal jelentkezzen be távolról a ASDK számítógép jogosult csatlakozni. Alapértelmezés szerint **AzureStack\AzureStackAdmin** a ASDK gazdagépnek a távoli engedélyekkel rendelkezik.  

2. Az állomáson development kit nyissa meg a Kiszolgálókezelőt (ServerManager.exe). Válassza ki **helyi kiszolgáló**, kapcsolja ki a **Internet Explorer fokozott biztonsági beállításai**, és zárja be a Kiszolgálókezelőt.

3. Jelentkezzen be a felügyeleti portált **AzureStack\CloudAdmin** vagy más Azure Stack-operátorokról hitelesítő adatokat használja. A felügyeleti portál ASDK cím [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Jelentkezzen be a felhasználói portálra, **AzureStack\CloudAdmin** vagy más Azure Stack felhasználói hitelesítő adatokat használja. A felhasználói portál ASDK cím [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Melyik fiók használatával kapcsolatos további információkért lásd: [ASDK Adminisztráció alapjai](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Csatlakozás az Azure Stackhez VPN segítségével

Egy osztott alagút VPN-kapcsolat egy ASDK gazdaszámítógép eléréséhez az Azure Stack-portálok és a helyileg telepített eszközök, mint például a Visual Studio és a PowerShell használatával is létrehozhat. VPN-kapcsolatok révén több felhasználó egyidejűleg a ASDK által üzemeltetett Azure Stack-erőforrások csatlakozhatnak.

VPN-kapcsolat használata támogatott, mind az Azure ad és az Active Directory összevonási szolgáltatások (AD FS) központi telepítések.

> [!NOTE]
> Egy VPN-kapcsolat *nem* Azure Stack virtuális gépek kapcsolatának biztosítása. Nem lesz képes a VPN-en keresztül csatlakozik az Azure Stack virtuális gépek távoli asztali eléréséhez.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt beállítaná a VPN-kapcsolat a ASDK, győződjön meg arról, az alábbi előfeltételek teljesülnek.

- Telepítés [Azure Stack-kompatibilis Azure PowerShell-lel](asdk-post-deploy.md#install-azure-stack-powershell) a helyi számítógépen.  
- Töltse le a [az Azure Stack működéséhez szükséges eszközök](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>VPN-kapcsolat beállítása

A ASDK VPN-kapcsolat létrehozásához nyissa meg a Powershellt rendszergazdaként azon a helyi Windows-alapú számítógép. Ezután futtassa a következő szkriptet (frissítés a környezet értékei az IP-cím és jelszó):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Ha a telepítés sikeres, **Azure Stack** a VPN-kapcsolatok listájában jelenik meg.

![Hálózati kapcsolatok](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

  Kapcsolódás az Azure Stack-példány a következő módszerek egyikével:  

  * Használja a `Connect-AzsVpn` parancsot:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * Válassza ki a helyi számítógépen **hálózati beállítások** > **VPN** > **Azure Stack** > **csatlakoztatása**. A bejelentkezési parancssorba írja be a felhasználónevet (**AzureStack\AzureStackAdmin**) és a jelszót.

Az első csatlakozás alkalmával a kéri az Azure Stack főtanúsítványának a telepítendő **AzureStackCertificateAuthority** a helyi számítógép tanúsítványtárolójában. Ebben a lépésben ASDK hitelesítésszolgáltató (CA) hozzáadása a megbízható gazdagépek listájához. Kattintson a **Igen** a tanúsítvány telepítéséhez.

![Főtanúsítvány](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > A rendszer kéri a PowerShell-ablakot, és más alkalmazásokkal ablakméreteitől függően.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

A portál kapcsolat teszteléséhez, nyisson meg egy webböngészőt, és keresse meg a felhasználói portálra (https://portal.local.azurestack.external/) vagy a felügyeleti portálon (https://adminportal.local.azurestack.external/). 

Jelentkezzen be a megfelelő előfizetést hitelesítő adataival az erőforrások létrehozásához és kezeléséhez.  

## <a name="next-steps"></a>További lépések

[Hibaelhárítás](asdk-troubleshooting.md)
