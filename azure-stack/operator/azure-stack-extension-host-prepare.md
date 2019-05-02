---
title: Azure stack-bővítmény gazdagép előkészítése |} A Microsoft Docs
description: Ismerje meg, fel a bővítmény gazdagép, a későbbi Azure Stack csomag automatikusan engedélyezve van.
services: azure-stack
keywords: ''
author: WenJason
ms.author: v-jay
origin.date: 03/07/2019
ms.date: 04/29/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: digimobile
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 23f91454027b3073498ef3c1b4a388cca0c88bab
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64291693"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Azure stack-bővítmény gazdagép előkészítése

A bővítmény fogadó Azure Stack védi a szükséges TCP/IP-portok számának csökkentésével. Ez a cikk megvizsgálja a bővítmény-gazdagépet, amely a 1808 frissítés után automatikusan engedélyezve van az Azure Stack-frissítési csomag segítségével az Azure Stack előkészítése. Ez a cikk az Azure Stack frissítéseinek 1808 1809 és 1811 vonatkozik.

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A bővítmény gazdagép két új tartományok névterek egyedi állomásbejegyzéssel portál kiterjesztési garantálása érdekében valósít meg. Az új tartomány névterek szükség két további helyettesítő tanúsítványok biztonságos kommunikáció biztosítása érdekében.

A táblázat bemutatja az új névterek és a kapcsolódó tanúsítványok:

| Telepítési mappa | Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosneveket (SAN) | Hatókör (régiónként) | SubDomain namespace |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Rendszergazdai kiterjesztés gazdagép | *.adminhosting. \<régió >. \<teljesen minősített tartományneve > (altartományokra is kibővített SSL-tanúsítványok) | Rendszergazdai kiterjesztés gazdagép | adminhosting.\<region>.\<fqdn> |
| A bővítmény nyilvános állomás | *.hosting.\<region>.\<fqdn> (Wildcard SSL Certificates) | A bővítmény nyilvános állomás | hosting.\<region>.\<fqdn> |

Tekintheti meg a részletes tanúsítványokra vonatkozó követelményeket a [Azure Stack nyilvános kulcsokra épülő infrastruktúra tanúsítványkövetelmények](azure-stack-pki-certs.md) cikk.

## <a name="create-certificate-signing-request"></a>Tanúsítvány-aláírási kérelem létrehozása

Az Azure Stack készültségi ellenőrző eszköz lehetővé teszi a tanúsítvány-aláírási kérelem számára a két új, a szükséges SSL-tanúsítványok létrehozásához. Kövesse a cikk a [Azure Stack-aláírási kérelmet. generációs tanúsítványok](azure-stack-get-pki-certs.md).

> [!Note]  
> Előfordulhat, hogy kihagyja ezt a lépést, attól függően, hogyan kért az SSL-tanúsítványokat.

## <a name="validate-new-certificates"></a>Új tanúsítványok ellenőrzése

1. Nyissa meg a Powershellt a hardver életciklus gazdagépen vagy az Azure Stack felügyeleti munkaállomás emelt szintű engedélyekkel.
2. Az Azure Stack készültségi ellenőrző eszköz telepítése a következő parancsmag futtatásával.

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Futtassa a következő parancsfájlt a szükséges mappa-struktúra létrehozása:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Az Azure Active Directory összevont szolgáltatások (AD FS) központi telepítésekor a következő könyvtárak fel kell venni **$directories** a szkriptben: `ADFS`, `Graph`.

4. Helyezze el a meglévő tanúsítványok, amelyet jelenleg használ az Azure Stackben, a megfelelő címtárakban. Például helyezze a **rendszergazdai ARM** -tanúsítványt a `Arm Admin` mappát. Majd helyezzük az újonnan létrehozott szolgáltatási tanúsítványok a `Admin extension host` és `Public extension host` könyvtárak.
5. Futtassa a tanúsítvány-ellenőrzés indítása a következő parancsmagot:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Ellenőrizze, hogy a kimenet és az összes tanúsítvány adja át az összes teszt.


