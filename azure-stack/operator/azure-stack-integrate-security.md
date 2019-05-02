---
title: Az Azure Stack syslog-továbbítás
description: Ismerje meg, hogyan integrálhatja az Azure Stacket figyelési megoldások syslog-továbbítás használatával
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/23/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 04/23/2019
keywords: ''
ms.openlocfilehash: a045ed6b64b80c521e6930b651635591ec0e50d4
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985311"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Az Azure Stack adatközpont integrációja - syslog-továbbítás

Ez a cikk bemutatja, hogyan külső biztonsági megoldások már telepítve van a helyi adatközpontban az Azure Stack-infrastruktúra integrálása a syslog használatával. Ha például egy biztonsági információk eseménykezelés (SIEM) rendszert. A syslog-csatorna auditálását, a riasztások és az Azure Stack-infrastruktúra összetevőit biztonsági naplóinak tesz elérhetővé. Syslog-továbbítás használatára biztonsági megoldások integrálása és/vagy beolvasni az összes auditálását, a riasztások és a biztonsági naplók megőrzésének tárolja őket.

1809 frissítésével kezdődően az Azure Stack egy integrált syslog ügyfél, beállítások konfigurálása után bocsát ki a syslog-üzeneteket a Common Event Format (CEF) hasznos adattal rendelkezik.

A következő ábra ismerteti az az Azure Stack egy külső SIEM-integráció. Nincsenek két integrációs minták, figyelembe kell venni: az első (az egy kék színnel) egyike az Azure Stack-infrastruktúra, amely magában foglalja az infrastruktúrát virtuális gépek és a Hyper-V-csomópontokon. A naplózás, a biztonsági naplók és összetevőket riasztásai központilag gyűjtött és syslog CEF hasznos adattal keresztül elérhető. Ez a dokumentum az oldal a integrációs minta ismerteti.
A második integrációs minta narancssárga fejezet azt, és az alaplapi felügyeleti vezérlővel (bmc), a hardver életciklus-gazdagép (HLH), a virtuális gépek és/vagy a hardveres partner figyelési futtató virtuális készülékek és felügyeleti ismerteti szoftverek és rack (TOR) kapcsoló tetején. Mivel ezek az összetevők hardver-partner adott, forduljon a hardver dokumentációja integrálja ezeket egy külső SIEM partnereként.

