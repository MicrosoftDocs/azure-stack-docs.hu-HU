---
title: Azure Stack hub PKI-tanúsítványok előkészítése üzembe helyezéshez vagy elforgatáshoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan készítheti elő a PKI-tanúsítványokat Azure Stack hub üzembe helyezéséhez vagy a titkok elforgatásához.
author: BryanLa
ms.topic: how-to
ms.date: 10/19/2020
ms.author: bryanla
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 863a10ced2bf4c9ee4efef29472aab595ece4aa4
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231251"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Azure Stack hub PKI-tanúsítványok előkészítése üzembe helyezéshez vagy elforgatáshoz

> [!NOTE]
> Ez a cikk csak a külső tanúsítványok előkészítésére vonatkozik, amelyek a külső infrastruktúrán és szolgáltatásokon található végpontok védelmére szolgálnak. A belső tanúsítványokat külön kezelik a [tanúsítvány-elforgatási folyamat](azure-stack-rotate-secrets.md)során.

A [hitelesítésszolgáltatótól beszerzett](azure-stack-get-pki-certs.md) tanúsítványfájl-fájlokat importálni és exportálni kell a Azure stack hub tanúsítványára vonatkozó követelményeknek megfelelő tulajdonságokkal.

Ebből a cikkből megtudhatja, hogyan importálhat, csomagolhat és érvényesítheti a külső tanúsítványokat, hogy előkészítse Azure Stack hub üzembe helyezését vagy a titkok rotációját. 

## <a name="prerequisites"></a>Előfeltételek

A rendszernek meg kell felelnie a következő előfeltételeknek, mielőtt a PKI-tanúsítványokat becsomagolja egy Azure Stack hub telepítéséhez:

- A hitelesítésszolgáltatótól visszaadott tanúsítványok tárolása egyetlen címtárban,. cer formátumban történik (egyéb konfigurálható formátumok, például. CERT,. SST vagy. pfx).
- Windows 10 vagy Windows Server 2016 vagy újabb
- Használja ugyanazt a rendszerét, amely a tanúsítvány-aláírási kérést generálta (kivéve, ha a PFXs-be előre csomagolt tanúsítványra van megcélozva).

