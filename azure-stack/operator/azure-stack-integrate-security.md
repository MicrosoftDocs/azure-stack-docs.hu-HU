---
title: Azure Stack hub integrálása figyelési megoldásokkal a syslog forwarding használatával
description: Ismerje meg, hogyan integrálhatja Azure Stack hubot a figyelési megoldásokkal a syslog forwarding használatával.
author: ihenkel
ms.topic: article
ms.date: 01/10/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 43398b4ef745c9ad0d72274a9a3ef400c4669053
ms.sourcegitcommit: 3974fc544986a5eccfac0ba7c4441b05279ae226
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "77001614"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>Azure Stack hub integrálása figyelési megoldásokkal a syslog forwarding használatával

Ez a cikk bemutatja, hogyan integrálhatja az adatközpontban már üzembe helyezett külső biztonsági megoldásokkal Azure Stack hub-infrastruktúrát a syslog használatával. Például egy biztonsági információ-kezelő (SIEM) rendszer. A syslog-csatorna a Azure Stack hub-infrastruktúra összes összetevőjéből naplózza a naplókat, a riasztásokat és a biztonsági naplókat. A syslog forwarding használatával integrálhatja a biztonsági figyelő megoldásait, és lekérheti az összes naplózási, riasztási és biztonsági naplót, hogy tárolja azokat az adatmegőrzéshez.

Az 1809-es frissítéstől kezdve a Azure Stack hub egy integrált syslog-ügyféllel rendelkezik, amely a konfigurálást követően a gyakori esemény formátumában (CEF) elbocsátja a syslog-üzeneteket.

Az alábbi ábra az Azure Stack hub külső SIEM-sel való integrálását ismerteti. Kétféle integrációs mintát kell figyelembe venni: az első (a kék) az a Azure Stack hub-infrastruktúra, amely magában foglalja az infrastruktúra-alapú virtuális gépeket és a Hyper-V-csomópontokat. Ezekből az összetevőkből származó összes naplózási, biztonsági naplós és riasztás központilag össze van gyűjtve, és a syslog használatával CEF hasznos adattartalommal. Ez az integrációs minta a jelen dokumentum oldalán olvasható.
A második integrációs minta a narancssárga ábrán látható, amely a alaplapi-felügyeleti vezérlőket (bmc), a hardveres életciklus-gazdagépet (HLH), a hardvereszközöket és a hardvereket futtató virtuális gépeket, valamint a hardverprofil-figyelési és-felügyeleti szoftvert futtató virtuális készülékeket és a rack (TOR) kapcsolók. Mivel ezek az összetevők a hardveres partnerekkel kapcsolatosak, vegye fel a kapcsolatot a hardver-partnerével, hogy miként integrálhatja őket egy külső SIEM-mel.

![Syslog-továbbítási diagram](media/azure-stack-integrate-security/azure-stack-hub-syslog-forwarding-diagram_bg.svg)

## <a name="configuring-syslog-forwarding"></a>Syslog-továbbítás konfigurálása

Az Azure Stack hub syslog-ügyfele a következő konfigurációkat támogatja:

1. **Syslog over TCP, kölcsönös hitelesítéssel (ügyfél és kiszolgáló) és TLS 1,2 titkosítással:** Ebben a konfigurációban a syslog-kiszolgáló és a syslog-ügyfél is ellenőrizheti egymás identitását a tanúsítványokon keresztül. Az üzenetek küldése TLS 1,2 titkosított csatornán történik.

2. **SYSLOG TCP protokollon keresztül kiszolgáló-hitelesítéssel és TLS 1,2 titkosítással:** Ebben a konfigurációban a syslog-ügyfél tanúsítvány használatával ellenőrizheti a syslog-kiszolgáló identitását. Az üzenetek küldése TLS 1,2 titkosított csatornán történik.

3. **SYSLOG TCP protokollon keresztül, titkosítás nélkül:** Ebben a konfigurációban a syslog-ügyfél és a syslog-kiszolgáló identitása nem ellenőrzött. Az üzeneteket a rendszer tiszta szövegként továbbítja a TCP protokollon keresztül.

