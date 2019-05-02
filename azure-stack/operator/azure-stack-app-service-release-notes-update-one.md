---
title: App Service-ben az Azure Stack update 1 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg a frissítést az App Service az Azure Stacken, az ismert problémákról, valamint a helyét, a frissítés letöltése.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digmobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 86eb23e57516bc108557bbdc4f2d131153f5e855
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290527"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service-ben az Azure Stack update 1 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések láthatók a fejlesztései, valamint a javításokat az Azure App Service az Azure Stack Update 1 és olyan ismert problémákat. Ismert problémák az üzembe helyezési, frissítési folyamat és a build (telepítés utáni) problémái közvetlenül kapcsolódó problémák vannak felosztva.

> [!IMPORTANT]
> Az 1802-es frissítés alkalmazása az Azure Stackkel integrált rendszer, vagy a legújabb Azure Stack fejlesztői készletének telepítése az Azure App Service üzembe helyezése előtt.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure Stack Update 1 buildszám **69.0.13698.9**

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Új telepítések esetén az Azure App Service az Azure Stacken most szükséges egy [három-tulajdonos helyettesítő tanúsítvány](azure-stack-app-service-before-you-get-started.md#get-certificates) köszönhető, amelyben az Azure App Service mostantól kezelése a Kudu egyszeri Bejelentkezést. Az új tulajdonos  **\*. sso.appservice.\< régió\>.\< tartománynév\>.\< bővítmény\>**
>
>

Tekintse meg a [mielőtt elkezdené a dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal

Az Azure App Service-ben az Azure Stack 1. frissítés tartalmazza a következő fejlesztések és javítások:

- **Magas rendelkezésre állás az Azure App Service** – az Azure Stack 1802-es frissítés engedélyezve számítási feladatokhoz történő központi tartalék tartományokat. Ezért az App Service-infrastruktúra képes legyen képes lehet a hibatűrő, mivel a tartalék tartomány között pedig települnek. Alapértelmezés szerint az Azure App Service összes új üzemelő példányhoz rendelkezik ezzel a képességgel azonban a központi telepítés befejezése előtt az Azure Stack 1802-es frissítés alkalmazása folyamatban tekintse meg a [App Service-tartalék tartomány dokumentációja](azure-stack-app-service-before-you-get-started.md )

- **A meglévő virtuális hálózat üzembe helyezése** -ügyfelek most már telepítheti az Azure Stack App Service-ben meglévő virtuális hálózaton belül. Egy meglévő virtuális hálózaton üzembe helyezése lehetővé teszi ügyfeleink számára, az SQL Server és a fájlkiszolgáló, az Azure App Service-ben privát portokon keresztül szükséges kapcsolódni. Üzembe helyezés során az ügyfelek üzembe helyezése meglévő virtuális hálózattal, viszont ki [létre kell hoznia az App Service általi használatra alhálózatok](azure-stack-app-service-before-you-get-started.md#virtual-network) üzembe helyezés előtt.

- Frissítések **App Services-bérlő, a rendszergazda, a Functions-portálok és eszközök a Kudu**. Az Azure Stack Portal SDK-verzió összhangban.

- Frissítések **Azure Functions runtime** való **v1.0.11388**.

- **A következő alkalmazás-keretrendszerek és eszközök frissítések**:
    - Hozzáadott **.NET Core 2.0** támogatása
    - Hozzáadott **Node.JS** verziók:
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
    - Hozzáadott **NPM** verziók:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Hozzáadott **PHP** frissítések:
        - 5.6.32
        - 7.0.26 (x86 és x64)
        - 7.1.12-es (x86 és x64)
    - Frissített **Git for Windows** v 2.14.1
    - Frissített **Mercurial** v4.5.0,

  - Támogatás hozzáadva az **csak HTTPS** belül az App Service-bérlői portál az egyéni tartomány a szolgáltatás a szolgáltatás. 

  - Az Azure Functions az egyéni tároló-választó tárolási kapcsolat hozzáadott érvényesítése 

#### <a name="fixes"></a>Javítások

- Egy kapcsolat nélküli telepítőcsomag létrehozása, ha ügyfelek többé nem kapja meg "hozzáférés megtagadva" hibaüzenetet kap, amikor megnyitni a mappát, az az App Service-telepítő

- Az App Service-bérlői portál az egyéni tartományok funkció használatakor a probléma megoldása.

- Megakadályozza a telepítés során fenntartott rendszergazdai neveket használó ügyfeleink

- Engedélyezve van az App Service-környezet **tartományhoz csatlakoztatott** fájlkiszolgáló

- Továbbfejlesztett lekérése az Azure Stack legfelső szintű tanúsítvány-szkriptben, és most az az App Service-telepítő a legfelső szintű tanúsítvány érvényesítése.

- Az Azure Resource Manager alatt adja vissza, ha egy előfizetés rögzített helytelen állapot a benne foglalt erőforrásokat Microsoft.Web névtér törlése.

### <a name="known-issues-with-the-deployment-process"></a>Az üzembe helyezési folyamat ismert problémái

- Tanúsítvány-ellenőrzési hibák

Egyes ügyfeleink problémák lépett fel változatának tanúsítványok az App Service-telepítő való telepítésekor egy integrált rendszer, a telepítő a túl szigorú ellenőrzési miatt. Az App Service-telepítő új kiadása, az ügyfeleknek kell [töltse le a frissített](https://aka.ms/appsvconmasinstaller). Ha folytatja, a frissített telepítővel tanúsítványok érvényesítése hibákat tapasztal, forduljon az ügyfélszolgálathoz.

- A probléma az Azure Stack főtanúsítványának integrált rendszer való beolvasásakor.

A Get-AzureStackRootCert.ps1 hibája miatt az ügyfelek számára nem sikerült beolvasni az Azure Stack főtanúsítványának olyan számítógépen, amelyen nincs telepítve a legfelső szintű tanúsítvány a parancsfájl végrehajtása közben. A parancsfájl emellett mostantól új kiadása, ezt a problémát, és a kérés ügyfelek feloldása [töltse le a frissített segítő szkripteket](https://aka.ms/appsvconmashelpers). Ha továbbra is a frissített szkript a következő főtanúsítványok beolvasása közben jelentkezik, forduljon az ügyfélszolgálathoz.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Nem tartoznak ismert problémák az Azure App Service az Azure Stack Update 1 frissítése.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Tárolóhelycsere nem működik.

Ebben a kiadásban hely tárolóhelycsere megszakadt. Működésének visszaállításához hajtsa végre a következő lépéseket:

1. Módosítsa a ControllersNSG hálózati biztonsági csoport **engedélyezése** vezérlő App Service-példányok távoli asztali kapcsolatokat. AppService.local cserélje le az üzembe helyezett App Service-erőforráscsoport nevére.

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

2. Keresse meg a **CN0 virtuális** alatt az Azure Stack felügyeleti portálon található virtuális gépek és **kattintson a csatlakozás** , nyissa meg a távoli asztali munkamenetet a tartományvezérlő-példány. Az App Service üzembe helyezése során megadott hitelesítő adatokat használja.
3. Indítsa el **Powershellt rendszergazdaként** , és hajtsa végre a következő parancsfájl

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

4. Zárja be a távoli asztali munkamenetet.
5. A ControllersNSG hálózati biztonsági csoport a visszaállítás **Megtagadás** vezérlő App Service-példányok távoli asztali kapcsolatokat. AppService.local cserélje le az üzembe helyezett App Service-erőforráscsoport nevére.

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

6. Feldolgozók nem érhető el a fájlkiszolgálót, amikor az App Service-ben meglévő virtuális hálózaton van üzembe helyezve, és a fájlkiszolgáló csak érhető el a magánhálózaton.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Ehhez nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: Bármelyik
- Forrás porttartomány: *
- Cél: IP-címek
- Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
- Cél porttartomány: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák működtetése az Azure App Service az Azure Stack felhő-rendszergazdák számára

A dokumentáció a [Azure Stack 1802 kibocsátási megjegyzései](azure-stack-update-1903.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintését lásd: [Azure App Service az Azure Stack áttekintése](azure-stack-app-service-overview.md).
- Hogyan készíti elő az Azure Stack App Service üzembe helyezése kapcsolatos további információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md).

<!-- Update_Description: wording update -->