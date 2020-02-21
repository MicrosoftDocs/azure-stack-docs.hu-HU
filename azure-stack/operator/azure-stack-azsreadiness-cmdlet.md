---
title: A Start-AzsReadinessChecker parancsmag referenciája
description: PowerShell-parancsmag Súgó az Azure Stack hub Readiness-ellenőrző modulhoz.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 067164db905e6649def7ecd06b0e15d166d286b9
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509381"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>A Start-AzsReadinessChecker parancsmag referenciája

Modul: **Microsoft. AzureStack. ReadinessChecker**

Ez a modul csak egyetlen parancsmagot tartalmaz. A parancsmag egy vagy több üzembe helyezés előtti vagy karbantartási funkciót hajt végre Azure Stack hub számára.

## <a name="syntax"></a>Szintaxis

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Leírás

A **Start-AzsReadinessChecker** parancsmag ellenőrzi a tanúsítványokat, az Azure-fiókokat, az Azure-előfizetéseket és az Azure Active Directoryt (Azure ad). Futtassa az ellenőrzést az Azure Stack hub üzembe helyezése előtt, vagy Azure Stack hub karbantartási műveletei, például a titkos elforgatása előtt. A parancsmag használatával tanúsítvány-aláírási kérések is létrehozhatók az infrastruktúra-tanúsítványokhoz, és opcionálisan a Pásti tanúsítványokat is. Végezetül a parancsmag újracsomagolhatja a PFX-tanúsítványokat a gyakori csomagolási problémák elhárításához.

## <a name="examples"></a>Példák

### <a name="example-generate-certificate-signing-request"></a>Példa: tanúsítvány-aláírási kérelem előállítása

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Ebben a példában a `Start-AzsReadinessChecker` több tanúsítvány-aláírási kérést (munkatársakat) hoz létre olyan tanúsítványok számára, amelyek alkalmasak egy AD FS Azure Stack hub központi telepítésére, amely egy **keleti** régió nevével és a **azurestack.contoso.com**külső teljes tartománynevével rendelkezik.

### <a name="example-validate-certificates"></a>Példa: tanúsítványok ellenőrzése

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Ebben a példában a PFX-jelszó szükséges a biztonsághoz, és `Start-AzsReadinessChecker` ellenőrzi az Azure AD-példányhoz tartozó, a **keleti** régió nevére és a **azurestack.contoso.com**külső teljes tartománynevére érvényes tanúsítványok relatív mappájának **tanúsítványait** .

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Példa: tanúsítványok ellenőrzése központi telepítési adattal (üzembe helyezés és támogatás)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

Ebben az üzembe helyezési és támogatási példában a PFX-jelszó szükséges a biztonsághoz, és `Start-AzsReadinessChecker` ellenőrzi, hogy az üzemelő példányhoz tartozó, az identitást, a régiót és a külső teljes tartománynevet tartalmazó központi telepítésre **vonatkozó,** a központi telepítéshez létrehozott, telepítési adatok JSON-fájljából beolvasott tanúsítvány

### <a name="example-validate-paas-certificates"></a>Példa: Pásti-tanúsítványok ellenőrzése

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

Ebben a példában egy szórótábla elérési utakat és jelszavakat hoz létre az egyes Péter-tanúsítványokhoz. A tanúsítványok nem hagyhatók el. `Start-AzsReadinessChecker` ellenőrzi, hogy az egyes PFX-útvonalak léteznek-e, és a **keleti** régió és a külső FQDN **azurestack.contoso.com**használatával érvényesíti őket.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Példa: a Pásti-tanúsítványok érvényesítése telepítési adattal

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Ebben a példában egy szórótábla elérési utakat és jelszavakat hoz létre az egyes Péter-tanúsítványokhoz. A tanúsítványok nem hagyhatók el. `Start-AzsReadinessChecker` ellenőrzi, hogy az egyes PFX-útvonalak léteznek-e, és a központi telepítéshez létrehozott telepítési adatok JSON-fájljából beolvasva ellenőrzi azokat a régió és a külső FQDN használatával.

### <a name="example-validate-azure-identity"></a>Példa: az Azure Identity ellenőrzése

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatai szükségesek a biztonsághoz, és `Start-AzsReadinessChecker` ellenőrzi, hogy az Azure-fiók és az Azure AD érvényes-e egy Azure AD-példányhoz a **azurestack.contoso.com**-beli bérlői címtár nevével.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Példa: az Azure-identitás ellenőrzése központi telepítési adatokkal (üzembe helyezési támogatás)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatai szükségesek a biztonsághoz, és `Start-AzsReadinessChecker` ellenőrzi, hogy az Azure-fiók és az Azure AD érvényes-e egy Azure AD-telepítésre, ahol a **AzureCloud** és a **TenantName** a központi telepítéshez generált telepítési adatok JSON-fájljából származnak.

