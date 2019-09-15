---
title: App Service a Azure Stack Update 1 kibocsátási megjegyzésekben | Microsoft Docs
description: További információ az 1. frissítésben szereplő javításokról, javításokról és ismert problémákról Azure Stack App Service.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 7be74701b3e25658258abc7102668346e584ab39
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974827"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service Azure Stack Update 1 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések az 1. Azure Stack Update-ben Azure App Service javításait, javításait és ismert problémáit ismertetik. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza a 1802-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service üzembe helyezése előtt.

## <a name="build-reference"></a>Hivatkozás létrehozása

A (z) Azure Stack Update 1 Build számának App Service **69.0.13698.9**.

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure App Service on Azure Stack új központi telepítései a következő [három tulajdonos helyettesítő karaktert](azure-stack-app-service-before-you-get-started.md#get-certificates) igénylik, mert a kudu-hez tartozó SSO-t a Azure app Service kezeli. Az új tárgy  **\*:. SSO. appservice.\< régió\>.\< tartománynév\>.\< kiterjesztés\>**

A telepítés megkezdése előtt tekintse át a [App Service Azure stack üzembe helyezésének előfeltételeit](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A (z) Azure Stack Update 1 Azure App Service az alábbi javításokat és javításokat tartalmazza:

- A **Azure app Service magas rendelkezésre állása** – a Azure stack 1802 frissítés engedélyezve munkaterhelések a tartalék tartományok között telepíthetők, ami lehetővé teszi app Service infrastruktúra hibatűrővé tételét a tartalék tartományok között. Alapértelmezés szerint a Azure App Service összes új központi telepítése rendelkezik ezzel a képességgel. Azonban a Azure Stack 1802 frissítés alkalmazása előtt befejezett központi telepítések esetén tekintse meg a [app Service tartalék tartomány dokumentációját](azure-stack-app-service-before-you-get-started.md).

- **Üzembe helyezés meglévő virtuális hálózatban** – az ügyfelek most már üzembe helyezhetik app Service Azure stack egy meglévő virtuális hálózaton belül. Egy meglévő virtuális hálózatban való üzembe helyezés lehetővé teszi, hogy az ügyfelek a Azure App Service számára szükséges, privát portokon keresztül csatlakozzanak a SQL Serverhoz és a fájlkiszolgálón. Az üzembe helyezés során az ügyfelek kiválaszthatják, hogy egy meglévő virtuális hálózaton helyezik üzembe a telepítést, azonban az üzembe helyezés előtt [létre kell hozni az alhálózatokat app Service használatára](azure-stack-app-service-before-you-get-started.md#virtual-network) .

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

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

  - A **csak HTTPS** funkció támogatása a app Service bérlői portál egyéni tartomány funkcióján belül.

  - A Storage-kapcsolatok ellenőrzése a Azure Functions egyéni tároló-választójában.

#### <a name="fixes"></a>Javítások

- Kapcsolat nélküli központi telepítési csomag létrehozásakor az ügyfelek többé nem kapnak hozzáférési megtagadási hibaüzenetet, amikor megnyitja a mappát a App Service telepítőből.

- Megoldott problémák a App Service bérlői portál egyéni tartományok funkciójának használatakor.

- A fenntartott rendszergazdai nevek használatának megakadályozása az ügyfelek számára a telepítés során.

- Engedélyezve App Service központi telepítés **tartományhoz csatlakoztatott** fájlkiszolgálón.

- Azure Stack főtanúsítvány jobb beolvasása parancsfájlban, és a App Service telepítőben a legfelső szintű tanúsítvány érvényesítésének lehetősége.

- Helytelen állapotot adott vissza a rendszer, ha a Microsoft. Web névtérben található erőforrásokat tartalmazó előfizetés törölve lett Azure Resource Manager.

### <a name="known-issues-with-the-deployment-process"></a>Az üzembe helyezési folyamattal kapcsolatos ismert problémák

- Tanúsítvány-érvényesítési hibák.

    Néhány ügyfél olyan problémákat észlelt, amikor a App Service telepítőjének tanúsítványait az integrált rendszeren való üzembe helyezéskor, a telepítő túlságosan korlátozó érvényesítése miatt. Az App Service-telepítőt újra kiadták, és [az ügyfeleknek le kell tölteniük a frissített telepítőt](https://aka.ms/appsvconmasinstaller). Ha továbbra is problémákat tapasztal a tanúsítványok a frissített telepítővel való érvényesítésével kapcsolatban, forduljon az ügyfélszolgálathoz.

- Probléma Azure Stack főtanúsítvány beolvasása az integrált rendszerből.

    A Get-AzureStackRootCert. ps1 hibája miatt az ügyfelek nem tudják lekérni a Azure Stack főtanúsítványt, amikor olyan gépen futtatják a parancsfájlt, amelyen nincs telepítve a főtanúsítvány. A szkript már újra megjelent, ami megoldja a problémát. [Töltse le a frissített segítő szkripteket itt](https://aka.ms/appsvconmashelpers). Ha továbbra is problémákat tapasztal a főtanúsítvány frissített parancsfájllal történő beolvasásakor, forduljon az ügyfélszolgálathoz.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Nincsenek ismert problémák a Azure App Service frissítéséhez az 1. Azure Stack Update-ben.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

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

2. Keresse meg a **CN0-VM-** et a Virtual Machines alatt a Azure stack felügyeleti portálon, és **kattintson a kapcsolódás lehetőségre** egy távoli asztali munkamenetnek a vezérlő példánnyal való megnyitásához. Használja a App Service telepítése során megadott hitelesítő adatokat.
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
5. Állítsa le a ControllersNSG hálózati biztonsági csoportot, hogy megtagadja a távoli asztali kapcsolatokat a app Service vezérlő példányaihoz. Cserélje le a AppService. local nevet a App Service központilag telepített erőforráscsoport nevére.

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

- Forrás: Any
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Cél IP-címtartomány: A fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Allow
- Fontosság: 700
- Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack

Tekintse meg a dokumentációt a [Azure Stack 1802 kibocsátási megjegyzésekben](azure-stack-update-1903.md)

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- A Azure Stack App Service telepítésének előkészítésével kapcsolatos további információkért lásd: [app Service üzembe helyezésének előfeltételei a Azure stack](azure-stack-app-service-before-you-get-started.md).