4. **Syslog over UDP, titkosítás nélkül:** Ebben a konfigurációban a syslog-ügyfél és a syslog-kiszolgáló identitása nem ellenőrzött. Az üzenetek küldése az UDP protokollon keresztül, tiszta szövegként történik.

> [!IMPORTANT]
> A Microsoft határozottan azt javasolja, hogy a TCP-t hitelesítéssel és titkosítással (konfiguráció #1, vagy legalább #2) használja az éles környezetekben, hogy védelmet nyújtson a támadásoknak és az üzenetek lehallgatásának.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>A syslog-továbbítás konfigurálásához szükséges parancsmagok
A syslog-továbbítás konfigurálásához hozzá kell férnie a privilegizált végponthoz (PEP). Két PowerShell-parancsmag lett hozzáadva a PEP-hez a syslog-továbbítás konfigurálásához:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <UInt16>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Parancsmagok paraméterei

A *set-SyslogServer* parancsmag paraméterei:

| Paraméter | Leírás | Type (Típus) | Szükséges |
|---------|---------|---------|---------|
|*ServerName* | A syslog-kiszolgáló teljes tartományneve vagy IP-címe. | Sztring | igen|
|*ServerPort* | A syslog-kiszolgáló által figyelt port száma. | UInt16 | igen|
|*Nincs titkosítás*| Kényszerítse az ügyfelet, hogy a syslog-üzeneteket titkosítatlan szövegként küldje el. | zászló | nem|
|*SkipCertificateCheck*| A syslog-kiszolgáló által a kezdeti TLS-kézfogás során biztosított tanúsítvány érvényesítésének kihagyása. | zászló | nem|
|*SkipCNCheck*| Kihagyhatja a syslog-kiszolgáló által a kezdeti TLS-kézfogás során megadott tanúsítvány köznapi név értékének érvényesítését. | zászló | nem|
|*UseUDP*| Használja a syslog-t UDP-ként átviteli protokollként. |zászló | nem|
|*Eltávolítása*| Távolítsa el a kiszolgáló konfigurációját az ügyfélről, és állítsa le a syslog-továbbítást.| zászló | nem|

A *set-SyslogClient* parancsmag paraméterei:

| Paraméter | Leírás | Type (Típus) |
|---------|---------| ---------|
| *pfxBinary* | A pfx-fájl tartalma, amely egy bájt [] értékre van átirányítva, amely tartalmazza az ügyfél által a syslog-kiszolgálón való hitelesítéshez használandó tanúsítványt.  | Bájt [] |
| *CertPassword* |  A pfx-fájlhoz társított titkos kulcs importálására szolgáló jelszó. | SecureString |
|*RemoveCertificate* | Tanúsítvány eltávolítása az ügyfélről. | zászló|
| *OutputSeverity* | A kimeneti naplózás szintje. Az értékek **alapértelmezettek** vagy **részletesek**. Az alapértelmezett érték a súlyossági szinteket tartalmazza: figyelmeztetés, kritikus vagy hiba. A részletes beállítás minden súlyossági szintet tartalmaz: részletes, tájékoztató, figyelmeztetés, kritikus vagy hiba.  | Sztring |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>A syslog továbbításának konfigurálása a TCP, a kölcsönös hitelesítés és a TLS 1,2 titkosítás használatával

Ebben a konfigurációban az Azure Stack hub syslog-ügyfele továbbítja az üzeneteket a syslog-kiszolgálónak TCP protokollon keresztül, TLS 1,2 titkosítással. A kezdeti kézfogás során az ügyfél ellenőrzi, hogy a kiszolgáló érvényes és megbízható tanúsítványt biztosít-e. Az ügyfél emellett tanúsítványt is biztosít a kiszolgálónak az identitásának igazolásához. Ez a legbiztonságosabb beállítás, mivel az ügyfél és a kiszolgáló identitásának teljes érvényesítését biztosítja, és titkosított csatornán keresztül küld üzeneteket.

> [!IMPORTANT]
> A Microsoft határozottan javasolja ennek a konfigurációnak az éles környezetekben való használatát. 

A syslog-továbbítás konfigurálásához a TCP, a kölcsönös hitelesítés és a TLS 1,2 titkosítás használatával futtassa mindkét parancsmagot egy PEP-munkamenetben:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Az ügyféltanúsítvány-nek ugyanazzal a gyökérrel kell rendelkeznie, mint az Azure Stack hub üzembe helyezése során. Emellett titkos kulcsot is tartalmaznia kell.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>A syslog továbbításának konfigurálása a TCP, a kiszolgálói hitelesítés és a TLS 1,2 titkosítás használatával

Ebben a konfigurációban az Azure Stack hub syslog-ügyfele továbbítja az üzeneteket a syslog-kiszolgálónak TCP protokollon keresztül, TLS 1,2 titkosítással. A kezdeti kézfogás során az ügyfél azt is ellenőrzi, hogy a kiszolgáló érvényes és megbízható tanúsítványt biztosít-e. Ez a konfiguráció megakadályozza, hogy az ügyfél üzeneteket küldjön nem megbízható célhelyekre.
A hitelesítést és titkosítást használó TCP az alapértelmezett konfiguráció, amely a Microsoft által az éles környezethez javasolt minimális biztonsági szintet jelöli.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Ha egy önaláírt vagy nem megbízható tanúsítvánnyal szeretné tesztelni a syslog-kiszolgáló integrációját az Azure Stack hub-ügyféllel, a jelzők használatával kihagyhatja az ügyfél által a kezdeti kézfogás során elvégzett érvényesítést.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> A Microsoft a-SkipCertificateCheck jelző használatát javasolja éles környezetekben. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>A syslog-továbbítás konfigurálása TCP-vel és titkosítás nélkül

Ebben a konfigurációban az Azure Stack hub syslog-ügyfele továbbítja az üzeneteket a syslog-kiszolgálónak TCP protokollon keresztül, titkosítás nélkül. Az ügyfél nem ellenőrzi a kiszolgáló identitását, és nem biztosítja a saját identitását a kiszolgálónak ellenőrzés céljából.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> A Microsoft javasolja, hogy ezt a konfigurációt éles környezetekben használja.


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>A syslog továbbításának konfigurálása UDP protokollal és titkosítás nélkül

Ebben a konfigurációban az Azure Stack hub syslog-ügyfele továbbítja az üzeneteket a syslog-kiszolgálónak UDP-n keresztül, titkosítás nélkül. Az ügyfél nem ellenőrzi a kiszolgáló identitását, és nem biztosítja a saját identitását a kiszolgálónak ellenőrzés céljából.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Míg a legegyszerűbb, ha a titkosítás nélküli UDP-t konfigurálja, nem nyújt védelmet a támadók és az üzenetek lehallgatásával szemben.

> [!IMPORTANT]
> A Microsoft javasolja, hogy ezt a konfigurációt éles környezetekben használja.


## <a name="removing-syslog-forwarding-configuration"></a>A syslog-továbbító konfigurációjának eltávolítása

A syslog-kiszolgáló konfigurációjának teljes eltávolítása és a syslog-továbbítás leállítása:

**A syslog-kiszolgáló konfigurációjának eltávolítása az ügyfélről**

```powershell  
Set-SyslogServer -Remove
```

**Ügyféltanúsítvány eltávolítása az ügyfélről**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>A syslog telepítésének ellenőrzése

Ha sikeresen csatlakoztatta a syslog-ügyfelet a syslog-kiszolgálóhoz, hamarosan megkezdheti az események fogadását. Ha nem lát eseményt, ellenőrizze a syslog-ügyfél konfigurációját a következő parancsmagok futtatásával:

**A kiszolgáló konfigurációjának ellenőrzése a syslog-ügyfélben**

```powershell  
Get-SyslogServer
```

**A tanúsítvány telepítésének ellenőrzése a syslog-ügyfélben**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog-üzenet sémája

Az Azure Stack hub-infrastruktúra syslog-továbbítása a Common Event Format (CEF) formátumban formázott üzeneteket küld.
Minden syslog-üzenet strukturálva van a séma alapján:

```Syslog
<Time> <Host> <CEF payload>
```

Az CEF hasznos adatok az alábbi struktúrán alapulnak, de az egyes mezők leképezése az üzenet típusától (Windows-esemény, létrehozott riasztás, riasztás lezárt) függően változik.

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack Hub
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>CEF megfeleltetése a Kiemelt végpont eseményeihez

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Az emelt szintű végpont eseményeinek táblázata:

| Esemény | PEP-esemény azonosítója | A PEP-feladat neve | Súlyosság |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

A PEP súlyossági táblázata:

| Súlyosság | Szint | Numerikus érték |
|----------|-------| ----------------|
|0|Nem definiált|Érték: 0. A naplókat minden szinten jelzi|
|10|Kritikus|Érték: 1. Kritikus riasztások naplóit jelzi|
|8|Hiba| Érték: 2. Hibát jelez a naplókban.|
|5|Figyelmeztetés|Érték: 3. Figyelmeztetési naplókat jelez|
|2|Információ|Érték: 4. Tájékoztató üzenet naplóit jelzi|
|0|Részletes|Érték: 5. A naplókat minden szinten jelzi|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>CEF-megfeleltetés a helyreállítási végpont eseményeihez

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

A helyreállítási végpont eseményeinek táblázata:

| Esemény | REP-esemény azonosítója | REP-feladat neve | Súlyosság |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

REP súlyossági táblázat:

| Súlyosság | Szint | Numerikus érték |
|----------|-------| ----------------|
|0|Nem definiált|Érték: 0. A naplókat minden szinten jelzi|
|10|Kritikus|Érték: 1. Kritikus riasztások naplóit jelzi|
|8|Hiba| Érték: 2. Hibát jelez a naplókban.|
|5|Figyelmeztetés|Érték: 3. Figyelmeztetési naplókat jelez|
|2|Információ|Érték: 4. Tájékoztató üzenet naplóit jelzi|
|0|Részletes|Érték: 5. A naplókat minden szinten jelzi|

### <a name="cef-mapping-for-windows-events"></a>Windows-események CEF-leképezése

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows-események súlyossági táblázata:

| CEF súlyossági értéke | Windows-esemény szintje | Numerikus érték |
|--------------------|---------------------| ----------------|
|0|Nem definiált|Érték: 0. A naplókat minden szinten jelzi|
|10|Kritikus|Érték: 1. Kritikus riasztások naplóit jelzi|
|8|Hiba| Érték: 2. Hibát jelez a naplókban.|
|5|Figyelmeztetés|Érték: 3. Figyelmeztetési naplókat jelez|
|2|Információ|Érték: 4. Tájékoztató üzenet naplóit jelzi|
|0|Részletes|Érték: 5. A naplókat minden szinten jelzi|

Egyéni bővítmények táblázata Azure Stack hub Windows-eseményeihez:

| Egyéni bővítmény neve | Windows-esemény – példa | 
|-----------------------|---------|
|MasChannel | Rendszer|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!! 4132, G, 0!!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| A felhasználó Csoportházirend beállításait a rendszer sikeresen feldolgozta. A Csoportházirend legutóbbi sikeres feldolgozása óta nem észlelhető változás.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Sikeres naplózás|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |információ|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-csoportházirend|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Folyamat létrehozása|
|MasUserData|KB4093112!! 5112!! Telepítve!! 0x0!! WindowsUpdateAgent XPath:/Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>CEF-megfeleltetés a létrehozott riasztásokhoz

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Riasztások súlyossági táblázata:

| Súlyosság | Szint |
|----------|-------|
|0|Nem definiált|
|10|Kritikus|
|5|Figyelmeztetés|

Egyéni bővítmények táblázata Azure Stack központban létrehozott riasztásokhoz:

| Egyéni bővítmény neve | Példa | 
|-----------------------|---------|
|MasEventDescription|Leírás: \<tesztfelhasználó\> felhasználói fiók lett létrehozva \<TestDomain\>. Ez egy lehetséges biztonsági kockázat. --SZERVIZELÉS: forduljon az ügyfélszolgálathoz. A probléma megoldásához az ügyfél segítségére van szükség. Ne próbálja meg elhárítani ezt a problémát segítség nélkül. A támogatási kérés megnyitása előtt indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles útmutatása alapján.

### <a name="cef-mapping-for-alerts-closed"></a>CEF lezárt riasztások leképezése

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Az alábbi példa egy CEF adattartalommal rendelkező syslog-üzenetet mutat be:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Következő lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)