## <a name="import-extension-host-certificates"></a>Bővítmény gazdagép tanúsítványok importálása

Használjon egy számítógépet, amely képes kapcsolódni a következő lépéseket az Azure Stack az emelt szintű végpontját. Győződjön meg arról, hogy érheti el az új fájlokat erről a számítógépről.

1. Használjon egy számítógépet, amely képes kapcsolódni a következő lépéseket az Azure Stack az emelt szintű végpontját. Ellenőrizze, hogy erről a számítógépről az új tanúsítvány fájlok elérését.
2. Nyissa meg a PowerShell ISE-ben, hajtsa végre a következő parancsfájl-blokkokban
3. Importálhatja a tanúsítványt a felügyeleti végpontot üzemeltető.

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
4. Importálja a tanúsítványt, a üzemeltetési végpont.
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
> Ebben a lépésben nincs szükség, ha a DNS-zónadelegálás használja a DNS-integráció.
Ha az egyéni gazdagépeken A rekordok közzététele az Azure Stack-végpontok konfigurálása megtörtént, két további gazdagépcsoport A rekordok létrehozásához szüksége:

| IP | Gazdanév | Típus |
|----|------------------------------|------|
| \<IP> | *. Adminhosting. \<Régió >. \<Teljesen minősített Tartományneve > | A |
| \<IP> | *. Üzemeltetési. \<Régió >. \<Teljesen minősített Tartományneve > | A |

Lefoglalt IP-címek rendszerjogosultságú végpont használatával a parancsmag futtatásával kérhető **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Portok és protokollok

A cikk [adatközpont integrációja az Azure Stack - végpontok közzététele](azure-stack-integrate-endpoints.md), a portok és protokollok közzététele az Azure Stack a bővítmény gazdagép bevezetést megelőző bejövő kommunikációt igénylő ismerteti.

### <a name="publish-new-endpoints"></a>Új végpontok közzététele

Kötelező a tűzfalon keresztül nyilvánosságra két új végpontja van. A nyilvános VIP-címkészlet lefoglalt IP-címekről lehet beolvasni a következő kóddal kell futtatni az Azure Stack [környezet a rendszerjogosultságú végpont](azure-stack-privileged-endpoint.md).

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
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

#### <a name="sample-output"></a>Kimeneti példa

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Ezt a módosítást a bővítmény gazdagép engedélyezése előtt. Ez lehetővé teszi az Azure Stack-portálokkal, folyamatosan elérhetők legyenek.

| Endpoint (VIP) | Protocol | Portok |
|----------------|----------|-------|
| Admin Hosting | HTTPS | 443 |
| Üzemeltetés | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Frissítse a meglévő közzétételi szabályokat (Post engedélyezése bővítmény gazdagép)

> [!Note]  
> A 1808 Azure Stack frissítési csomag does **nem** még bővítmény gazdagép engedélyezése. Lehetővé teszi a bővítmény gazdagép előkészítése a szükséges tanúsítványok importálásával. Ne zárja be a portokat, mielőtt bővítmény gazdagép automatikusan engedélyezve van az Azure Stack frissíteni csomag keresztül 1808 frissítés után.

A következő meglévő endpoint portokat a meglévő tűzfalszabályok zártnak kell lenniük.

> [!Note]  
> Javasoljuk, hogy ezeket a portokat bezárása után ellenőrzést.

| Endpoint (VIP) | Protocol | Portok |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portál (rendszergazda) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portál (felhasználó) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Az Azure Resource Manager (rendszergazda) | HTTPS | 30024 |
| Az Azure Resource Manager (felhasználó) | HTTPS | 30024 |

## <a name="next-steps"></a>További lépések

- Ismerje meg [tűzfal-integráció](azure-stack-firewall.md).
- Ismerje meg [Azure Stack-tanúsítványok aláíró kérés létrehozása](azure-stack-get-pki-certs.md)