### <a name="example-validate-azure-registration"></a>Példa: az Azure-regisztráció ellenőrzése

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Ebben a példában az előfizetés tulajdonosának hitelesítő adatai szükségesek a biztonsághoz, és `Start-AzsReadinessChecker` ezután végrehajtja az érvényesítést az adott fiókkal és előfizetéssel, hogy az Azure Stack hub-regisztrációhoz is használható legyen.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Példa: az Azure-regisztráció ellenőrzése központi telepítési adattal (üzembe helyezési csapat)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában az előfizetés tulajdonosának hitelesítő adatai szükségesek a biztonsághoz, és a `Start-AzsReadinessChecker` ezután végrehajtja az érvényesítést az adott fiókkal és előfizetéssel annak biztosítása érdekében, hogy használható legyen Azure Stack hub-regisztrációhoz, ahol további részletek olvashatók az üzembe helyezéshez generált telepítési adatok JSON-fájljából.

### <a name="example-importexport-pfx-package"></a>Példa: PFX-csomag importálása/exportálása

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Ebben a példában a PFX-jelszó szükséges a biztonsághoz. Az SSL. pfx fájlt a rendszer importálja a helyi számítógép tanúsítványtárolóba, majd újra exportálja ugyanazzal a jelszóval, és a Ssl_new. pfx fájlként menti. Ez az eljárás akkor használatos, ha a tanúsítvány-ellenőrzési jelzők nem rendelkeznek a **helyi számítógép** attribútumával, a tanúsítványlánc megszakad, a nem releváns tanúsítványok szerepelnek a pfx-ben, vagy a tanúsítványlánc nem megfelelő sorrendben van megadva.

### <a name="example-view-validation-report-deployment-and-support"></a>Példa: ellenőrzési jelentés megtekintése (központi telepítés és támogatás)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Ebben a példában az üzembe helyezési vagy támogatási csapat megkapja a készültségi jelentést az ügyféltől (contoso), és `Start-AzsReadinessChecker` használatával tekinti meg a contoso által végrehajtott érvényesítési végrehajtások állapotát.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Példa: az ellenőrzési jelentés összefoglalásának megtekintése csak tanúsítvány-ellenőrzéshez (telepítés és támogatás)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Ebben a példában az üzembe helyezési vagy támogatási csapat megkapja a készültségi jelentést az ügyféltől (contoso), és a `Start-AzsReadinessChecker` használatával megtekinti a tanúsítvány-ellenőrzési végrehajtások contoso által végrehajtott összefoglaló állapotát.

## <a name="required-parameters"></a>Szükséges paraméterek

### <a name="-regionname"></a>– RegionName

Megadja az Azure Stack hub központi telepítési régiójának nevét.

|  |  |
|----------------------------|--------------|
|Típus:                       |Sztring        |
|Pozíció                   |Elemzi         |
|Alapértelmezett érték:              |Nincs          |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-fqdn"></a>– FQDN

Meghatározza a Azure Stack hub központi telepítésének külső FQDN-jét, amely **ExternalFQDN** és **ExternalDomainName**is aliasként van megjelölve.

|  |  |
|----------------------------|--------------|
|Típus:                       |Sztring        |
|Pozíció                   |Elemzi         |
|Alapértelmezett érték:              |ExternalFQDN, ExternalDomainName |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-identitysystem"></a>-IdentitySystem

Meghatározza a Azure Stack hub központi telepítési azonosító rendszer érvényes értékeit, HRE vagy ADFS-t Azure Active Directory és Active Directory összevont szolgáltatások esetében.

|  |  |
|----------------------------|--------------|
|Típus:                       |Sztring        |
|Pozíció                   |Elemzi         |
|Alapértelmezett érték:              |Nincs          |
|Érvényes értékek:               |'AAD','ADFS'  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-pfxpassword"></a>-PfxPassword

Megadja a PFX-tanúsítványfájl-fájlokhoz társított jelszót.

|  |  |
|----------------------------|---------|
|Típus:                       |SecureString |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-paascertificates"></a>-PaaSCertificates

Megadja a szórótábla elérési utakat és jelszavakat tartalmazó.

|  |  |
|----------------------------|---------|
|Típus:                       |Szórótábla |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Megadja az Azure Stack hub telepítési adatjson-konfigurációs fájlját. Ezt a fájlt a rendszer az üzembe helyezéshez hozza létre.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-pfxpath"></a>-PfxPath