Folytassa a megfelelő [előkészítési tanúsítványok (Azure stack Readiness-ellenőrző)](#prepare-certificates-azure-stack-readiness-checker) vagy a [tanúsítványok előkészítése (manuális lépések)](#prepare-certificates-manual-steps) szakaszban.

## <a name="prepare-certificates-azure-stack-readiness-checker"></a>Tanúsítványok előkészítése (Azure Stack Readiness-ellenőrző)

Ezekkel a lépésekkel a Azure Stack készenléti ellenőrző PowerShell-parancsmagok használatával csomagolhatja ki a tanúsítványokat:

1. Telepítse az Azure Stack Readiness-ellenőrző modult egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```
2. A tanúsítványfájl **elérési útjának** megadása. Például:

    ```powershell  
        $Path = "$env:USERPROFILE\Documents\AzureStack"
    ```

3. Deklarálja a **pfxPassword**. Például:

    ```powershell  
        $pfxPassword = Read-Host -AsSecureString -Prompt "PFX Password"
    ```
4. Állapítsa meg azt a **ExportPath** , amelybe az eredményül kapott PFXs exportálni fogja. Például:

    ```powershell  
        $ExportPath = "$env:USERPROFILE\Documents\AzureStack"
    ```

5. Tanúsítványok konvertálása Azure Stack hub-tanúsítványokra. Például:

    ```powershell  
        ConvertTo-AzsPFX -Path $Path -pfxPassword $pfxPassword -ExportPath $ExportPath
    ```
8.  Tekintse át a kimenetet:

    ```powershell  
    ConvertTo-AzsPFX v1.2005.1286.272 started.

    Stage 1: Scanning Certificates
        Path: C:\Users\[*redacted*]\Documents\AzureStack Filter: CER Certificate count: 11
        adminmanagement_east_azurestack_contoso_com_CertRequest_20200710235648.cer
        adminportal_east_azurestack_contoso_com_CertRequest_20200710235645.cer
        management_east_azurestack_contoso_com_CertRequest_20200710235644.cer
        portal_east_azurestack_contoso_com_CertRequest_20200710235646.cer
        wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20200710235649.cer
        wildcard_adminvault_east_azurestack_contoso_com_CertRequest_20200710235642.cer
        wildcard_blob_east_azurestack_contoso_com_CertRequest_20200710235653.cer
        wildcard_hosting_east_azurestack_contoso_com_CertRequest_20200710235652.cer
        wildcard_queue_east_azurestack_contoso_com_CertRequest_20200710235654.cer
        wildcard_table_east_azurestack_contoso_com_CertRequest_20200710235650.cer
        wildcard_vault_east_azurestack_contoso_com_CertRequest_20200710235647.cer

    Detected ExternalFQDN: east.azurestack.contoso.com

    Stage 2: Exporting Certificates
        east.azurestack.contoso.com\Deployment\ARM Admin\ARMAdmin.pfx
        east.azurestack.contoso.com\Deployment\Admin Portal\AdminPortal.pfx
        east.azurestack.contoso.com\Deployment\ARM Public\ARMPublic.pfx
        east.azurestack.contoso.com\Deployment\Public Portal\PublicPortal.pfx
        east.azurestack.contoso.com\Deployment\Admin Extension Host\AdminExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\KeyVaultInternal\KeyVaultInternal.pfx
        east.azurestack.contoso.com\Deployment\ACSBlob\ACSBlob.pfx
        east.azurestack.contoso.com\Deployment\Public Extension Host\PublicExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\ACSQueue\ACSQueue.pfx
        east.azurestack.contoso.com\Deployment\ACSTable\ACSTable.pfx
        east.azurestack.contoso.com\Deployment\KeyVault\KeyVault.pfx

    Stage 3: Validating Certificates.

    Validating east.azurestack.contoso.com-Deployment-AAD certificates in C:\Users\[*redacted*]\Documents\AzureStack\east.azurestack.contoso.com\Deployment 

    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
        ...
    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    ConvertTo-AzsPFX Completed
    ```
    > [!NOTE]
    > További használat esetén a Get-Help ConvertTo-AzsPFX – teljes mértékben használható a további használathoz, például a különböző tanúsítvány-formátumok ellenőrzésének vagy szűrésének letiltásához.

    A sikeres ellenőrzési tanúsítványokat a további lépések nélkül lehet üzembe helyezésre vagy rotációra feltüntetni.

## <a name="prepare-certificates-manual-steps"></a>Tanúsítványok előkészítése (manuális lépések)

Ezekkel a lépésekkel az új Azure Stack hub PKI-tanúsítványokhoz tartozó tanúsítványokat manuális lépések használatával csomagolhatja ki.

### <a name="import-the-certificate"></a>A tanúsítvány importálása

1. Másolja a [választott hitelesítésszolgáltatótól beszerzett](azure-stack-get-pki-certs.md) eredeti tanúsítványokat a telepítési gazdagép egyik könyvtárába. 
   > [!WARNING]
   > Ne másolja a közvetlenül a HITELESÍTÉSSZOLGÁLTATÓ által biztosított fájlokból a már importált, exportált vagy módosított fájlokat.

1. Kattintson a jobb gombbal a tanúsítványra, és válassza a **tanúsítvány telepítése** vagy a **pfx telepítése** lehetőséget attól függően, hogy a tanúsítványt hogyan szállították le a hitelesítésszolgáltatótól.

1. A **tanúsítvány importálása varázslóban** válassza a **helyi gép** importálási helyként lehetőséget. Kattintson a **Tovább** gombra. A következő képernyőn kattintson ismét a Tovább gombra.

    ![Helyi számítógép importálási helye a tanúsítványhoz](./media/prepare-pki-certs/1.png)

1. Válassza a **minden tanúsítvány elhelyezése a következő tárban** lehetőséget, majd válassza a **vállalati megbízhatóság** lehetőséget a helyként. A tanúsítványtároló kiválasztása párbeszédpanel bezárásához kattintson **az OK gombra** , majd válassza a **tovább** lehetőséget.

   ![A tanúsítványtároló konfigurálása a tanúsítványok importálásához](./media/prepare-pki-certs/3.png)

   a. Ha PFX-t importál, egy további párbeszédablak jelenik meg. A **titkos kulcs védelme** lapon adja meg a tanúsítványfájl jelszavát, majd engedélyezze a **kulcs megjelölését exportálhatóként.** lehetőség, amely lehetővé teszi a kulcsok későbbi biztonsági mentését vagy továbbítását. Kattintson a **Tovább** gombra.

   ![Kulcs megjelölése exportálhatóként](./media/prepare-pki-certs/2.png)

1. Az importálás befejezéséhez kattintson a **Befejezés** gombra.

> [!NOTE]
> Azure Stack hub tanúsítványának importálása után a rendszer a tanúsítvány titkos kulcsát PKCS 12 fájlként (PFX) tárolja a fürtözött tárolón.

### <a name="export-the-certificate"></a>A tanúsítvány exportálása

Nyissa meg a Tanúsítványkezelő MMC-konzolt, és kapcsolódjon a helyi számítógép tanúsítványtárolóhoz.

1. Nyissa meg a Microsoft Management Console-t. A Windows 10-es konzol megnyitásához kattintson a jobb gombbal a **Start menü** **Futtatás** parancsára, majd írja be az **MMC** parancsot, majd nyomja le az ENTER billentyűt.

2. Válassza a **fájl**  >  **beépülő modul hozzáadása/eltávolítása** elemet, majd válassza a **tanúsítványok** lehetőséget, és válassza a **Hozzáadás** lehetőséget.

    ![Tanúsítványkezelő beépülő modul hozzáadása a Microsoft Management Console-ban](./media/prepare-pki-certs/mmc-2.png)

3. Válassza a **számítógépfiók** lehetőséget, majd kattintson a **Tovább gombra**. Válassza a **helyi számítógép** lehetőséget, majd fejezze be a **befejezést**. A Snap-In hozzáadása/eltávolítása lap bezárásához kattintson **az OK gombra** .

    ![Fiók kiválasztása a tanúsítványok hozzáadása beépülő modulhoz a Microsoft Management Console-ban](./media/prepare-pki-certs/mmc-3.png)

4. Tallózással keresse meg a **tanúsítványok**  >  **vállalati megbízhatósági**  >  **tanúsítványának helyét**. Győződjön meg arról, hogy a jobb oldalon megjelenik a tanúsítvány.

5. A Tanúsítványkezelő konzol tálcán válassza a **műveletek**  >  **minden feladat**  >  **Exportálás** lehetőséget. Kattintson a **Tovább** gombra.

   > [!NOTE]
   > Attól függően, hogy hány Azure Stack hub-tanúsítvány van, előfordulhat, hogy a folyamatot többször kell végrehajtania.

6. Válassza **az igen, a titkos kulcs exportálása** lehetőséget, majd kattintson a **tovább** gombra.

7. A fájlformátum exportálása szakaszban:
    
   - **Ha lehetséges, jelölje be a tanúsítványban szereplő összes tanúsítvány belefoglalása** jelölőnégyzetet.  
   - Válassza **az összes kibővített tulajdonság exportálása** lehetőséget.  
   - Válassza a **tanúsítvány adatvédelem engedélyezése** lehetőséget.  
   - Kattintson a **Tovább** gombra.  
    
     ![Tanúsítvány exportálása varázsló kiválasztott beállításokkal](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Válassza a **jelszó** lehetőséget, és adja meg a tanúsítványok jelszavát. Hozzon létre egy jelszót, amely megfelel a következő jelszó-összetettségi követelményeknek:

    * Legalább nyolc karakter hosszúnak kell lennie.
    * A következő karakterek legalább hármat tartalmazhatnak: nagybetűs, kisbetűk, 0-9, speciális karakterek, alfabetikus karakter, amely nem nagybetűs vagy kisbetűs.

    Jegyezze fel ezt a jelszót. Ezt fogja használni központi telepítési paraméterként.

9. Kattintson a **Tovább** gombra.

10. Válassza ki az exportálandó PFX-fájl nevét és helyét. Kattintson a **Tovább** gombra.

11. Válassza a **Befejezés** gombot.

## <a name="next-steps"></a>Következő lépések

[PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)
