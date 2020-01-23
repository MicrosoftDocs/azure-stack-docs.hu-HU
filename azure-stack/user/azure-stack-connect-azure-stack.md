---
title: Kapcsolódás Azure Stack hubhoz | Microsoft Docs
description: További információ a Azure Stack hub összekapcsolásáról.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cb6a1e99def82625d01864601c504b62425b7a5f
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535892"
---
# <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

Az erőforrások kezeléséhez kapcsolódnia kell a Azure Stack Development Kithoz. Ez a cikk a fejlesztői csomaghoz való kapcsolódáshoz szükséges lépéseket ismerteti. A következő kapcsolatok közül választhat:

* Távoli asztal: lehetővé teszi, hogy egyetlen egyidejű felhasználó gyorsan kapcsolódjon a fejlesztői készletből.
* Virtuális magánhálózat (VPN): lehetővé teszi több egyidejű felhasználó csatlakozását az Azure Stack hub-infrastruktúrán kívüli ügyfelektől (konfigurációt igényel).

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>Kapcsolódás Azure Stack hubhoz Távoli asztal
Egy Távoli asztali kapcsolat egyetlen egyidejű felhasználó együttműködik a portálon az erőforrások kezeléséhez.

1. Nyisson meg egy Távoli asztali kapcsolat, és kapcsolódjon a fejlesztői csomaghoz. Adja meg a **AzureStack\AzureStackAdmin** nevet és a Azure stack hub telepítésekor megadott rendszergazdai jelszót.  

2. A fejlesztői készlet számítógépén nyissa meg a Kiszolgálókezelő eszközt, kattintson a **helyi kiszolgáló**elemre, kapcsolja ki az Internet Explorer fokozott biztonsági szolgáltatását, majd a Kiszolgálókezelő elemet.

3. A portál megnyitásához nyissa meg a (https://portal.local.azurestack.external/) és jelentkezzen be a felhasználói hitelesítő adatok használatával.


## <a name="connect-to-azure-stack-hub-with-vpn"></a>Csatlakozás Azure Stack hubhoz VPN-vel

Létrehozhat egy megosztott bújtatási VPN-kapcsolatot egy Azure Stack Development Kit. A VPN-kapcsolaton keresztül elérheti a felügyeleti portált, a felhasználói portált és a helyileg telepített eszközöket, például a Visual studiót és a PowerShellt Azure Stack hub-erőforrások kezeléséhez. A VPN-kapcsolat a Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) alapú üzemelő példányok esetében is támogatott. A VPN-kapcsolatok lehetővé teszik, hogy egyszerre több ügyfél kapcsolódjon Azure Stack hubhoz. 

> [!NOTE] 
> Ez a VPN-kapcsolat nem biztosít kapcsolatot Azure Stack hub-infrastruktúra virtuális gépei számára. 

### <a name="prerequisites"></a>Előfeltételek

* Telepítse [Azure stack hub-kompatibilis Azure PowerShell](../operator/azure-stack-powershell-install.md) a helyi számítógépre.  
* Töltse le az [Azure stack hub használatához szükséges eszközöket](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>VPN-kapcsolat konfigurálása

Ha VPN-kapcsolatot szeretne létrehozni a fejlesztői csomaggal, nyisson meg egy emelt szintű PowerShell-munkamenetet a helyi Windows-alapú számítógépről, és futtassa a következő parancsfájlt (Ügyeljen arra, hogy frissítse a környezet IP-címét és a jelszó értékeit):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
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

Ha a telepítés sikeres, a `azurestack` jelenik meg a VPN-kapcsolatok listájában.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

Kapcsolódjon az Azure Stack hub-példányhoz a következő két módszer egyikével:  

* Az `Connect-AzsVpn` parancs használatával: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, bízza a Azure Stack hub-gazdagépet, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** -ből a helyi számítógép tanúsítványtárolóba. Előfordulhat, hogy a parancssor a PowerShell-munkamenet ablaka mögött jelenik meg. 

* A helyi számítógépen nyissa meg a **hálózati beállítások** > **VPN** > válassza a `azurestack` > **Kapcsolódás**lehetőséget. A bejelentkezési kérésben adja meg a felhasználónevet (AzureStack\AzureStackAdmin) és a jelszót.

### <a name="test-the-vpn-connectivity"></a>A VPN-kapcsolat tesztelése

A portál-kapcsolatok teszteléséhez nyisson meg egy böngészőt, és nyissa meg a felhasználói portált (https://portal.local.azurestack.external/), jelentkezzen be, majd hozzon létre erőforrásokat.  

## <a name="next-steps"></a>Következő lépések



