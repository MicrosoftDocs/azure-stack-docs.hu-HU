---
title: Az Azure Stackben biztonságos Service Fabric-fürt üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy biztonságos Service Fabric-fürtön az Azure Stackben
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
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 29adea1cb8c07acc309ef7aae2856b9a14759de3
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985890"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Az Azure Stack a Service Fabric-fürt üzembe helyezése

Használja a **Service Fabric-fürt** elem a biztonságos Service Fabric-fürtön az Azure Stack üzembe helyezése az Azure piactérről. 

A Service Fabric használatával kapcsolatos további információkért lásd: [áttekintése az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) és [Service Fabric-fürtök biztonsági forgatókönyveit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), az Azure dokumentációjában olvashatók.

A Service Fabric-fürtön az Azure Stackben Microsoft.ServiceFabric erőforrás-szolgáltató nem használja. Az Azure Stackben, a Service Fabric-fürt egy előre telepített szoftverfrissítési csoport Desired State Configuration (DSC) használatával virtuális gépek méretezési.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek a Service Fabric-fürt üzembe helyezéséhez:
1. **Fürt tanúsítvány**  
   Ez az a KeyVault Service Fabric üzembe helyezésekor hozzáadása X.509-tanúsítvány. 
   - A **CN** az ehhez a tanúsítványhoz meg kell egyeznie a teljesen minősített tartomány nevét (FQDN), a Service Fabric-fürtöt hoz létre. 
   - A tanúsítvány formátuma PFX, kell lennie, mint a nyilvános és titkos kulcsok szükségesek. 
     Lásd: [követelmények](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) a kiszolgálóoldali tanúsítvány létrehozásához.

     > [!NOTE]  
     > A x.509 tanúsítvány egy önaláírt tanúsítványt inplace tesztelési célokra használható. Önaláírt tanúsítványok nem kell egyeznie a fürt teljes Tartománynevét.

1. **Rendszergazdai ügyfél tanúsítványa** Ez az a tanúsítvány, amelyet az ügyfél a Service Fabric-fürtöt, amely is lehet önaláírt hitelesítést fog használni. Lásd: [követelmények](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) az ügyféltanúsítvány létrehozásához.

1. **Az Azure Stack piactéren elérhetőnek kell lennie a következőkkel:**
    - **A Windows Server 2016** – a sablon a Windows Server 2016 rendszerképet használ a fürt létrehozásához.  
    - **Egyéni szkriptek futtatására szolgáló bővítmény** – virtuálisgép-bővítmény a Microsoft.  
    - **PowerShell célállapot-konfigurációs szakasz** – virtuálisgép-bővítmény a Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz
Service Fabric-fürt üzembe helyezéséhez meg kell adnia a megfelelő KeyVault *titkos azonosító* vagy a Service Fabric-fürt URL-CÍMÉT. Az Azure Resource Manager-sablon egy KeyVault fogadja bemeneti adatként. A sablon majd lekéri a fürt tanúsítványt, a Service Fabric-fürt telepítése során.

> [!IMPORTANT]  
> A KeyVault titkos kód hozzáadása a Service Fabric segítségével a PowerShell használatával kell. Ne használja a portálon.  

