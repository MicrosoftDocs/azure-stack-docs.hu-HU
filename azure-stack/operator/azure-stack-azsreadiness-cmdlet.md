---
title: Start-AzsReadinessChecker parancsmag-referencia |} A Microsoft Docs
description: PowerShell-parancsmag súgójában talál az Azure Stack készültségi ellenőrző modul.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 03efa5e8e0dbb3ec29748383914d3d0361bc124b
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986158"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker parancsmag-referencia

A modul: **Microsoft.AzureStack.ReadinessChecker**

Ez a modul csak egyetlen parancsmag tartalmazza. A parancsmag egy vagy több központi telepítés előtti vagy előre karbantartási műveleteket az Azure Stack hajtja végre.

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

A **Start-AzsReadinessChecker** parancsmag ellenőrzi a tanúsítványokat, Azure-fiókok, az Azure-előfizetések és az Azure Active könyvtárak. Azure Stack üzembe helyezése előtt, vagy azt megelőzően karbantartási műveleteket, mint például a titkos Elforgatás Azure Stack érvényesítési futtatásához. A parancsmag is használható tanúsítvány-aláírási kérelem számára a tanúsítványokat, és opcionálisan a PaaS-tanúsítványok előállításához. Végül a parancsmag is ismételje meg a becsomagolást csomagolási közös problémák javítása a PFX-tanúsítványokat.

## <a name="examples"></a>Példák

### <a name="example-generate-certificate-signing-request"></a>Példa: a tanúsítvány-aláírási kérelem létrehozása

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Ebben a példában `Start-AzsReadinessChecker` állít elő, több tanúsítvány-aláírási kérelem (tanúsítvány-aláírási kérelmeinek) egy AD FS az Azure Stack üzembe helyezés egy régió neve, a megfelelő tanúsítványok **kelet** és a egy külső Tartományneve  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Példa: a tanúsítványok ellenőrzése

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Ebben a példában a PFX-jelszó szükség a biztonsági, és `Start-AzsReadinessChecker` ellenőrzi a relatív mappa **tanúsítványok** egy régió neve, az aad-ben üzembe helyezésének érvényes tanúsítványok **kelet** és a egy külső FQDN-jének **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Példa: az üzembe helyezési adatok (üzembe helyezés és támogatás) tanúsítványok érvényesítésére

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

A központi telepítés és a támogatás a példában a PFX-jelszó szükség a biztonsági, és `Start-AzsReadinessChecker` ellenőrzi a relatív mappa **tanúsítványok** érvényes, amelyeknél identitás, a régiót és a külső FQDN központi tanúsítványok a központi telepítési adatok JSON-fájl jön létre a központi telepítés olvasni.

