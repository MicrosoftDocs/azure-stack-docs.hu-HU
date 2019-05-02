---
title: Ismételt üzembe helyezése az Azure Stack Development Kit (ASDK) |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja, hogyan telepítse újra a ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 2ebb7c702eeb2c7f04f61f6bbc79aa0edaf8a860
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64292463"
---
# <a name="redeploy-the-asdk"></a>Telepítse újra a ASDK
Ebből a cikkből elsajátíthatja, hogyan ismételt üzembe helyezése az Azure Stack Development Kit (ASDK) egy nem éles környezetben. A ASDK frissítése nem támogatott, mivel teljesen ismételt üzembe helyezése áthelyezése egy újabb verzióra kell. Még csak át szeretné kezdje újra a folyamatot az alapoktól bármikor telepítse újra a ASDK.

> [!IMPORTANT]
> A ASDK frissítése új verzió nem támogatott. Hogy ismételt üzembe helyezése a development kit gazdagépen a ASDK minden alkalommal, amikor szeretne értékelni az Azure Stack egy újabb verziója.

## <a name="remove-azure-registration"></a>Távolítsa el az Azure-regisztráció 
Ha korábban már regisztrált az ASDK telepítése az Azure-ral, ismételt üzembe helyezése a ASDK előtt távolítsa el a regisztrációs erőforrás. Regisztrálja újra a ASDK engedélyezéséhez a piactéren elemek rendelkezésre állását a ASDK újbóli telepítése során. Ha nem korábban már regisztrált az ASDK az Azure-előfizetésében, kihagyhatja az ebben a szakaszban.

A regisztrációs erőforrás eltávolításához használja a **Remove-AzsRegistration** regisztrációjának törlése az Azure Stack-parancsmagot. Ezután használja a **Remove-AzureRMResourceGroup** parancsmagot, hogy az Azure Stack erőforráscsoport törlése az Azure-előfizetésből:

1. Nyisson meg egy PowerShell-konzolt olyan számítógépen, amelyen a kiemelt végponthoz való hozzáférést a rendszergazdaként. A ASDK Ez a fejlesztői csomag gazdaszámítógépen.

2. Futtassa a következő PowerShell-parancsokat regisztrációjának törlése az ASDK telepítése és törlése a **azurestack** az erőforráscsoporthoz az Azure-előfizetéséhez:

   ```Powershell    
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

3. Jelentkezzen be az Azure-előfizetés és a helyi ASDK telepítési is a parancsfájl futtatásakor kéri.
4. Miután a parancsfájl futása befejeződött, a következő példákhoz hasonló üzenetek kell megjelennie:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Az Azure Stack sikeresen kell az Azure-előfizetés regisztrációját. Ezenkívül az azurestack erőforráscsoport jön létre, amikor az Azure-ban regisztrált a ASDK is törölni kell.

## <a name="deploy-the-asdk"></a>A ASDK üzembe helyezése
Az Azure Stack ismételt üzembe helyezése, el kell indítania keresztül teljesen új az alább ismertetett. E telepítéséhez is használt az Azure Stack (asdk-installer.ps1) telepítő szkriptet a ASDK függően más lépéseket kell végrehajtania.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Telepítse újra a ASDK, a telepítő parancsfájl használatával
1. A ASDK számítógépen nyisson meg egy rendszergazda jogú PowerShell-konzolt, és keresse meg a asdk-installer.ps1 parancsfájl a **AzureStack_Installer** könyvtárban található egy nem rendszer-meghajtón. Futtassa a szkriptet, és kattintson a **újraindítás**.

   ![A asdk-installer.ps1 parancsfájl futtatása](media/asdk-redeploy/1.png)

2. Válassza ki az operációs rendszer (nem **Azure Stack**), és kattintson a **tovább**.

   ![Indítsa újra a számítógépet a gazdagép operációs rendszere](media/asdk-redeploy/2.png)

3. Miután a development kit gazdagép újraindult, az alap operációs rendszert, jelentkezzen be helyi rendszergazdaként. Keresse meg és törölje a **C:\CloudBuilder.vhdx** fájlt, amely a korábbi központi telepítésének részeként lett megadva. 

4. Ismételje meg a lépéseket az első igénybe vett [üzembe helyezése a ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Telepítse újra a ASDK anélkül, hogy a telepítő használatával
Ha nem használja a asdk-installer.ps1 parancsfájlt a ASDK telepítéséhez, manuálisan kell állítani a development kit gazdaszámítógép ismételt üzembe helyezése a ASDK előtt.

1. Indítsa el a rendszer-konfigurációs segédprogram futtatásával **msconfig.exe** ASDK számítógépen. Az a **rendszerindító** lapra, válassza ki a gazdagép számítógép operációs rendszere (nem az Azure Stack), majd **alapértelmezett**, és kattintson a **OK**. Kattintson a **indítsa újra a** amikor a rendszer kéri.

      ![A rendszerindítási konfiguráció beállítása](media/asdk-redeploy/4.png)

2. Miután a development kit gazdagép újraindult, az alap operációs rendszert, jelentkezzen be helyi rendszergazdaként a development kit gazdagép esetében. Keresse meg és törölje a **C:\CloudBuilder.vhdx** fájlt, amely a korábbi központi telepítésének részeként lett megadva. 

3. Ismételje meg a lépéseket az első igénybe vett [üzembe helyezése a PowerShell-lel ASDK](asdk-deploy-powershell.md).


## <a name="next-steps"></a>További lépések
[Utáni ASDK üzembe helyezési feladatok](asdk-post-deploy.md)




