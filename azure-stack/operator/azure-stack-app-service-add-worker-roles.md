---
title: Horizontális felskálázás az App Services szolgáltatásban – Azure Stack a feldolgozói szerepkörökhöz |} A Microsoft Docs
description: Részletes útmutatást az Azure Stack App Services méretezése
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: c7dcb31c74ac08f3045425d246e17376808a5b98
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269304"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service-ben az Azure Stack: További infrastruktúra vagy feldolgozói szerepkörök hozzáadása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*  

Ez a dokumentum méretezése App Service-ben az Azure Stack-infrastruktúra és a feldolgozói szerepkörök kapcsolatos útmutatást. Bármilyen méretű alkalmazások támogatásához további feldolgozói szerepkörök létrehozásának lépéseit tartalmazza.

> [!NOTE]
> Ha az Azure Stack-környezet nem rendelkezik több mint 96 GB RAM, előfordulhat, hogy további kapacitást nehézségek.

App Service-ben az Azure Stacken, alapértelmezés szerint támogatja az ingyenes és a megosztott feldolgozói rétegek. Adja hozzá a többi feldolgozói rétegek, további feldolgozói szerepkörök hozzáadása kell.

Ha nem biztos abban, hogy mi az Azure Stack-telepítés az alapértelmezett App Service-ben üzemel, áttekintheti a további információkat a [App Service-ben az Azure Stack áttekintése](azure-stack-app-service-overview.md).

Az Azure Stack az Azure App Service üzembe helyezi a Virtual Machine Scale Sets használatával az összes szerepkör, és így igénybe veszi ezt a munkafolyamatot skálázási funkcióit. Ezért az összes méretezés a feldolgozói rétegek használatával történik az App Service-rendszergazdához.

## <a name="add-additional-workers-with-powershell"></a>Adja hozzá a további feldolgozó a PowerShell-lel

1. [A PowerShell az Azure Stack rendszergazdai környezet beállítása](azure-stack-powershell-configure-admin.md)

2. Ez a példa használatával horizontális felskálázása a méretezési csoportot:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Ebben a lépésben a szerepkör típusát és a példányok számát függően órákig is igénybe vehet.
   >
   >

3. Az új App Service felügyeletének szerepkörpéldányt állapotának figyelése, egy egyéni szerepkör-példány állapota kattintson a szerepkör típusát, a listában.

## <a name="add-additional-workers-using-the-administration-portal"></a>Adja hozzá a felügyeleti portál használatával további feldolgozók

1. Jelentkezzen be az Azure Stack felügyeleti portálon a szolgáltatás-rendszergazdaként.

2. Keresse meg a **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Kattintson a **szerepkörök**. Itt láthatja az összes üzembe helyezett App Service-szerepkör lebontása.

4. A jobb gombbal a sor a méretezhető, és kattintson a kívánt típusú a **méretezési**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Kattintson a **méretezés**, válassza ki a méretezhető, és kattintson a kívánt példányok számát **mentése**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Az Azure Stack App Service-ben fog most adja hozzá a további virtuális gépek, konfigurálhatja őket, a szükséges szoftver telepítése és megjelölni készen áll a folyamat befejeződése. Ez a folyamat körülbelül 80 percet is igénybe vehet.

7. Az új szerepkör készen áll-e az előrehaladását a dolgozók megtekintésével figyelheti a **szerepkörök** panelen.

## <a name="result"></a>Eredmény

Miután bekerültek a teljes körűen rendszerbe állított és készen áll, a dolgozók be azokat a számítási feladatok üzembe helyezése felhasználók válnak. Az alábbiakban látható egy példa a többféle tarifacsomagban is kapható elérhető alapértelmezés szerint. Ha egy adott feldolgozói réteg nincs rendelkezésre álló feldolgozók, válassza ki a megfelelő tarifacsomagot lehetőség nem érhető el.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> A horizontális felskálázáshoz felügyeleti kezelőfelület vagy a közzétevő szerepkört adja hozzá, ki kell terjeszteni a megfelelő szerepkör-típus. 
>
>

A horizontális felskálázáshoz-kezelés, az előtérbeli vagy szerepköröket közzétevő ugyanezekkel a lépésekkel jelölje ki a megfelelő szerepkört. Tartományvezérlők, a méretezési csoportok nem települnek, és ezért két kell telepíteni az összes éles környezetekben üzemelő példányok telepítéskor.

### <a name="next-steps"></a>További lépések

[Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md)
