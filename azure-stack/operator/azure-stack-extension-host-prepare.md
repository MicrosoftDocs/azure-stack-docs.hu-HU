---
title: Felkészülés a bővítmény gazdagépére Azure Stack hub-ban | Microsoft Docs
description: Megtudhatja, hogyan készítheti elő a bővítmények gazdagépét Azure Stack hub-ban, amely a 1808-es verzió után automatikusan engedélyezve van egy Azure Stack hub-frissítési csomagon keresztül.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 10/02/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 45a26354edb7939a5fbc241bb5bcaf5d9db8edf3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818389"
---
# <a name="prepare-for-extension-host-in-azure-stack-hub"></a>A bővítmény-gazdagép előkészítése Azure Stack központban

A bővítmény-gazdagép a szükséges TCP/IP-portok számának csökkentésével biztonságossá teszi Azure Stack hubot. Ez a cikk az 1808-es frissítés után az Azure Stack hub-frissítési csomagon keresztül automatikusan engedélyezett bővítmény-gazdagépre vonatkozó Azure Stack hub előkészítését vizsgálja. Ez a cikk a 1808-es, 1809-as és 1811-es Azure Stack hub-frissítésekre vonatkozik.

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A bővítmény-gazdagép két új tartományi névteret valósít meg az egyes portál-bővítmények egyedi gazdagép-bejegyzéseinek biztosításához. Az új tartományi névtereknek két további helyettesítő tanúsítványra van szükségük a biztonságos kommunikáció biztosításához.

A tábla az új névtereket és a hozzájuk tartozó tanúsítványokat jeleníti meg:

| Telepítési mappa | Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN) | Hatókör (régiónként) | Altartomány névtere |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Felügyeleti bővítmény gazdagépe | *.adminhosting.\<régió >.\<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Felügyeleti bővítmény gazdagépe | adminhosting.\<régió >.\<FQDN > |
| Nyilvános kiterjesztésű gazdagép | *. hosting.\<régió >.\<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Nyilvános kiterjesztésű gazdagép | üzemeltetési.\<régió >.\<FQDN > |

