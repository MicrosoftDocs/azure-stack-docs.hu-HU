---
title: A ASDK újbóli üzembe helyezése
description: Megtudhatja, hogyan telepítheti újra a Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: d0fc4539b581474c9db2a2dbb05495c9b1bce695
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873515"
---
# <a name="redeploy-the-asdk"></a>A ASDK újbóli üzembe helyezése
Ebből a cikkből megtudhatja, hogyan telepítheti újra a Azure Stack Development Kit (ASDK) nem éles környezetben. Mivel a ASDK frissítése nem támogatott, teljesen újra kell telepítenie, hogy egy újabb verzióra váltson. Újból üzembe helyezheti a ASDK, amikor teljesen új kezdést szeretne kezdeni.

> [!IMPORTANT]
> A ASDK új verzióra való frissítése nem támogatott. Minden alkalommal újra kell telepítenie a ASDK a ASDK-gazdagépen, ha szeretné kiértékelni Azure Stack újabb verzióját.

## <a name="remove-azure-registration"></a>Azure-regisztráció eltávolítása 
Ha korábban regisztrálta a ASDK-telepítést az Azure-ban, távolítsa el a regisztrációs erőforrást a ASDK újbóli üzembe helyezése előtt. Regisztrálja újra a ASDK, hogy engedélyezze a piactéren található elemek rendelkezésre állását a ASDK újbóli telepítésekor. Ha korábban még nem regisztrálta az ASDK az Azure-előfizetésével, kihagyhatja ezt a szakaszt.

A regisztrációs erőforrás eltávolításához használja a **Remove-AzsRegistration** parancsmagot a Azure stack regisztrációjának törléséhez. Ezután a **Remove-AzureRMResourceGroup** parancsmag használatával törölje a Azure stack erőforráscsoportot az Azure-előfizetésből:

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként egy olyan számítógépen, amely hozzáfér a Kiemelt végponthoz. A ASDK esetében ez a ASDK-gazda számítógép.

2. Futtassa a következő PowerShell-parancsokat a ASDK-telepítés regisztrációjának törléséhez, és törölje a **azurestack** -erőforráscsoportot az Azure-előfizetésből:

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. A rendszer felszólítja, hogy jelentkezzen be az Azure-előfizetéséhez és a helyi ASDK-telepítéshez a parancsfájl futtatásakor.
4. A parancsfájl befejeződése után az alábbi példákhoz hasonló üzeneteket kell látnia:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



A Azure Stack mostantól sikeresen törölni kell az Azure-előfizetésből. A azurestack erőforráscsoportot is törölni kell. Ez az erőforráscsoport a ASDK az Azure-ban való első regisztrálásakor létrehozott csoport.

## <a name="deploy-the-asdk"></a>A ASDK üzembe helyezése
Azure Stack újbóli üzembe helyezéséhez elölről kell kezdenie az alábbiak szerint. A lépések eltérnek attól függően, hogy a Azure Stack Installer (asdk-Installer. ps1) parancsfájlt használta-e a ASDK telepítéséhez.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>A ASDK újratelepítése a telepítő parancsfájl használatával
1. A ASDK számítógépen nyisson meg egy rendszergazda jogú PowerShell-konzolt, és navigáljon a asdk-Installer. ps1 parancsfájlhoz a nem rendszermeghajtón található **AzureStack_Installer** könyvtárban. Futtassa a szkriptet, és kattintson az **Újraindítás**gombra.

   ![Futtassa a asdk-Installer. ps1 parancsfájlt](media/asdk-redeploy/1.png)

2. Válassza ki az alapszintű operációs rendszert (nem **Azure stack**), majd kattintson a **tovább**gombra.

   ![Újraindítás a gazda operációs rendszerbe](media/asdk-redeploy/2.png)

3. Miután a ASDK gazdagép újraindult az alapszintű operációs rendszerbe, jelentkezzen be helyi rendszergazdaként. Keresse meg és törölje az előző üzemelő példány részeként használt **C:\CloudBuilder.vhdx** -fájlt.

4. Ismételje meg ugyanezeket a lépéseket, amelyeket először [a ASDK üzembe helyezése](asdk-install.md)során vett igénybe.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>A ASDK újratelepítése a telepítő használata nélkül
Ha nem a asdk-Installer. ps1 parancsfájlt használja a ASDK telepítéséhez, akkor a ASDK újratelepítése előtt manuálisan kell konfigurálnia a ASDK-gazdagépet.

1. Indítsa el a rendszerkonfigurációs segédprogramot a ASDK számítógép **msconfig. exe** fájljának futtatásával. A **rendszerindítás** lapon válassza ki a gazdagép operációs rendszerét (nem Azure stack), kattintson a **Beállítás alapértelmezettként**lehetőségre, majd **az OK**gombra. Ha a rendszer kéri, kattintson az **Újraindítás** gombra.

      ![A rendszerindítási konfiguráció beállítása](media/asdk-redeploy/4.png)

2. Miután a ASDK gazdagép újraindult az alapszintű operációs rendszerbe, jelentkezzen be helyi rendszergazdaként a ASDK gazdagéphez. Keresse meg és törölje az előző üzemelő példány részeként használt **C:\CloudBuilder.vhdx** -fájlt.

3. Ismételje meg ugyanezen lépéseket, amelyeket először [a PowerShell használatával kell üzembe helyeznie a ASDK](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Következő lépések
[ASDK-telepítési feladatok közzététele](asdk-post-deploy.md)