A következő szkript használatával a kulcstartó létrehozása és hozzáadása a *fürttanúsítvány* rá. (Lásd a [Előfeltételek](#prerequisites).) A parancsfájl futtatása előtt tekintse át a minta parancsfájl, és módosítsa a megadott paramétereket, a környezetéhez. Ez a szkript is kimenete az értékeket, meg kell adnia az Azure Resource Manager-sablon. 

> [!TIP]  
> A szkript sikeres, mielőtt egy nyilvános ajánlat, amely magában foglalja a számítási, hálózati, tárolási és KeyVault szolgáltatásokat kell. 

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


További információkért lásd: [KeyVault kezelése az Azure Stackben a PowerShell-lel](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>A Piactéri elem üzembe helyezése

1. A felhasználói portálon lépjen a **+ erőforrás létrehozása** > **számítási** > **Service Fabric-fürt**. 

   ![Válassza ki a Service Fabric-fürt](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Minden olyan lap például *alapjai*, töltse ki az üzembe helyezés űrlapot. Használja az alapértelmezett értékeket, ha nem biztos egy értéket. Válassza ki **OK** , folytassa a következő oldalra:

   ![Alapvető beállítások](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Az a *hálózati beállítások* lapon megadhat adott portok megnyitásához az alkalmazások számára:

   ![Hálózati beállítások](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Az a *biztonsági* lap, adja hozzá az értékeket webhelyről származó alkalmazásazonosítóra [létrehozása az Azure KeyVault](#add-a-secret-to-key-vault) , és töltse fel a titkos kulcsot.

   Az a *felügyeleti ügyfél tanúsítványának ujjlenyomata*, ujjlenyomatát adja meg a *rendszergazdai ügyfél tanúsítványa*. (Lásd a [Előfeltételek](#prerequisites).)
   
   - A forrás Key Vaultnak:  Adja meg a teljes *keyVault azonosítója* karakterláncot, a szkriptek eredményeit. 
   - Fürt tanúsítvány URL-címe: Adja meg a teljes URL-címet a *titkos kód azonosítója* a parancsfájl eredmények közül. 
   - Fürt tanúsítvány ujjlenyomata: Adja meg a *fürt tanúsítvány-ujjlenyomat* a parancsfájl eredmények közül.
   - Rendszergazdai ügyfél-tanúsítvány Ujjlenyomataival: Adja meg a *felügyeleti ügyfél tanúsítványának ujjlenyomata* létrehozott az előfeltételeket. 

   ![Parancsprogram kimenete](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Biztonság](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Fejezze be a varázslót, és válassza ki **létrehozás** a Service Fabric-fürt üzembe helyezéséhez.



## <a name="access-the-service-fabric-cluster"></a>Hozzáférés a Service Fabric-fürt
Elérheti a Service Fabric-fürthöz a Service Fabric Explorerben vagy a Service Fabric PowerShell használatával.


### <a name="use-service-fabric-explorer"></a>Service Fabric Explorert használjuk
1.  Ellenőrizze, hogy a rendszergazdai ügyfél tanúsítványa hozzáféréssel rendelkezik a böngészőt, és a Service Fabric-fürthöz hitelesíteni tudja.  

    a. Nyissa meg az Internet Explorert, majd **Internetbeállítások** > **tartalom** > **tanúsítványok**.
  
    b. A tanúsítványokat, jelölje be **importálás** elindításához a *Tanúsítványimportáló varázsló*, és kattintson a **tovább**. Az a *importálandó fájl* lapon kattintson **Tallózás**, és válassza ki a **rendszergazdai ügyfél tanúsítványa** , az Azure Resource Manager-sablon megadott.
        
       > [!NOTE]  
       > Ez a tanúsítvány nem volt korábban hozzáadott KeyVault fürttanúsítvány áll.  

    c. Győződjön meg arról, hogy a "Személyes információcsere" Fájlkezelő ablak a bővítmény legördülő listában kiválasztott.  

       ![Személyes információcsere](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Az a *tanúsítvány Store* lapon jelölje be **személyes**, majd fejezze be a varázslót.  
       ![Tanúsítványtároló](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Keresése a Service Fabric-fürt teljes Tartománynevét:  

    a. Nyissa meg az erőforráscsoporthoz társított a Service Fabric-fürt, és keresse meg a *nyilvános IP-cím* erőforrás. Jelölje ki az objektumot, nyissa meg a nyilvános IP-címet társítva a *nyilvános IP-cím* panelen.  

      ![Nyilvános IP-cím](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. A nyilvános IP-cím cím panelen a teljes Tartománynevet jeleníti meg *DNS-név*.  

      ![DNS-név](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Az URL-Címének megkeresése a Service Fabric Explorert, és az ügyfél csatlakozási végpontját, tekintse át a sablon telepítésének eredményeit.

1. A böngészőben nyissa meg a https://*FQDN*: 19080-as. Cserélje le *FQDN* a 2. lépés a Service Fabric-fürt teljes Tartománynevét.   
   Ha korábban már használta egy önaláírt tanúsítványt, kap egy figyelmeztetés, hogy a kapcsolat nem biztonságos. Lépjen a webhely, válassza ki a **több információt**, majd **folytassa a képernyőn látható weblapon**. 

1. Hitelesítéséhez a helyhez, jelöljön ki egy használni kívánt tanúsítványt. Válassza ki **további lehetőségek**, válassza ki a megfelelő tanúsítvány, és kattintson **OK** szeretne csatlakozni a Service Fabric Explorert. 

   ![Hitelesítés](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Service Fabric PowerShell-lel

1. Telepítse a *a Microsoft Azure Service Fabric SDK* a [a Windows a fejlesztési környezet előkészítését](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) az Azure Service Fabric dokumentációjában.  

1. A telepítés befejezése után ellenőrizze, hogy a Service Fabric-parancsmagok elérhetők-e a PowerShellben a rendszer környezeti változók konfigurálása.  
    
    a. Lépjen a **Vezérlőpult** > **rendszer és biztonság** > **rendszer**, majd válassza ki **Speciális rendszerbeállítások**.  
    
      ![Vezérlőpult](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Az a **speciális** lapján *Rendszertulajdonságok*válassza **környezeti változók**.  

    c. A *rendszerváltozók*, Szerkesztés **elérési** , és ellenőrizze, hogy **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric \\Fabric.Code** felső listáját, a környezeti változók.  

      ![Környezeti változók listájának](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Miután megváltoztatta a környezeti változók sorrendjét, indítsa újra a PowerShell, és futtassa a következő PowerShell-parancsfájl hozzáfér a Service Fabric-fürthöz:

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
   > Nincs nem *https://* előtt a szkriptben a fürt nevére. 19000 portot kötelező megadni.

## <a name="next-steps"></a>További lépések

[Az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)
