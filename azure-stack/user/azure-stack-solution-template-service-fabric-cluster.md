---
title: Biztonságos Service Fabric-fürt üzembe helyezése Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe biztonságos Service Fabric-fürtöt a Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 8a323bf5d767db288cd2d876f38119b61f577afa
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909671"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Service Fabric-fürt üzembe helyezése Azure Stack

Az Azure Marketplace **Service Fabric-fürtjével** biztonságos Service Fabric-fürtöt helyezhet üzembe a Azure Stackban. 

További információ a Service Fabric használatáról: az Azure- [Service Fabric áttekintése](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) és a [Service Fabric-fürtök biztonsági forgatókönyvei](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) az Azure dokumentációjában.

A Azure Stack Service Fabric-fürt nem a Microsoft. ServiceFabric erőforrás-szolgáltatót használja. Ehelyett Azure Stack a Service Fabric-fürt egy virtuálisgép-méretezési csoport egy előre telepített szoftverrel a [kívánt állapot-konfiguráció (DSC)](https://docs.microsoft.com/powershell/dsc/overview/overview)használatával.

## <a name="prerequisites"></a>Előfeltételek

A Service Fabric-fürt telepítéséhez a következők szükségesek:
1. **Fürt tanúsítványa**  
   A Service Fabric telepítésekor a Key Vaulthoz hozzáadott X. 509 kiszolgálói tanúsítvány. 
   - A tanúsítványhoz tartozó **CN-** nek meg kell egyeznie a létrehozott Service Fabric-fürt teljes tartománynevével (FQDN). A teljes tartománynévvel kapcsolatos további útmutatásért tekintse meg [Azure App Service Azure stack éles környezetben történő üzembe helyezéséhez szükséges tanúsítványokat](../operator/azure-stack-app-service-before-you-get-started.md#certificates-required-for-azure-stack-production-deployment-of-azure-app-service).
   - A tanúsítvány formátumának PFX-nek kell lennie, mivel a nyilvános és a titkos kulcs is kötelező. 
     Lásd a kiszolgálóoldali tanúsítvány létrehozásához [szükséges követelményeket](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) .

     > [!NOTE]  
     > Tesztelési célból az X. 509 kiszolgálói tanúsítvány önaláírt tanúsítványát is használhatja. Az önaláírt tanúsítványoknak nem kell megegyezniük a fürt teljes tartománynevével.

1. **Rendszergazdai ügyféltanúsítvány**  
   Ez az a tanúsítvány, amelyet az ügyfél a Service Fabric-fürt hitelesítésére használ, amely önaláírt lehet. Tekintse meg az ügyfél-tanúsítvány létrehozásához [szükséges követelményeket](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) .

1. **A következő elemeknek elérhetőknek kell lenniük a Azure Stack piactéren:**
    - **Windows server 2016** – a sablon a windows Server 2016 rendszerképet használja a fürt létrehozásához.  
    - **Egyéni szkriptek bővítménye** – virtuálisgép-bővítmény a Microsofttól.  
    - A **PowerShell kívánt fázisának konfigurálása** – virtuálisgép-bővítmény a Microsofttól.


## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz
Service Fabric-fürt üzembe helyezéséhez meg kell adnia a Service Fabric fürt helyes Key Vault *titkos azonosítóját* vagy URL-címét. A Azure Resource Manager sablon bemenetként Key Vault. Ezután a sablon lekéri a fürtöt a Service Fabric-fürt telepítésekor.

> [!IMPORTANT]  
> A Service Fabric használatával történő használathoz a PowerShellt kell használnia a Key Vaulthoz. Ne használja a portált.  

A következő szkripttel hozza létre a Key Vault, és adja hozzá a *fürt tanúsítványát* . (Lásd az [előfeltételeket](#prerequisites).) A szkript futtatása előtt tekintse át a minta parancsfájlt, és frissítse a jelzett paramétereket a környezetének megfelelően. Ez a szkript a Azure Resource Manager-sablon számára szükséges értékeket is megjeleníti. 

> [!TIP]  
> Ahhoz, hogy a szkript sikeres legyen, egy nyilvános ajánlatnak kell lennie, amely tartalmazza a számítási, hálózati, tárolási és Key Vault szolgáltatásokat. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


További információ: [a Azure Stack Key Vault kezelése a PowerShell](azure-stack-key-vault-manage-powershell.md)-lel.

## <a name="deploy-the-marketplace-item"></a>A piactér-elemek üzembe helyezése

1. A felhasználói portálon válassza a **+ erőforrás** > létrehozása**számítási** > **Service Fabric fürt**lehetőséget. 

   ![Service Fabric fürt kiválasztása](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Az egyes lapokhoz, *például az*alapszintekhez, töltse ki a központi telepítési űrlapot. Használja az alapértelmezett értékeket, ha nem biztos benne, hogy egy értéket.

    A leválasztott Azure Stack vagy a Service Fabric egy másik verziójának központi telepítéséhez töltse le a Service Fabric központi telepítési csomagot és a hozzá tartozó futtatókörnyezet-csomagot, és tárolja Azure Stack blobon. Adja meg ezeket az értékeket a **Service Fabric központi telepítési csomag URL-címéhez** és a **Service Fabric futtatókörnyezet-csomag URL-** mezőihez.
    > [!NOTE]  
    > A Service Fabric és a hozzá tartozó SDK legújabb kiadása között kompatibilitási problémák léptek fel. A probléma megoldása érdekében adja meg a következő paramétereket a központi telepítési csomag URL-címéhez és a futásidejű csomag URL-címéhez. A központi telepítések egyébként nem fognak működni.
    > - Service Fabric központi telepítési csomag URL-címe:<https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>
    > - Service Fabric futásidejű csomag URL-címe:<https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>
    >
    > A leválasztott központi telepítések esetében töltse le ezeket a csomagokat a megadott helyről, és helyileg tárolja egy Azure Stack blobon.

   ![Alapadatok](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. A *hálózati beállítások* lapon megadhatja az alkalmazásai számára megnyitható portokat:

   ![Hálózati beállítások](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. A *Biztonság* lapon adja meg azokat az értékeket, amelyeket a [Azure Key Vault létrehozásával](#add-a-secret-to-key-vault) és a titkos kód feltöltésével kapott.

   A *rendszergazdai ügyféltanúsítvány ujjlenyomata*mezőben adja meg a *felügyeleti ügyféltanúsítvány*ujjlenyomatát. (Lásd az [előfeltételeket](#prerequisites).)
   
   - Forrás Key Vault:  Adja meg `keyVault id` a teljes karakterláncot a parancsfájl eredményeiből. 
   - Fürt tanúsítványának URL-címe: Adja meg a teljes URL- `Secret Id` címet a parancsfájl eredményei közül. 
   - Fürt tanúsítványának ujjlenyomata: Adja meg a *fürt tanúsítványának ujjlenyomatát* a parancsfájl eredményei közül.
   - Kiszolgálói tanúsítvány URL-címe: Ha a fürt tanúsítványának külön tanúsítványát kívánja használni, töltse fel a tanúsítványt egy kulcstartóba, és adja meg a titkos kulcs teljes URL-címét. 
   - Kiszolgálói tanúsítvány ujjlenyomata: A kiszolgálói tanúsítvány ujjlenyomatának megadása
   - Rendszergazdai ügyféltanúsítvány ujjlenyomatai megfelelnek: Itt adhatja meg az előfeltételekben létrehozott *rendszergazdai ügyféltanúsítvány ujjlenyomatát* . 

   ![Parancsprogram kimenete](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Biztonság](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Fejezze be a varázslót, majd a **Létrehozás** gombra kattintva telepítse a Service Fabric-fürtöt.



## <a name="access-the-service-fabric-cluster"></a>A Service Fabric-fürt elérése
A Service Fabric-fürtöt a Service Fabric Explorer vagy a Service Fabric PowerShell használatával érheti el.


### <a name="use-service-fabric-explorer"></a>Service Fabric Explorer használata
1.  Győződjön meg arról, hogy a böngésző hozzáfér a rendszergazdai ügyféltanúsítványt, és képes hitelesíteni a Service Fabric-fürtöt.  

    a. Nyissa meg az Internet Explorert, és lépjen az Internetbeállítások**tartalmi** > **tanúsítványok** **lehetőségre** > .
  
    b. A tanúsítványok lapon válassza az **Importálás** lehetőséget a *tanúsítvány importálása varázsló*elindításához, majd kattintson a **tovább**gombra. Az *Importálandó fájl* lapon kattintson a **Tallózás**gombra, és válassza ki a Azure Resource Manager sablonhoz megadott **rendszergazdai ügyféltanúsítványt** .
        
       > [!NOTE]  
       > Ez a tanúsítvány nem a Key Vaulthoz korábban hozzáadott fürtözött tanúsítvány.  

    c. Győződjön meg arról, hogy a fájlkezelő ablak bővítmény legördülő menüjében a "személyes információcsere" lehetőség van kiválasztva.  

       ![Személyes információcsere](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. A *tanúsítványtároló* lapon válassza a **személyes**lehetőséget, majd fejezze be a varázslót.  
       ![Tanúsítványtároló](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. A Service Fabric-fürt teljes tartománynevének megkeresése:  

    a. Lépjen a Service Fabric-fürthöz társított erőforráscsoporthoz, és keresse meg a *nyilvános IP-cím* erőforrást. Válassza ki a nyilvános IP-címhez társított objektumot a *nyilvános IP-cím* panel megnyitásához.  

      ![Nyilvános IP-cím](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. A nyilvános IP-cím panelen a teljes tartománynév *DNS-névként*jelenik meg.  

      ![DNS-név](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. A Service Fabric Explorer URL-címének és az ügyfél-kapcsolódási végpontnak a megkereséséhez tekintse át a Template deployment eredményét.

1. A böngészőben nyissa <https://*FQDN*:19080>meg a következőt:. Cserélje le a *teljes tartománynevet* a Service Fabric-fürt teljes tartománynevére a 2. lépésben.   
   Ha önaláírt tanúsítványt használt, a rendszer figyelmeztetést kap arról, hogy a kapcsolatok nem biztonságosak. A webhely folytatásához válassza a **További információk**lehetőséget, majd lépjen a **weblapra**. 

1. A helyhez való hitelesítéshez ki kell választania a használni kívánt tanúsítványt. Válassza a **további lehetőségek**lehetőséget, válassza ki a megfelelő tanúsítványt, majd kattintson az **OK** gombra a Service Fabric Explorerhoz való kapcsolódáshoz. 

   ![Hitelesítés](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>A Service Fabric PowerShell használata

1. Telepítse a *Microsoft Azure Service FABRIC SDK* -t a [fejlesztési környezet előkészítése a Windowsban](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) az Azure Service Fabric dokumentációjában.  

1. A telepítés befejezése után konfigurálja a rendszerkörnyezeti változókat annak biztosítására, hogy a Service Fabric parancsmagok elérhetők legyenek a PowerShellből.  
    
    a. Nyissa meg a **Vezérlőpult** > **rendszer és biztonsági** > **rendszer**elemét, majd válassza a **Speciális rendszerbeállítások**lehetőséget.  
    
      ![Vezérlőpult](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. A *rendszer tulajdonságai párbeszédpanel* **speciális** lapján válassza a **környezeti változók**lehetőséget.  

    c. A *rendszerváltozók*esetében szerkessze az **elérési utat** , és győződjön meg arról, hogy a **\\C:\\Program Files Microsoft Service Fabric\\bin\\Fabric\\Fabric. a kód** a lista elején található. környezeti változók.  

      ![Környezeti változók listája](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. A környezeti változók sorrendjének módosítása után indítsa újra a PowerShellt, majd futtassa a következő PowerShell-parancsfájlt, hogy hozzáférjen a Service Fabric-fürthöz:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Nincs *https://* a fürt neve előtt a parancsfájlban. A 19000-es portot kötelező megadni.

## <a name="next-steps"></a>További lépések

[A Kubernetes üzembe helyezése Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
