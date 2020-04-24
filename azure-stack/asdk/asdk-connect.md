---
title: Kapcsolódás a ASDK
description: Megtudhatja, hogyan csatlakozhat a Azure Stack Development Kithoz (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 1b562d2a72f3da4d4ac9ef7045f5cbd5408f4afa
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79294428"
---
# <a name="connect-to-the-asdk"></a>Kapcsolódás a ASDK

Az erőforrások kezeléséhez először csatlakoznia kell a Azure Stack Development Kithoz (ASDK). Ebben a cikkben azokat a lépéseket ismertetjük, amelyeket a ASDK való kapcsolódáshoz a következő kapcsolódási lehetőségek használatával végez:

* [Távoli asztali kapcsolat (RDP)](#connect-with-rdp): Ha távoli asztali kapcsolat használatával csatlakozik, egyetlen felhasználó gyorsan CSATLAKOZHAT a ASDK.
* [Virtuális magánhálózat (VPN)](#connect-with-vpn): Ha VPN-kapcsolaton keresztül csatlakozik, több felhasználó is csatlakozhat a Azure stack-portálokhoz az Azure stack infrastruktúrán kívüli ügyfelektől. A VPN-kapcsolathoz némi telepítés szükséges.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Kapcsolódás Azure Stack RDP használatával

Egyetlen egyidejű felhasználó a Azure Stack felügyeleti portálon vagy a felhasználói portálon keresztül felügyelheti az erőforrásokat közvetlenül a ASDK-gazdagépről Távoli asztali kapcsolat.

> [!TIP]
> Ez a beállítás azt is lehetővé teszi, hogy az RDP-t újra használhatja, miközben bejelentkezett a ASDK gazdagépre, hogy bejelentkezzen a ASDK-gazdagépen létrehozott virtuális gépekre (VM).

1. Nyissa meg Távoli asztali kapcsolat (mstc. exe), és kapcsolódjon a ASDK állomás számítógépének IP-címéhez. Győződjön meg arról, hogy olyan fiókot használ, amely a ASDK-gazdaszámítógépre való távoli bejelentkezéshez van hitelesítve. Alapértelmezés szerint a **AzureStack\AzureStackAdmin** rendelkezik a ASDK gazdagép számítógépéhez való távoli hozzáféréshez szükséges engedélyekkel.  

2. A ASDK-gazdagépen nyissa meg a Kiszolgálókezelő alkalmazást (ServerManager. exe). Válassza a **helyi kiszolgáló**lehetőséget, kapcsolja ki az **Internet Explorer fokozott biztonsági beállításait**, majd a Kiszolgálókezelő bezárását.

3. Jelentkezzen be a felügyeleti portálra **AzureStack\CloudAdmin** , vagy használjon más Azure stack kezelői hitelesítő adatokat. A ASDK felügyeleti portál címe: [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Jelentkezzen be a felhasználói portálra **AzureStack\CloudAdmin** , vagy használjon más Azure stack felhasználói hitelesítő adatokat. A ASDK felhasználói portáljának címe [https://portal.local.azurestack.external](https://portal.local.azurestack.external):.

> [!NOTE]
> Ha további információt szeretne arról, hogy mikor kell használni a fiókot, tekintse meg a [ASDK-adminisztrátor alapjai](asdk-admin-basics.md#what-account-should-i-use)című témakört.

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Csatlakozás Azure Stack VPN használatával

Az Azure Stack-portálok és a helyileg telepített eszközök, például a Visual Studio és a PowerShell eléréséhez létrehozhat egy felosztott bújtatási VPN-kapcsolatot egy ASDK-gazdagéphez. VPN-kapcsolatok használata esetén több felhasználó is csatlakozhat egyszerre a ASDK által üzemeltetett erőforrások Azure Stackéhez.

A VPN-kapcsolat az Azure AD és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) üzemelő példányok esetében is támogatott.

> [!NOTE]
> A VPN-kapcsolat *nem* biztosít kapcsolatot Azure stack virtuális gépekhez. VPN-kapcsolaton keresztül nem lehet RDP-Azure Stack virtuális gépekre csatlakozni.

### <a name="prerequisites"></a>Előfeltételek
A VPN-kapcsolat ASDK való beállítása előtt győződjön meg arról, hogy teljesítette a következő előfeltételeket:

- Telepítse a [Azure stack-kompatibilis Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) a helyi számítógépre.  
- Töltse le a [Azure stack használatához szükséges eszközöket](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>VPN-kapcsolat beállítása

Ha VPN-kapcsolat létesítését szeretné létrehozni a ASDK, nyissa meg a PowerShellt rendszergazdaként a helyi Windows-alapú számítógépen. Ezután futtassa a következő szkriptet (frissítse a környezete IP-címét és a jelszó értékeit):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the ASDK host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

Ha a telepítés sikeres, **Azure stack** jelenik meg a VPN-kapcsolatok listájában:

![Hálózati kapcsolatok](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

  Kapcsolódjon a Azure Stack-példányhoz az alábbi módszerek egyikének használatával:  

  * Használja a `Connect-AzsVpn` parancsot:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * A helyi számítógépen válassza a **hálózati beállítások** > **VPN** > **Azure stack** > a**Csatlakozás**lehetőséget. A bejelentkezési kérésben adja meg a felhasználónevet (**AzureStack\AzureStackAdmin**) és a jelszavát.

Amikor először csatlakozik, a rendszer arra kéri, hogy telepítse a Azure Stack főtanúsítványt a **AzureStackCertificateAuthority** a helyi számítógép tanúsítványtárolójában. Ez a lépés hozzáadja a ASDK-hitelesítésszolgáltatót (CA) a megbízható gazdagépek listájához. A tanúsítvány telepítéséhez kattintson az **Igen** gombra.

![Főtanúsítvány](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Előfordulhat, hogy a promptot a PowerShell-ablak vagy más alkalmazások eltakarják.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

A portál-kapcsolatok teszteléséhez nyisson meg egy böngészőt, és lépjen a felhasználói portálra (https://portal.local.azurestack.external/) vagy a felügyeleti portálrahttps://adminportal.local.azurestack.external/)).

Az erőforrások létrehozásához és kezeléséhez jelentkezzen be a megfelelő előfizetési hitelesítő adatokkal.  

## <a name="next-steps"></a>További lépések

[Hibaelhárítás](asdk-troubleshooting.md)
