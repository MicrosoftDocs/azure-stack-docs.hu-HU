---
title: Kapcsolódás Azure Stackhoz | Microsoft Docs
description: További információ a Azure Stack összekapcsolásáról.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6ccb86bd3ef0ded9126e68f53d87282e505b416e
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277040"
---
# <a name="connect-to-azure-stack"></a>Kapcsolódás az Azure Stackhez

Az erőforrások kezeléséhez kapcsolódnia kell a Azure Stack Development Kithoz. Ez a cikk a fejlesztői csomaghoz való kapcsolódáshoz szükséges lépéseket ismerteti. A következő kapcsolatok közül választhat:

* Távoli asztal: lehetővé teszi, hogy egyetlen egyidejű felhasználó gyorsan kapcsolódjon a fejlesztői készletből.
* Virtuális magánhálózat (VPN): lehetővé teszi, hogy több egyidejű felhasználó kapcsolódjon az Azure Stack-infrastruktúrán kívüli ügyfelektől (konfigurációt igényel).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Kapcsolódás Azure Stackhoz Távoli asztal
Egy Távoli asztali kapcsolat egyetlen egyidejű felhasználó együttműködik a portálon az erőforrások kezeléséhez.

1. Nyisson meg egy Távoli asztali kapcsolat, és kapcsolódjon a fejlesztői csomaghoz. Adja meg a **AzureStack\AzureStackAdmin** nevet a felhasználónévként, valamint a Azure stack beállítása során megadott rendszergazdai jelszót.  

2. A fejlesztői készlet számítógépén nyissa meg a Kiszolgálókezelő eszközt, kattintson a **helyi kiszolgáló**elemre, kapcsolja ki az Internet Explorer fokozott biztonsági szolgáltatását, majd a Kiszolgálókezelő elemet.

3. A portál megnyitásához nyissa meg a (https://portal.local.azurestack.external/), és jelentkezzen be felhasználói hitelesítő adatok használatával.


## <a name="connect-to-azure-stack-with-vpn"></a>Csatlakozás Azure Stack VPN-kapcsolattal

Létrehozhat egy megosztott bújtatási VPN-kapcsolatot egy Azure Stack Development Kit. A VPN-kapcsolaton keresztül elérheti a felügyeleti portált, a felhasználói portált és a helyileg telepített eszközöket, például a Visual studiót és a PowerShellt Azure Stack erőforrások kezeléséhez. A VPN-kapcsolat a Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) alapú üzemelő példányok esetében is támogatott. A VPN-kapcsolatok lehetővé teszik, hogy több ügyfél egyszerre kapcsolódjon Azure Stackhoz. 

> [!NOTE] 
> Ez a VPN-kapcsolat nem biztosít kapcsolatot Azure Stack infrastruktúra virtuális gépei számára. 

### <a name="prerequisites"></a>Előfeltételek

* Telepítse [Azure stack kompatibilis Azure PowerShell](../operator/azure-stack-powershell-install.md) a helyi számítógépre.  
* Töltse le a [Azure stack használatához szükséges eszközöket](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>VPN-kapcsolat konfigurálása

Ha VPN-kapcsolatot szeretne létrehozni a fejlesztői csomaggal, nyisson meg egy emelt szintű PowerShell-munkamenetet a helyi Windows-alapú számítógépről, és futtassa a következő parancsfájlt (Ügyeljen arra, hogy frissítse a környezet IP-címét és a jelszó értékeit):

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

Ha a telepítés sikeres, a VPN-kapcsolatok listájában `azurestack` jelenik meg.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Kapcsolódás az Azure Stackhez

Kapcsolódjon a Azure Stack-példányhoz a következő két módszer valamelyikével:  

* A `Connect-AzsVpn` parancs használatával: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Ha a rendszer kéri, bízza a Azure Stack gazdagépre, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** -ből a helyi számítógép tanúsítványtárolóba. Előfordulhat, hogy a parancssor a PowerShell-munkamenet ablaka mögött jelenik meg. 

* A helyi számítógépen lépjen a **hálózati beállítások** > **VPN** > válassza a `azurestack` @ no__t-4**Csatlakozás**lehetőséget. A bejelentkezési kérésben adja meg a felhasználónevet (AzureStack\AzureStackAdmin) és a jelszót.

### <a name="test-the-vpn-connectivity"></a>A VPN-kapcsolat tesztelése

A portál-kapcsolatok teszteléséhez nyisson meg egy böngészőt, és nyissa meg a felhasználói portált (https://portal.local.azurestack.external/), jelentkezzen be, majd hozzon létre erőforrásokat.  

## <a name="next-steps"></a>Következő lépések