### <a name="example-validate-paas-certificates"></a>Példa: PaaS tanúsítványok ellenőrzése

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates - RegionName east -FQDN azurestack.contoso.com
```

Ebben a példában egy kivonattáblát elérési utak és a jelszavakat az egyes PaaS tanúsítvány jön létre. Tanúsítványok elhagyható. `Start-AzsReadinessChecker` ellenőrzi, hogy mindegyik PFX elérési út létezik, és érvényesíti azokat a régió **kelet** és külső FQDN **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Példa: PaaS-tanúsítványok központi telepítési adatok ellenőrzése

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

Ebben a példában egy kivonattáblát elérési utak és a jelszavakat az egyes PaaS tanúsítvány jön létre. Tanúsítványok elhagyható. `Start-AzsReadinessChecker` ellenőrzi, hogy mindegyik PFX elérési út létezik, és ellenőrzi azokat a régiót, és külső FQDN olvasni a telepítési adatok JSON-fájl jön létre a központi telepítés.

### <a name="example-validate-azure-identity"></a>Példa: Azure-identitás ellenőrzése

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatai szükségesek a biztonság érdekében és `Start-AzsReadinessChecker` ellenőrzi, hogy az Azure-fiók és az Azure Active Directory bérlői könyvtár nevét aad-ben üzembe helyezésének érvényes  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Példa: Azure-identitás (üzembe helyezés támogatása) üzembe helyezési adatok ellenőrzése

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatai szükségesek a biztonság érdekében és `Start-AzsReadinessChecker` ellenőrzi, hogy az Azure-fiók és az Azure Active Directory érvényesek az aad-ben üzembe helyezéshez, amelyben **AzureCloud** és **TenantName** olvassa a a telepítési adatok JSON-fájl jön létre a központi telepítés rendszer.

### <a name="example-validate-azure-registration"></a>Példa: Azure-regisztráció ellenőrzése

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatok szükségesek a biztonság érdekében és `Start-AzsReadinessChecker` hajtja végre az ellenőrzés az adott fiók és -előfizetés annak biztosítása érdekében az Azure Stack-regisztráció is használható.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Példa: központi adatokat (a telepítési csapat) az Azure-regisztráció ellenőrzése

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatok szükségesek a biztonság érdekében és `Start-AzsReadinessChecker` hajtja végre az ellenőrzés az adott fiókot és előfizetést győződjön meg arról is használható az Azure Stack-regisztráció, ahol a további részletekért olvassa el a üzembe helyezési adatok JSON-fájl jön létre a központi telepítéshez.

### <a name="example-importexport-pfx-package"></a>Példa: importálási/exportálási PFX-csomag

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Ebben a példában a PFX-jelszó megadása kötelező a biztonság. A Ssl.pfx fájlt importálja a helyi számítógép tanúsítványtárolójába, ugyanazt a jelszót újra exportálva, és Ssl_new.pfx elmentve. Ez az eljárás szolgál, ha a tanúsítványok ellenőrzését megjelölt, hogy nem rendelkezik titkos kulccsal a **helyi gép** attribútumkészletet, a tanúsítványlánc megszakad, nem számít tanúsítványok találhatók a PFX vagy a tanúsítványlánc a nem megfelelő sorrendben.

### <a name="example-view-validation-report-deployment-support"></a>Például: ellenőrzési jelentés megjelenítése (üzembe helyezés támogatás)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Ebben a példában az üzembe helyezés vagy a támogatási csapat a készültségi jelentést kap az ügyfél (Contoso), és használja `Start-AzsReadinessChecker` , az ellenőrzés céljából Contoso végrehajtott művelet állapotának megtekintéséhez.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Például: ellenőrzési jelentés összefoglaló tanúsítvány érvényesítési csak (üzembe helyezés és támogatás) megtekintése

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Ebben a példában az üzembe helyezés vagy a támogatási csapat a készültségi jelentést kap az ügyfél (Contoso), és használja `Start-AzsReadinessChecker` a tanúsítvány érvényesítése céljából Contoso végrehajtott összesített állapotának megtekintése.

## <a name="required-parameters"></a>Szükséges paraméterek

### <a name="-regionname"></a>-RegionName

Az Azure Stack üzembe helyezési régió nevét adja meg.

|  |  |
|----------------------------|--------------|
|Típus:                       |String        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-fqdn"></a>-FQDN

Adja meg az Azure Stack üzembe helyezés külső teljes Tartományneve, is aliasneve **ExternalFQDN** és **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Típus:                       |String        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |ExternalFQDN, ExternalDomainName |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-identitysystem"></a>-IdentitySystem

Adja meg az Azure Stack üzembe helyezési identitás rendszer érvényes értékek, az AAD vagy az ADFS, Azure Active Directory és az Active Directory összevonási szolgáltatásokat, a jelölik.

|  |  |
|----------------------------|--------------|
|Típus:                       |String        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Érvényes értékek:               |'AAD','ADFS'  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

### <a name="-pfxpassword"></a>-PfxPassword

Adja meg a tanúsítvány PFX-fájlok társított jelszót.

|  |  |
|----------------------------|---------|
|Típus:                       |SecureString |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-paascertificates"></a>-PaaSCertificates

Az elérési utak és a jelszavakat a PaaS-tanúsítványokat tartalmazó kivonattáblát határozza meg.

|  |  |
|----------------------------|---------|
|Típus:                       |Kivonattábla |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Itt adható meg az Azure Stack üzembe helyezési adatok JSON-konfigurációs fájlt. Ez a fájl jön létre a központi telepítés.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-pfxpath"></a>-PfxPath

Ezt az eszközt a tanúsítványok ellenőrzését aszinkronitást-importálási és exportálási rutin megoldásához, igénylő problémát észlel tanúsítvány elérési útja.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Megadja a eredő PFX-fájlt az importálási/exportálási rutin a cél elérési útját.  

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-subject"></a>-Tulajdonos

A tanúsítvány-kérelem létrehozása egy rendezett szótár, az e-mail tárgyát határozza meg.

|  |  |
|----------------------------|---------|
|Típus:                       |OrderedDictionary   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-requesttype"></a>-RequestType

A SAN adja meg a tanúsítványkérelem. Érvényes értékek a következők **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** állít elő, több tanúsítvány-kérelmeket, egyet az egyes szolgáltatások.
- **SingleCSR** hoz létre egy tanúsítványkérelmet szolgáltatásokhoz.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |'MultipleCSR','SingleCSR' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Megadja a tanúsítványfájlok kérelem elérési út. Könyvtár már léteznie kell.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Adja meg az Azure Active Directory szolgáltatás rendszergazdája számára az Azure Stack üzembe helyezéshez használható.

|  |  |
|----------------------------|---------|
|Típus:                       |PSCredential   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Azure Stack központi telepítéshez használt adja meg az Azure Active Directoryban.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Az Azure-szolgáltatások tartalmazó a fiókok, könyvtárak és az előfizetések Azure Stack üzembe helyezésére és regisztrációjára használható példányát határozza meg.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Megadja, hogy a regisztráció az Azure Stack-regisztráció használandó.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Itt adhatja meg a regisztrációs előfizetés-azonosító használható az Azure Stack-regisztráció.

|  |  |
|----------------------------|---------|
|Típus:                       |Guid     |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-reportpath"></a>-ReportPath

Készültségi jelentés elérési útja, alapértelmezés szerint az aktuális címtár és az alapértelmezett a jelentés nevét.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

## <a name="optional-parameters"></a>Választható paraméterek

### <a name="-certificatepath"></a>-CertificatePath

Itt adhatja meg az elérési utat, amelyben csak a tanúsítványra szükség mappákat tanúsítvány jelen.

Azure Active Directory identitáskezelő rendszerbe Azure Stack üzembe helyezéshez szükséges mappák a következők:

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Azure stack-telepítés Active Directory összevonási szolgáltatások identitáskezelő rendszerbe van szükséges a mappa:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |. \Certificates |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-includepaas"></a>-IncludePaaS  

Itt adhatja meg, e PaaS-szolgáltatások/állomásnevek hozzá kell adni a tanúsítvány kérést.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-reportsections"></a>-ReportSections

Itt adhatja meg, hogy csak az jeleníti meg a jelentés összefoglaló, az áttekinthetőség kedvéért kihagyja részletei.

|  |  |
|----------------------------|---------|
|Típus:                       |String   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Érvényes értékek:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

### <a name="-summary"></a>-Összefoglaló

Itt adhatja meg, hogy csak az jeleníti meg a jelentés összefoglaló, az áttekinthetőség kedvéért kihagyja részletei.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-cleanreport"></a>-CleanReport

Eltávolítja az előző végrehajtás és érvényesítési előzmények, és a egy új jelentés ellenőrzések írja.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |CF-hez                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-outputpath"></a>-OutputPath

Itt adható meg egy egyéni elérési út készültségi jelentés a JSON és a részletes naplófájl mentéséhez. Ha az elérési út már nem létezik, a parancs megkísérli a következő könyvtár létrehozásakor.

|  |  |
|----------------------------|------------------|
|Típus:                       |String            |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |$ENV: TEMP\AzsReadinessChecker  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-confirm"></a>-Confirm

A parancsmag futtatása előtt megerősítést kér.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |CF-hez                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

### <a name="-whatif"></a>-WhatIf

Megmutatja, hogy mi történne a parancsmag futtatásakor. A parancsmag nem fut.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |wi                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |
