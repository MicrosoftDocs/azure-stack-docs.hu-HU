---
title: Csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 8b7a9e58fd4d4d8c3a05fea60c79ff47a519bf8c
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197371"
---
# <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Az erőforrások kezelése az Azure Stack Development Kit kell kapcsolódnia. Ez a cikk ismerteti a lépéseket a csomag való kapcsolódáshoz szükséges. A következő kapcsolati lehetőségek bármelyikét használhatja:

* A távoli asztal: lehetővé teszi, hogy a gyors elérését a csomag egyetlen egyidejű felhasználó.
* Virtuális magánhálózat (VPN): lehetővé teszi több egyidejű felhasználók, az Azure Stack-infrastruktúra (a konfiguráció szükséges hozzá) kívül az ügyfelek elérését.

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Csatlakozás az Azure Stack a távoli asztallal
A távoli asztali kapcsolat több párhuzamos felhasználó is működjön a portállal-erőforrások kezeléséhez.

1. Nyissa meg a távoli asztali kapcsolatot, és csatlakozzon a development Kitet. Adja meg **AzureStack\AzureStackAdmin** a felhasználónevet, valamint az Azure Stack-telepítés során megadott rendszergazdai jelszót.  

2. A fejlesztői számítógépről kit, nyissa meg a Kiszolgálókezelőt, kattintson a **helyi kiszolgáló**, és kapcsolja ki az Internet Explorer fokozott biztonsági, majd zárja be a Kiszolgálókezelőt.

3. Nyissa meg a portálon, lépjen a (https://portal.local.azurestack.external/) , és jelentkezzen be a felhasználói hitelesítő adatokkal.


## <a name="connect-to-azure-stack-with-vpn"></a>Csatlakozás az Azure Stackhez VPN

Egy VPN-kapcsolat az Azure Stack Development Kit osztott alagút is létrehozhat. A VPN-kapcsolaton keresztül elérheti a felügyeleti portálon, a felhasználói portál és a helyileg telepített eszközök a Visual Studio és a PowerShell használatával az Azure Stack-erőforrások kezeléséhez. VPN-kapcsolat használata támogatott az Azure Active Directory (AAD) és az Active Directory összevonási szolgáltatások (AD FS)-alapú telepítések. VPN-kapcsolatok lehetővé teszik, hogy csatlakozzon az Azure Stackhez egyszerre több ügyfélnek. 

> [!NOTE] 
> A VPN-kapcsolat Azure Stack-infrastruktúra virtuális gépekhez való kapcsolódás nem biztosítanak. 

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

Ha a telepítés sikeres, látni fogja `azurestack` a VPN-kapcsolatok listájában.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Kapcsolódás az Azure Stack-példány, az alábbi két módszer egyikével:  

* Használatával a `Connect-AzsVpn` parancsot: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure Stack gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** alakzatot a helyi számítógép tanúsítványtárolójába. A rendszer kéri a PowerShell-munkamenet ablakban mögött jelenhet meg. 

* A helyi számítógépen nyissa meg **hálózati beállítások** > **VPN** > Válasszon `azurestack`  >  **csatlakozás**. A bejelentkezési parancssorba írja be a felhasználónevet (AzureStack\AzureStackAdmin) és a jelszót.

### <a name="test-the-vpn-connectivity"></a>A VPN-kapcsolat tesztelése

A portál kapcsolat teszteléséhez, nyisson meg egy böngészőt, és nyissa meg a felhasználói portált (https://portal.local.azurestack.external/), és jelentkezzen be, majd az erőforrások létrehozása.  

## <a name="next-steps"></a>További lépések



