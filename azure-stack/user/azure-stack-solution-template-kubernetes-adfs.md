---
title: Az Active Directory összevonási szolgáltatásokban (AD FS) használatával az Azure Stack üzembe helyezése Kubernetes |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Kubernetes az Azure Stackhez az Active Directory összevonási szolgáltatásokban (AD FS) használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 367053882e9e44ba983d5df552929c65222fcafc
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197348"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Az Active Directory összevont szolgáltatásokat az Azure Stack üzembe helyezése Kubernetes

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

A regisztrálással kapcsolatban, telepítse és állítsa be az erőforrásokat a Kubernetes esetében ez a cikk. Az Active Directory összevonási szolgáltatásokban (AD FS) az identity management-szolgáltatás az alábbi lépéseket követve.

## <a name="prerequisites"></a>Előfeltételek 

Első lépésként ellenőrizze, hogy a megfelelő engedélyekkel rendelkezik, és, hogy készen áll-e az Azure Stack.

1. Hozzon létre nyilvános és titkos ssh-kulcs, jelentkezzen be a Linux rendszerű virtuális gép az Azure Stacken. A fürt létrehozásakor kell a nyilvános kulcsot.

    -Kulcs létrehozásával kapcsolatos utasításokért lásd: [SSH kulcs generálása](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Ellenőrizze, hogy az Azure Stack-bérlői portálon érvényes előfizetéssel rendelkezik, és, hogy rendelkezik-e elegendő nyilvános IP-címek adhatók hozzá az új alkalmazások.

    A fürt nem telepíthető az Azure Stackkel **rendszergazda** előfizetés. Szüksége lesz egy **felhasználói** előfizetés. 

1. Szüksége lesz a Key Vault szolgáltatásban az Azure Stack-előfizetés.

1. Szüksége lesz a Kubernetes-fürt a Marketplace-en. 

Ha a Key Vault szolgáltatásban és a Kubernetes-fürt marketplace-elem hiányzik, forduljon az Azure Stack rendszergazdai.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az AD FS-identitáskezelési megoldásként használatakor az egyszerű szolgáltatás beállítása az Azure Stack rendszergazdával együttműködve kell. Egyszerű szolgáltatás Azure Stack-erőforrások az alkalmazás-hozzáférést biztosít.

1. Az Azure Stack rendszergazdai biztosít egy tanúsítványt, és a szolgáltatásnév adatait.

   - A szolgáltatásnév adatait hasonlóan kell kinéznie:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - A tanúsítvány lesz kiterjesztésű fájl `.pfx`. Egy titkos kulcsot, a tanúsítványt fogja tárolni egy kulcstartóban.

2. Közreműködője szerepkör az új egyszerű szolgáltatás hozzárendelése az előfizetéshez. Útmutatásért lásd: [szerepkör hozzárendelése](../operator/azure-stack-create-service-principals.md).

3. Központi telepítés a tanúsítvány tárolásához használandó key vault létrehozása. A portál helyett a következő PowerShell-parancsfájlokat használja.

   - Az alábbi adatokra lesz szüksége:

       | Érték | Leírás |
       | ---   | ---         |
       | Az Azure Resource Manager-végpont | A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.<br>A végpont az Azure Stack Development Kit (ASDK) a következő: `https://management.local.azurestack.external/`<br>A végpont az integrált rendszerekről van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Az előfizetés-azonosító | A [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |
       | A felhasználónév | Használja a felhasználónév helyett a tartománynév és a felhasználó nevét, például `username` helyett `azurestack\username`. |
       | Az erőforráscsoport neve  | Egy új erőforráscsoportot, vagy válasszon egy meglévő erőforráscsoport neve. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket. |
       | Key Vault neve | A tároló nevére.<br> Reguláriskifejezés-minta: `^[a-zA-Z0-9-]{3,24}$` |
       | Erőforráscsoport helye | Az erőforráscsoport helyét. Ez az a régió úgy dönt, az Azure Stack-telepítés. |

   - Nyisson meg egy rendszergazda jogú parancssort, PowerShell és [csatlakozás az Azure Stack](azure-stack-powershell-configure-user.md#connect-to-azure-stack-with-ad-fs). Futtassa a következő szkriptet a paraméterekkel az értékek a frissített:

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. Töltse fel a tanúsítványt a key vaulttal.

   - Az alábbi adatokra lesz szüksége:

       | Érték | Leírás |
       | ---   | ---         |
       | Tanúsítvány elérési útja | A teljes tartománynév vagy a fájl elérési útja a tanúsítványt. |
       | Tanúsítványjelszó | A tanúsítvány jelszavát. |
       | Název tajného kódu | A mutató hivatkozás a tanúsítvány a tárolóban tárolt titkos neve. |
       | A Key vault neve | A key vault az előző lépésben létrehozott neve. |
       | Az Azure Resource Manager-végpont | A végpont az Azure Stack Development Kit (ASDK) a következő: `https://management.local.azurestack.external/`<br>A végpont az integrált rendszerekről van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Az előfizetés-azonosító | A [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |

   - Nyisson meg egy rendszergazda jogú parancssort, PowerShell és [csatlakozás az Azure Stack](azure-stack-powershell-configure-user.md#connect-to-azure-stack-with-ad-fs). Futtassa a következő szkriptet a paraméterekkel az értékek a frissített:

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).

1. Válassza ki **+ erőforrás létrehozása** > **számítási** > **Kubernetes-fürt**. Kattintson a **Create** (Létrehozás) gombra.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Alapvető beállítások

1. Válassza ki **alapjai** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Válassza ki a **előfizetés** azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket.

1. Válassza ki a **hely** az erőforráscsoport. Ez az a régió úgy dönt, az Azure Stack-telepítés.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes-fürt beállításai

1. Válassza ki **Kubernetes-fürt beállítások** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Adja meg a **Linux rendszerű virtuális gép rendszergazdai felhasználónevét**. A Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználóneve.

1. Adja meg a **SSH Public Key** használt a hitelesítéshez a Kubernetes-fürt és a DVM részeként létrehozott összes Linux rendszerű gépen.

1. Adja meg a **fő profil DNS-előtagja** , amely egyedi a régióban. Ez egy régió egyedi nevet, például kell lennie `k8s-12345`. Próbálja meg úgy döntött, hogy ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Ha mindegyik fürthöz egy új és egyedi fő profil DNS-előtagot használja.

1. Válassza ki a **Kubernetes fő készlet profil darabszám**. A szám a fő készletben lévő csomópontok számát tartalmazza. Nem lehet 1-től 7. Ez az érték páratlan számúaknak kell lennie.

1. Válassza ki **a fő Kubernetes-virtuálisgép az VMSize**.

1. Válassza ki a **Kubernetes csomópontok készlet profil száma**. A száma a fürtben található ügynökök számát tartalmazza. 

1. Válassza ki a **Tárolóprofil**. Választhat **Blob lemez** vagy **Managed Disk**. Azt határozza meg a virtuális gép méretét a Kubernetes csomópont azon virtuális gépeit. 

1. Válassza ki **ADFS** számára a **Azure Stack identitásrendszer** az Azure Stack-telepítés.

1. Adja meg a **szolgáltatásnév ClientId** ezt használja a Kubernetes Azure felhőszolgáltató. Az Alkalmazásazonosítót azonosította az eseményt az egyszerű szolgáltatás létrehozásakor az Azure Stack rendszergazdai ügyfél-azonosító.

1. Adja meg a **Key Vault-erőforráscsoport** , amely adatforrásokat a kulcstartóhoz, amely tartalmazza a tanúsítványt.

1. Adja meg a **Key Vault-névre** a tanúsítványt a titkos kulcsot tartalmazó kulcstartó neve. 

1. Adja meg a **Key Vault titkos kulcsából**. Název tajného kódu hivatkozik a tanúsítványt.

1. Adja meg a **Kubernetes az Azure Cloud szolgáltató verziója**. Ez az a verzió a Kubernetes Azure-szolgáltatóhoz. Az Azure Stack kiad egy egyéni Kubernetes-build minden egyes Azure Stack-verzió.

### <a name="3-summary"></a>3. Összegzés

1. Válassza ki a összegzése. A panel a Kubernetes-fürt konfigurációk beállítások érvényesítése üzenetet jelenít meg.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Tekintse át a beállításokat.

3. Válassza ki **OK** a fürt üzembe helyezéséhez.

> [!TIP]  
>  Ha az üzembe helyezéssel kapcsolatos kérdése van, tegye fel a kérdéseit, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>További lépések

[Csatlakozás a fürthöz](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[A Kubernetes-irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)