A tanúsítványokra vonatkozó részletes követelményekért lásd: [Azure stack hub nyilvános kulcsokra épülő infrastruktúrájának tanúsítványára vonatkozó követelmények](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Tanúsítvány-aláírási kérelem létrehozása

Az Azure Stack hub Readiness-ellenőrző eszköz lehetővé teszi tanúsítvány-aláírási kérelem létrehozását a két új és szükséges SSL-tanúsítványhoz. Kövesse az [Azure stack hub-tanúsítványok aláírási kérelmének létrehozása](azure-stack-get-pki-certs.md)című cikkben ismertetett lépéseket.

> [!Note]  
> Ezt a lépést az SSL-tanúsítványok igénylésének módjától függően kihagyhatja.

## <a name="validate-new-certificates"></a>Új tanúsítványok ellenőrzése

1. Nyissa meg a PowerShellt emelt szintű engedélyekkel a hardver életciklus-gazdagépen vagy az Azure Stack hub felügyeleti munkaállomáson.
2. Futtassa a következő parancsmagot az Azure Stack hub Readiness-ellenőrző eszköz telepítéséhez:

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Futtassa a következő szkriptet a szükséges mappastruktúrát létrehozásához:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Ha Azure Active Directory összevont szolgáltatásokkal (AD FS) végzi a telepítést, a következő könyvtárakat hozzá kell adni a **$directorieshoz** a parancsfájlban: `ADFS`, `Graph`.

4. Helyezze el a meglévő tanúsítványokat, amelyeket jelenleg a Azure Stack hub-ban használ a megfelelő címtárakban. Tegyük fel például, hogy a **rendszergazda ARM** -tanúsítvány a `Arm Admin` mappában található. Ezután helyezze az újonnan létrehozott üzemeltetési tanúsítványokat a `Admin extension host` és `Public extension host` címtárakba.
5. A tanúsítvány-ellenőrzési elindításához futtassa a következő parancsmagot:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Ellenőrizze a kimenetet, és ha minden tanúsítvány megfelel az összes tesztnek.


## <a name="import-extension-host-certificates"></a>Extension Host-tanúsítványok importálása

A következő lépésekhez használjon olyan számítógépet, amely csatlakozhat a Azure Stack hub privilegizált végponthoz. Győződjön meg arról, hogy van hozzáférése az új tanúsítványfájl-fájlokhoz a számítógépről.

1. A következő lépésekhez használjon olyan számítógépet, amely csatlakozhat a Azure Stack hub privilegizált végponthoz. Győződjön meg arról, hogy az adott számítógépről fér hozzá az új tanúsítványfájl-fájlokhoz.
2. Nyissa meg a PowerShell ISE-t a következő parancsfájl-blokkok végrehajtásához.
3. Importálja a tanúsítványt a rendszergazdai üzemeltetési végpont számára.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importálja az üzemeltetési végpont tanúsítványát.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>DNS-konfiguráció frissítése

> [!Note]  
> Erre a lépésre nincs szükség, ha DNS-zónák delegálását használta a DNS-integrációhoz.
Ha az egyes gazdagépeken rekordok vannak konfigurálva Azure Stack hub-végpontok közzétételére, két további gazdagépet kell létrehoznia:

| IP | Gazdanév | Type (Típus) |
|----|------------------------------|------|
| \<IP-> | *. Adminhosting.\<régió >.\<FQDN > | A |
| \<IP-> | *. Üzemeltetési.\<régió >.\<FQDN > | A |

A lefoglalt IP-címeket a **Get-AzureStackStampInformation**parancsmag futtatásával kérheti le a privilegizált végpont használatával.

### <a name="ports-and-protocols"></a>Portok és protokollok

A [Azure stack hub Datacenter-integráció – közzétételi végpontok azokat](azure-stack-integrate-endpoints.md) a portokat és protokollokat fedik le, amelyeken bejövő Azure stack kommunikációra van szükség a bővítmény gazdagépének bevezetéséhez.

### <a name="publish-new-endpoints"></a>Új végpontok közzététele

Két új végpont szükséges a tűzfalon keresztül történő közzétételhez. A nyilvános VIP-készletből lefoglalt IP-címek a következő kóddal olvashatók be, amelyet az Azure Stack hub-környezet emelt [szintű végpontján](azure-stack-privileged-endpoint.md)kell futtatni.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Hub Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Minta kimenet

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Ezt a módosítást a bővítmény gazdagépének engedélyezése előtt végezze el. Ez lehetővé teszi, hogy az Azure Stack hub-portálok folyamatosan elérhetők legyenek.

| Végpont (VIP) | Protocol (Protokoll) | Portok |
|----------------|----------|-------|
| Rendszergazdai üzemeltetés | HTTPS | 443 |
| Üzemeltetés | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Meglévő közzétételi szabályok frissítése (a bővítmény gazdagépének engedélyezése után)

> [!Note]  
> Az 1808 Azure Stack hub frissítési csomagja még **nem** engedélyezi a bővítmény gazdagépét. Lehetővé teszi a bővítmények gazdagépének előkészítését a szükséges tanúsítványok importálásával. Ne zárjunk be minden portot, mielőtt az 1808-es frissítés után automatikusan engedélyezve van a bővítmény állomása egy Azure Stack hub-frissítési csomagon keresztül.

A meglévő tűzfalszabályok következő meglévő végpont-portjait be kell zárni.

> [!Note]  
> A sikeres ellenőrzés után a portok bezárását javasoljuk.

| Végpont (VIP) | Protocol (Protokoll) | Portok |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portál (rendszergazda) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portál (felhasználó) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (rendszergazda) | HTTPS | 30024 |
| Azure Resource Manager (felhasználó) | HTTPS | 30024 |

## <a name="next-steps"></a>Következő lépések

- További információ a [tűzfal-integrációról](azure-stack-firewall.md).
- Tudnivalók a [Azure stack hub-tanúsítványok aláírási kérelmének létrehozásáról](azure-stack-get-pki-certs.md).
