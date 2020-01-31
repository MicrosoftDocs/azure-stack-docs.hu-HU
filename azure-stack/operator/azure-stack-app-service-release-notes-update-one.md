---
title: App Service Azure Stack hub Update 1 kibocsátási megjegyzései
description: Ismerkedjen meg az Azure Stack hub App Service Update 1. frissítésének javításokkal, javításokkal és ismert problémákkal.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: d81c0387e26a72865b74a88062c5b7b7588b3281
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875963"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>App Service Azure Stack hub Update 1 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack hub Update 1 verziójának javításait, javításait és ismert problémáit. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza az 1802-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service telepítése előtt.

## <a name="build-reference"></a>Build referenciája

Az Azure Stack hub Update 1 Build számának App Service **69.0.13698.9**.

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure App Service Azure Stack hub-beli új központi telepítései mostantól egy [három tárgyú helyettesítő karaktert](azure-stack-app-service-before-you-get-started.md#get-certificates) igényelnek, mivel a kudu-hez tartozó SSO-t a Azure app Service-ben kezelik. Az új tárgy **\*. SSO. appservice.\<régió\>.\<tartománynév\>.\<bővítmény\>**

Az üzembe helyezés megkezdése előtt tekintse át a [app Service telepítésének Előfeltételeit Azure stack hub-on](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub 1. frissítésének Azure App Service a következő javításokat és javításokat tartalmazza:

- A **Azure app Service magas rendelkezésre állása** – az Azure Stack hub 1802 frissítés engedélyezve munkaterhelések a tartalék tartományok között telepíthetők, ami lehetővé teszi app Service infrastruktúra hibatűrővé tételét, mivel azok a tartalék tartományok között üzembe helyezhetők. Alapértelmezés szerint a Azure App Service összes új központi telepítése rendelkezik ezzel a képességgel. Azonban az Azure Stack hub 1802-es frissítésének alkalmazása előtt befejezett központi telepítések esetén tekintse meg a [app Service tartalék tartomány dokumentációját](azure-stack-app-service-before-you-get-started.md).

- **Üzembe helyezés meglévő virtuális hálózatban** – az ügyfelek mostantól a meglévő virtuális hálózaton belül üzembe helyezhetik app Service Azure stack hubhoz. Egy meglévő virtuális hálózatban való üzembe helyezés lehetővé teszi, hogy az ügyfelek a Azure App Service számára szükséges, privát portokon keresztül csatlakozzanak a SQL Serverhoz és a fájlkiszolgálón. Az üzembe helyezés során az ügyfelek kiválaszthatják, hogy egy meglévő virtuális hálózaton helyezik üzembe a telepítést, azonban az üzembe helyezés előtt [létre kell hozni az alhálózatokat app Service használatára](azure-stack-app-service-before-you-get-started.md#virtual-network) .

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.11388**frissítése.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:
    - A **.net Core 2,0** -támogatás hozzáadva.
    - **Node. js** -verziók hozzáadva:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - **NPM** -verziók hozzáadva:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - **Php** -frissítések hozzáadva:
        - 5.6.32
        - 7.0.26 (x86 és x64)
        - 7.1.12 (x86 és x64)
    - Frissített **git a Windows** to v 2.14.1
    - Frissítve a **Mercurial** -től a v 4.5.0

  - A **csak HTTPS** funkció támogatása a app Service felhasználói portál egyéni tartomány funkciója területén.

  - A Storage-kapcsolatok ellenőrzése a Azure Functions egyéni tároló-választójában.

#### <a name="fixes"></a>Hibajavítások

- Kapcsolat nélküli központi telepítési csomag létrehozásakor az ügyfelek többé nem kapnak hozzáférési megtagadási hibaüzenetet, amikor megnyitja a mappát a App Service telepítőből.

- Megoldott problémák a App Service felhasználói portál egyéni tartományok funkciójának használatakor.

- A fenntartott rendszergazdai nevek használatának megakadályozása az ügyfelek számára a telepítés során.

- Engedélyezve App Service központi telepítés **tartományhoz csatlakoztatott** fájlkiszolgálón.

- Azure Stack hub főtanúsítványának továbbfejlesztett beolvasása parancsfájlban, valamint a App Service telepítőben a legfelső szintű tanúsítvány érvényesítésének lehetősége.

- Helytelen állapotot adott vissza a rendszer, ha a Microsoft. Web névtérben található erőforrásokat tartalmazó előfizetés törölve lett Azure Resource Manager.

### <a name="known-issues-with-the-deployment-process"></a>Az üzembe helyezési folyamattal kapcsolatos ismert problémák

- Tanúsítvány-érvényesítési hibák.

    Néhány ügyfél olyan problémákat észlelt, amikor a App Service telepítőjének tanúsítványait az integrált rendszeren való üzembe helyezéskor, a telepítő túlságosan korlátozó érvényesítése miatt. Az App Service-telepítőt újra kiadták, és [az ügyfeleknek le kell tölteniük a frissített telepítőt](https://aka.ms/appsvconmasinstaller). Ha továbbra is problémákat tapasztal a tanúsítványok a frissített telepítővel való érvényesítésével kapcsolatban, forduljon az ügyfélszolgálathoz.

- Probléma Azure Stack hub főtanúsítványának integrált rendszerből való beolvasása során.

    A Get-AzureStackRootCert. ps1 hibája miatt az ügyfelek nem tudják lekérni az Azure Stack hub főtanúsítványát, amikor a parancsfájlt olyan gépen futtatják, amelyen nincs telepítve a főtanúsítvány. A szkript már újra megjelent, ami megoldja a problémát. [Töltse le a frissített segítő szkripteket itt](https://aka.ms/appsvconmashelpers). Ha továbbra is problémákat tapasztal a főtanúsítvány frissített parancsfájllal történő beolvasásakor, forduljon az ügyfélszolgálathoz.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Nincsenek ismert problémák a Azure App Service frissítéséhez Azure Stack hub Update 1.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A tárolóhely-csere nem működik.

A tárolóhelyek felcserélése ebben a kiadásban megszakadt. A funkciók visszaállításához hajtsa végre az alábbi lépéseket:

1. Módosítsa a ControllersNSG hálózati biztonsági csoportot, hogy **engedélyezze** a távoli asztali kapcsolatokat a app Service vezérlő példányain. Cserélje le a AppService. local nevet a App Service központilag telepített erőforráscsoport nevére.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Tallózással keresse meg a **CN0-VM-** et Virtual Machines alatt a Azure stack hub felügyeleti portálon, és **kattintson a kapcsolódás lehetőségre** egy távoli asztali munkamenet a vezérlő példánnyal való megnyitásához. Használja a App Service telepítése során megadott hitelesítő adatokat.
3. Indítsa el a **PowerShellt rendszergazdaként** , és hajtsa végre a következő parancsfájlt:

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. A távoli asztali munkamenet lezárása.
5. Állítsa le a ControllersNSG hálózati biztonsági csoportot, hogy **megtagadja** a távoli asztali kapcsolatokat a app Service vezérlő példányaihoz. Cserélje le a AppService. local nevet a App Service központilag telepített erőforráscsoport nevére.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózaton van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: bármely
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az [Azure stack Hub 1802 kibocsátási megjegyzései](azure-stack-update-1903.md) dokumentációját

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stack hub-on: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