![Syslog-továbbítás diagramja](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Syslog-eseménytovábbítás konfigurálása

Az Azure Stack a syslog-ügyfél a következő konfigurációkat támogatja:

1. **Syslog (ügyfél és kiszolgáló) kölcsönös hitelesítést és a TLS 1.2-titkosítás a TCP-n keresztül:** Ebben a konfigurációban a syslog-kiszolgálónak, mind a syslog-ügyfél ellenőrizheti az identitás, minden más tanúsítványok használatával. Az üzeneteket küld egy TLS 1.2 titkosított csatornán.

2. **Syslog server-hitelesítés és a TLS 1.2-titkosítás TCP-n keresztül:** Ebben a konfigurációban a syslog-ügyfél egy tanúsítvány-n keresztül a syslog-kiszolgáló identitásának ellenőrzéséhez. Az üzeneteket küld egy TLS 1.2 titkosított csatornán.

3. **Syslog-titkosítás nem a TCP-n keresztül:** Ebben a konfigurációban a syslog-ügyfél és a syslog-kiszolgáló identitások nem ellenőrzi. Az üzeneteket küld az tiszta szöveges TCP-n keresztül.

4. **Syslog-titkosítás nélkül, UDP-n keresztül:** Ebben a konfigurációban a syslog-ügyfél és a syslog-kiszolgáló identitások nem ellenőrzi. Az üzenetek küldése nyílt szövegben UDP-n keresztül.

> [!IMPORTANT]
> A Microsoft javasolja, hitelesítést és titkosítást a TCP használata (#1 konfigurációs vagy, a nagyon minimális # 2) az éles környezetekhez man-in-the-middle támadásokat, és az üzenetek lehallgatás elleni védelem érdekében.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>-Parancsmagkészlettel konfigurálhatja a syslog-továbbítás
Syslog-eseménytovábbítás konfigurálása a kiemelt végponthoz (EGP) hozzáférést igényel. Két PowerShell-parancsmagok a syslog-továbbítás konfigurálása a EGP lettek hozzáadva:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Parancsmag-paraméterek

A paraméterek *Set-SyslogServer* parancsmagot:

| Paraméter | Leírás | Típus | Szükséges |
|---------|---------|---------|---------|
|*ServerName* | A syslog-kiszolgáló teljes Tartományneve vagy IP-címe | String | igen|
|*ServerPort* | Port számát a syslog-kiszolgáló figyel a következőn: | String | igen|
|*NoEncryption*| A syslog-üzeneteket küldjön a tiszta szöveges ügyfél kényszerítése | Jelző | nem|
|*SkipCertificateCheck*| Hagyja ki a kezdeti TLS-kézfogás során a syslog-kiszolgáló által megadott tanúsítvány érvényesítése | Jelző | nem|
|*SkipCNCheck*| Ellenőrzésének kihagyására kezdeti TLS-kézfogás során a syslog-kiszolgáló által megadott tanúsítvány köznapi nevének értékét | Jelző | nem|
|*UseUDP*| Syslog UDP az átviteli protokoll használata |Jelző | nem|
|*Remove*| Távolítsa el a kiszolgáló konfigurációja az ügyféltől érkező, és állítsa le a syslog-továbbítás| Jelző | nem|

A paraméterek *Set-SyslogClient* parancsmagot:

| Paraméter | Leírás | Típus |
|---------|---------| ---------|
| *pfxBinary* | használható az ügyfél által identitás a syslog-kiszolgáló hitelesítése tanúsítványt tartalmazó PFX-fájl  | Byte[] |
| *CertPassword* |  Jelszót a titkos kulcsot, amely kapcsolódik a pfx-fájl importálása | SecureString |
|*RemoveCertificate* | Távolítsa el a tanúsítványt az ügyfél | Jelző|
| *OutputSeverity* | A kimeneti naplózási szint. Értékek a következők **alapértelmezett** vagy **részletes**. Alapértelmezett súlyossági szintek figyelmeztetés, kritikus fontosságú, vagy hiba tartozik. Részletes tartalmazza az összes súlyossági szintek - verbose, információs, figyelmeztetési, kritikus fontosságú, vagy hiba  | String |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Syslog-továbbítás konfigurálása a TCP, a kölcsönös hitelesítés és a TLS 1.2-titkosítás

Ebben a konfigurációban a syslog-ügyfél az Azure Stackben továbbítja az üzeneteket a syslog-kiszolgálónak TCP,-n keresztül a TLS 1.2-titkosítás segítségével. A kezdeti kézfogás során az ügyfél ellenőrzi, hogy a kiszolgáló biztosít egy érvényes, megbízható tanúsítvány Hasonlóképpen az ügyfél is biztosít egy tanúsítványt a kiszolgálóra az identitása bizonyítékaként. Ez a konfiguráció a legbiztonságosabb, mert az ügyfél és a kiszolgáló identitását teljes ellenőrzés is biztosít, és üzeneteket küld egy titkosított csatornán keresztül. 

> [!IMPORTANT]
> A Microsoft határozottan javasolja, használja ezt a konfigurációt az éles környezetekhez. 

Syslog-továbbítás konfigurálása a TCP, a kölcsönös hitelesítés és a TLS 1.2-titkosítás, futtassa mindkét parancsmag egy EGP-munkamenetben:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Az ügyfél-tanúsítványt az Azure Stack üzembe helyezése során megadott megegyező legfelső szinttel kell rendelkeznie. Azt is tartalmaznia kell egy titkos kulcsot.

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

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>A TCP, syslog-eseménytovábbítás konfigurálása Server-hitelesítés és a TLS 1.2-titkosítás

Ebben a konfigurációban a syslog-ügyfél az Azure Stackben továbbítja az üzeneteket a syslog-kiszolgálónak TCP,-n keresztül a TLS 1.2-titkosítás segítségével. A kezdeti kézfogás során az ügyfél is ellenőrzi, hogy a kiszolgáló biztosít egy érvényes, megbízható tanúsítványt. Ez a konfiguráció megakadályozza, hogy az ügyfél nem megbízható célok való üzenetküldéshez.
Hitelesítési és titkosítást a TCP az alapértelmezett konfiguráció, és jelenti. a minimálisan, amelyet a Microsoft azt javasolja, éles környezetben. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Abban az esetben, ha szeretné tesztelni a syslog-kiszolgáló integrálása az Azure Stack-ügyfél önaláírt és/vagy nem megbízható tanúsítványokkal, hagyja ki a kiszolgáló-ellenőrzés a kezdeti kézfogás során az ügyfél által végzett jelző használatával.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> A Microsoft azt javasolja, éles környezetben – SkipCertificateCheck jelző használata ellen. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Syslog-továbbítás konfigurálása a TCP és a titkosítás nélkül

Ebben a konfigurációban a syslog-ügyfél az Azure Stackben továbbítja az üzeneteket a syslog-kiszolgálónak TCP,-n keresztül a titkosítás nélkül. Az ügyfél nem ellenőrzi a kiszolgáló identitását, és nem biztosít a kiszolgáló saját magát az ellenőrzéshez. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> A Microsoft azt javasolja, éles környezetben ez a konfiguráció tanúsítványokkal szemben. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Syslog-továbbítás konfigurálása a UDP és a titkosítás nélkül

Ebben a konfigurációban a syslog-ügyfél az Azure Stackben továbbítja az üzeneteket a syslog-kiszolgálónak UDP,-n keresztül a titkosítás nélkül. Az ügyfél nem ellenőrzi a kiszolgáló identitását, és nem biztosít a kiszolgáló saját magát az ellenőrzéshez. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Nincs titkosítás UDP ugyan a legkönnyebben konfigurálható, nem biztosít semmilyen man-in-the-middle támadások és az üzenetek lehallgatás elleni védelmet. 

> [!IMPORTANT]
> A Microsoft azt javasolja, éles környezetben ez a konfiguráció tanúsítványokkal szemben. 


## <a name="removing-syslog-forwarding-configuration"></a>Syslog-továbbítást, konfiguráció eltávolítása

Távolítsa el teljesen a syslog-kiszolgáló konfigurációját, és állítsa le a syslog-továbbítás:

**Távolítsa el a syslog-kiszolgáló konfigurálása az ügyfél**

```powershell  
Set-SyslogServer -Remove
```

**Távolítsa el az ügyféltanúsítványt az ügyfél**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>A syslog-telepítés ellenőrzése

Ha a syslog-kiszolgáló sikeresen csatlakozott a syslog-ügyfél, hamarosan webalkalmazásokba fogadja az eseményeket. Ha nem lát minden olyan esemény, a syslog-ügyfél konfigurációjának ellenőrzése a következő parancsmag futtatásával:

**A syslog-ügyfél konfigurációjának ellenőrzése**

```powershell  
Get-SyslogServer
```

**Ellenőrizze a tanúsítvány beállítása a syslog-ügyfélben**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog-üzenet séma

A syslog-továbbítás az Azure Stack-infrastruktúra küld a Common Event Format (CEF) formátumú üzenetek.
Minden syslog-üzenet szerkezete ezen séma alapján: 

```Syslog
<Time> <Host> <CEF payload>
```

A CEF hasznos az alábbi struktúra alapul, de a hozzárendelés az egyes mezőkhöz tartozó üzenet típusától függően változik (Windows-esemény, riasztás létrehozása riasztás lezárt).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Kiemelt végponthoz események CEF-leképezés

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

A kiemelt végponthoz események táblázatát:

| Esemény | EGP-esemény azonosítója | EGP feladat neve | Severity |
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

EGP súlyossági tábla:

| Severity | Szint | Numerikus érték |
|----------|-------| ----------------|
|0|Nem definiált|Érték: 0. Azt jelzi, hogy minden szinten naplók|
|10|Kritikus|Érték: 1. Azt jelzi, hogy a naplók kritikus riasztás|
|8|Hiba| Érték: 2. Azt jelzi, hogy hiba történt a naplók|
|5|Figyelmeztetés|Érték: 3. Azt jelzi, hogy a naplókat a további figyelmeztetés|
|2|Információ|Érték: 4. Azt jelzi, hogy az üzenet csak tájékoztató naplók|
|0|Részletes|Érték: 5. Azt jelzi, hogy minden szinten naplók|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Helyreállítási végpont események CEF-leképezés

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

A helyreállítási végpont események táblázatát:

| Esemény | REP-esemény azonosítója | REP feladat neve | Severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tábla REP súlyossága:

| Severity | Szint | Numerikus érték |
|----------|-------| ----------------|
|0|Nem definiált|Érték: 0. Azt jelzi, hogy minden szinten naplók|
|10|Kritikus|Érték: 1. Azt jelzi, hogy a naplók kritikus riasztás|
|8|Hiba| Érték: 2. Azt jelzi, hogy hiba történt a naplók|
|5|Figyelmeztetés|Érték: 3. Azt jelzi, hogy a naplókat a további figyelmeztetés|
|2|Információ|Érték: 4. Azt jelzi, hogy az üzenet csak tájékoztató naplók|
|0|Részletes|Érték: 5. Azt jelzi, hogy minden szinten naplók|

### <a name="cef-mapping-for-windows-events"></a>Windows-események CEF-leképezés

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows-események tábla súlyossága:

| CEF-fontosság | Windows Eseményszint | Numerikus érték |
|--------------------|---------------------| ----------------|
|0|Nem definiált|Érték: 0. Azt jelzi, hogy minden szinten naplók|
|10|Kritikus|Érték: 1. Azt jelzi, hogy a naplók kritikus riasztás|
|8|Hiba| Érték: 2. Azt jelzi, hogy hiba történt a naplók|
|5|Figyelmeztetés|Érték: 3. Azt jelzi, hogy a naplókat a további figyelmeztetés|
|2|Információ|Érték: 4. Azt jelzi, hogy az üzenet csak tájékoztató naplók|
|0|Részletes|Érték: 5. Azt jelzi, hogy minden szinten naplók|

Windows-események az Azure Stack egyéni bővítmény tábla:

| Egyéni bővítmény neve | Windows-esemény példa | 
|-----------------------|---------|
|MasChannel | Rendszer|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| A csoportházirend-beállítások, a felhasználó feldolgozása sikeresen megtörtént. Nem történt változás tanúsítványfeltöltés óta csoportházirend utolsó sikeres feldolgozását.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Sikeres hozzáférések naplózása|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |információ|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows biztonsági azonosítója\> |
|MasTask |0|
|MasTaskCategory| Folyamat létrehozása|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Létrehozott riasztások CEF-leképezés

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Riasztások súlyosság táblázatban:

| Severity | Szint |
|----------|-------|
|0|Nem definiált|
|10|Kritikus|
|5|Figyelmeztetés|

Riasztások létrehozása az Azure Stack egyéni bővítmény táblába:

| Egyéni bővítmény neve | Példa | 
|-----------------------|---------|
|MasEventDescription|LEÍRÁS: Egy felhasználói fiókot \<TestUser\> hoztuk létre a \<TestDomain\>. Fontos biztonsági kockázatot jelenthetnek. --SZERVIZELÉSI: Forduljon a támogatási szolgálathoz. Ügyfél segítségnyújtás a probléma megoldásához szükséges. Ne próbálja anélkül, hogy segítséget a probléma megoldásához. Nyisson egy támogatási kérést, mielőtt a fájl naplógyűjtési folyamat a útmutatásának indítása https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Lezárt riasztások CEF-leképezés

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Az alábbi példában látható a syslog-üzenet CEF hasznos adattal:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>További lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)