Meghatározza egy olyan problémás tanúsítvány elérési útját, amelyhez importálási/exportálási rutin szükséges a javításhoz, ahogy azt az eszköz tanúsítvány-ellenőrzése is jelzi.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Megadja az eredő PFX-fájl célhelyének elérési útját az importálási/exportálási rutinból.  

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-subject"></a>-Tárgy

Meghatározza a tanúsítvány kérésének generálásához tartozó tulajdonos rendezett szótárát.

|  |  |
|----------------------------|---------|
|Típus:                       |OrderedDictionary   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-requesttype"></a>-RequestType

Megadja a tanúsítványkérelem SAN-típusát. Az érvényes értékek a következők: **MultipleCSR**, **SingleCSR**.

- A **MultipleCSR** több tanúsítványkérelmet generál, egyet az egyes szolgáltatásokhoz.
- A **SingleCSR** minden szolgáltatáshoz létrehoz egy tanúsítványkérelmet.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Érvényes értékek:               |'MultipleCSR','SingleCSR' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Megadja a tanúsítványkérelem fájljainak elérési útját. A könyvtárnak már léteznie kell.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Megadja az Azure Stack hub üzembe helyezéséhez használni kívánt Azure AD szolgáltatás-rendszergazdát.

|  |  |
|----------------------------|---------|
|Típus:                       |PSCredential   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Megadja az Azure Stack hub üzembe helyezéséhez használandó Azure AD-nevet.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Meghatározza az Azure-szolgáltatások azon példányát, amely a Azure Stack hub üzembe helyezéséhez és regisztrálásához használt fiókokat, címtárakat és előfizetéseket tartalmazza.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Érvényes értékek:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Megadja a Azure Stack hub-regisztrációhoz használandó regisztrációs fiókot.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

A Azure Stack hub-regisztrációhoz használandó regisztrációs előfizetés AZONOSÍTÓját adja meg.

|  |  |
|----------------------------|---------|
|Típus:                       |Guid     |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Nincs     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-reportpath"></a>-ReportPath

Megadja a készültségi jelentés elérési útját, alapértelmezés szerint az aktuális könyvtár és a jelentés alapértelmezett neve.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Összes      |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

## <a name="optional-parameters"></a>Választható paraméterek

### <a name="-certificatepath"></a>-CertificatePath

Megadja azt az elérési utat, amely alatt csak a tanúsítványhoz szükséges tanúsítvány-mappák jelennek meg.

A Azure Stack hub Azure AD Identity systemtel való üzembe helyezéséhez szükséges mappák a következők:

- ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

A Active Directory összevonási szolgáltatások (AD FS) Identity System Azure Stack hub telepítéséhez szükséges mappák a következők:

- ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |.\Certificates |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-includepaas"></a>-IncludePaaS  

Megadja, hogy a rendszer felveszi-e a Pásti-szolgáltatások/állomásnevek nevét a tanúsítványkérelem (ok) hoz.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-reportsections"></a>-ReportSections

Megadja, hogy csak a jelentés összegzése jelenjen-e meg, kihagyja a részleteket.

|  |  |
|----------------------------|---------|
|Típus:                       |Sztring   |
|Pozíció                   |Elemzi    |
|Alapértelmezett érték:              |Összes      |
|Érvényes értékek:               |"Certificate", "AzureRegistration", "AzureIdentity", "Jobs", "all" |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-summary"></a>– Összefoglalás

Megadja, hogy csak a jelentés összegzése jelenjen-e meg, kihagyja a részleteket.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-cleanreport"></a>-CleanReport

Eltávolítja az előző végrehajtást és az érvényesítési előzményeket, és egy új jelentésbe írja az érvényességet.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasok                    |CF                |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-outputpath"></a>– OutputPath

Meghatározza a készültségi JSON-jelentés és a részletes naplófájl mentésének egyéni elérési útját. Ha az elérési út még nem létezik, a parancs megkísérli létrehozni a könyvtárat.

|  |  |
|----------------------------|------------------|
|Típus:                       |Sztring            |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |$ENV: TEMP\AzsReadinessChecker  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-confirm"></a>– Megerősítés

A parancsmag futtatása előtt megerősítést kér.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasok                    |CF                |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-whatif"></a>– WhatIf

Azt mutatja, hogy mi történne a parancsmag futtatásakor. A parancsmag nem fut.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasok                    |Wi                |
|Pozíció                   |Elemzi             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |
