---
title: Feldolgozók és infrastruktúra hozzáadása a App Service Azure Stackban | Microsoft Docs
description: Részletes útmutató a Azure Stack App Services skálázásához
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
ms.openlocfilehash: 43ee38c18e2831d1cb96958501cee6f77292edd0
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271682"
---
# <a name="add-workers-and-infrastructure-in-app-service-on-azure-stack"></a>Feldolgozók és infrastruktúra hozzáadása App Service Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*  

Ez a dokumentum útmutatást nyújt az infrastruktúra és a feldolgozói szerepkörök méretezéséhez a Azure Stack App Serviceban. Részletesen ismertetjük mindazokat a lépéseket, amelyek szükségesek a további feldolgozói szerepkörök létrehozásához bármilyen méretű alkalmazások támogatásához.

> [!NOTE]
> Ha a Azure Stack-környezet nem rendelkezik több mint 96 GB RAM-mal, lehetséges, hogy nehézségekbe ütközik a további kapacitás hozzáadásával.

A App Service on Azure Stack az ingyenes és a közös feldolgozói szintet is támogatja alapértelmezetten. Más munkavégző rétegek hozzáadásához további feldolgozói szerepköröket kell hozzáadnia.

Ha nem biztos abban, hogy mi lett telepítve az alapértelmezett App Service Azure Stack telepítéskor, tekintse át a App Service további információit [Azure stack áttekintésében](azure-stack-app-service-overview.md).

A Azure App Service on Azure Stack az összes szerepkört a Virtual Machine Scale Sets használatával helyezi üzembe, és így kihasználja a munkaterhelés méretezési képességeit. Ezért a feldolgozói szintek minden skálázása a App Service-rendszergazda használatával történik.

## <a name="add-additional-workers-with-powershell"></a>További feldolgozók hozzáadása a PowerShell-lel

1. [A Azure Stack felügyeleti környezet beállítása a PowerShellben](azure-stack-powershell-configure-admin.md)

2. Ez a példa a méretezési csoport felskálázására használható:
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
   > Ez a lépés a szerepkör típusától és a példányok számától függően több órát is igénybe vehet.
   >
   >

3. Az új szerepkör-példányok állapotának figyelése a App Service felügyeletben. Egy adott szerepkör-példány állapotának megtekintéséhez kattintson a listában a szerepkör típusára.

## <a name="add-additional-workers-using-the-admin-portal"></a>További feldolgozók felvétele a felügyeleti portál használatával

1. Jelentkezzen be a Azure Stack felügyeleti portálra szolgáltatás-rendszergazdaként.

2. Tallózással keresse meg **app Services**.

    ![App Service a Azure Stack felügyeleti portálon](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Kattintson a **szerepkörök**elemre. Itt láthatja az összes telepített App Service-szerepkör részletezését.

4. Kattintson a jobb gombbal a méretezni kívánt típus sorára, majd kattintson a **méretezési csoport**elemre.

    ![Méretezési csoport App Service szerepkörök a Azure Stack felügyeleti portálon](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Kattintson a **skálázás**elemre, válassza ki a méretezni kívánt példányok számát, majd kattintson a **Mentés**gombra.

    ![A Azure Stack felügyeleti portálon App Service szerepkörökre méretezési példányok beállítása](media/azure-stack-app-service-add-worker-roles/image03.png)

6. A App Service on Azure Stack mostantól hozzáadja a további virtuális gépeket, konfigurálja őket, telepíti az összes szükséges szoftvert, és készként jelöli meg őket a folyamat befejezésekor. Ez a folyamat körülbelül 80 percet is igénybe vehet.

7. A **szerepkörök** panelen megtekintheti az új szerepkörök készültségének előrehaladását.

## <a name="result"></a>Eredmény

Miután teljesen üzembe lettek és készen állnak, a dolgozók elérhetővé válnak a felhasználók számára a számítási feladatok üzembe helyezéséhez. Az alábbi képernyőképen egy példa látható a több, alapértelmezés szerint elérhető díjszabási rétegre. Ha egy adott feldolgozói szinten nem állnak rendelkezésre munkavégzők, a megfelelő árképzési csomag kiválasztásának lehetősége nem érhető el.

![Az új App Service-csomag díjszabása Azure Stack felügyeleti portálon](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> A felügyeleti, az előtér-vagy a közzétevői szerepkörök felskálázásához kövesse ugyanezen lépéseket a megfelelő szerepkör típusának kiválasztásával. A vezérlők nem helyezhetők üzembe méretezési csoportként, ezért a telepítéskor a kettőt telepíteni kell az összes éles környezetben.

### <a name="next-steps"></a>További lépések

[Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md)
