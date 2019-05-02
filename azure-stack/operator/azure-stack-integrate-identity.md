---
title: Az Azure Stack adatközpont integrációja - identitás
description: 'Útmutató: Azure Stack az AD FS integrálhatók az adatközponttaql, az AD FS'
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 7d9cfd742473c7a7050978a25f4b574fa0e4ba8c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985329"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Az Azure Stack adatközpont integrációja - identitás
Az identitás-szolgáltatóktól, Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) használatával az Azure-verem üzembe helyezhető. Ellenőrizze a kiválasztott Azure Stack üzembe helyezése előtt. Egy csatlakoztatott esetben válassza ki az Azure AD vagy az AD FS. A leválasztott esetben csak az AD FS használata támogatott.

> [!IMPORTANT]
> Az identitásszolgáltató nem válthat a teljes Azure Stack megoldás újbóli üzembe helyezése nélkül.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory összevonási szolgáltatások és Graph

Az AD FS telepítése lehetővé teszi, hogy identitások a az Azure Stackben erőforrásokkal történő hitelesítéshez meglévő Active Directory-erdőben. A meglévő Active Directory-erdő megköveteli, hogy egy AD FS összevonási megbízhatósági kapcsolat létrehozása az AD FS üzembe helyezése.

Hitelesítés az identitás egy részét. Szerepkör alapú hozzáférés-vezérlés (RBAC) az Azure Stackben kezeléséhez, a Graph-összetevőt kell konfigurálni. Erőforráshoz való hozzáférés delegálása, a Graph-összetevő jelenik meg a felhasználói fiókot a meglévő Active Directory-erdőben, az LDAP protokoll használatával.

![Az Azure Stack az AD FS-architektúra](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

A meglévő AD FS a fiók biztonsági jogkivonat-szolgáltatás (STS) által küldött jogcímek, az Azure Stack Active Directory összevonási szolgáltatások (az erőforrás STS). Az Azure Stackben automation hoz létre a jogcím-szolgáltatói megbízhatóság a metaadatok végpontján a meglévő AD FS számára.

A meglévő AD FS-ben található egy függőentitás-megbízhatóságot kell konfigurálni. Ebben a lépésben nem történik meg az automation, és meg kell adni az operátor. A minta használatával hozható létre az AD FS az Azure Stack VIP végpont `https://adfs.<Region>.<ExternalFQDN>/`.

A függő entitás megbízhatósági konfiguráció is megköveteli, hogy a Microsoft által biztosított jogcím-átalakítási szabályok konfigurálása.

A Graph-konfiguráció egy szolgáltatási fióknak kell lennie, feltéve, hogy rendelkezik-e olvasási engedéllyel a meglévő Active Directoryban. A fiókra akkor szükség ahhoz, hogy az RBAC-forgatókönyvek az automation bemenetként.

Az alapértelmezett szolgáltatója előfizetést az utolsó lépés egy új tulajdonos van konfigurálva. Ez a fiók összes olyan erőforrást, ha bejelentkezett az Azure Stack rendszergazdai portálon a teljes hozzáféréssel rendelkezik.

Követelmények:

|Összetevő|Követelmény|
|---------|---------|
|Graph|A Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|A Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Graph-integráció beállítása

Gráf csak egyetlen Active Directory-erdővel rendelkező integrációs támogatja. Ha több erdő, csak a konfigurációban megadott erdő használandó felhasználók és csoportok beolvasása.

A következő információkat szükség, mint a bemenetek az automatizálás paraméterek:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomADGlobalCatalog|A cél az Active Directory-erdő teljes Tartományneve<br>hogy szeretné-e integrálása|Contoso.com|
|CustomADAdminCredentials|Egy LDAP-olvasási engedéllyel rendelkező felhasználó|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Active Directory-helyek konfigurálása

A több hellyel rendelkező Active Directory-környezetek konfigurálja a legközelebbi Active Directory-helyeket az Azure Stack üzemelő példány. A konfiguráció elkerülhető kellene az Azure Stack Graph szolgáltatás egy globális katalóguskiszolgáló használatával egy távoli helyről lekérdezéseket.

Adja hozzá az Azure Stack [nyilvános VIP-hálózat](azure-stack-network.md#public-vip-network) az Active Directory-hely legközelebbi Azure Stack-alhálózatot. Például ha az Active Directory két hely Budapest és az Azure Stack üzembe helyezve, a Seattle helyen Redmond, volna hozzá a az Azure Stack nyilvános VIP-hálózat alhálózatából az Active Directory-hely Budapest számára.

További információ az Active Directory-helyek: [helytopológia megtervezése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Ha az Active Directory állhat egyetlen hely ezt a lépést kihagyhatja. Abban az esetben egy kevésbé alhálózat konfigurálva ellenőrizze, hogy az Azure Stack nyilvános virtuális IP-CÍMEK hálózati alhálózat nem része, azt.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Felhasználói fiók létrehozása a meglévő Active Directoryban (nem kötelező)

Igény szerint hozhat létre egy fiókot a Graph szolgáltatás a meglévő Active Directory. Hajtsa végre ezt a lépést, ha még nem rendelkezik egy használni kívánt fiókot.

1. A meglévő Active Directory létre a következő felhasználói fiók (javaslat):
   - **Felhasználónév**: graphservice
   - **Jelszó**: erős jelszó használata<br>Konfigurálja a jelszó soha ne járjon le.

   Nincsenek különleges engedélyekhez és a tagság nem szükséges.

#### <a name="trigger-automation-to-configure-graph"></a>Az eseményindító automation graph konfigurálása

Ebben az eljárásban az Adatközpont-hálózatát, amely képes kommunikálni az Azure Stack a rendszerjogosultságú végpont számítógépet használni.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és csatlakozzon a rendszerjogosultságú végpont IP-címét. A hitelesítő adatok használata **CloudAdmin** hitelesítéséhez.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy a kiemelt végponthoz csatlakozik, futtassa a következő parancsot: 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Amikor a rendszer kéri, adja meg, hogy a Graph szolgáltatás (például graphservice) használni kívánt felhasználói fiók hitelesítő adatait. A Register-DirectoryService parancsmag bemenete kell lennie az erdő nevét / az erdő tartományi helyett a más tartományokba az erdő gyökér.

   > [!IMPORTANT]
   > Várja meg a hitelesítő adatait az előugró (a Get-Credential nem támogatott a rendszerjogosultságú végpontját), és adja meg a Graph szolgáltatás fiók hitelesítő adatait.

#### <a name="graph-protocols-and-ports"></a>Graph-protokollok és portok

Graph szolgáltatás az Azure Stack egy írható globális katalógus Server (GC) és a kulcsszolgáltató (KDC), és a cél az Active Directory-erdőben található bejelentkezési kérések feldolgozására képes kommunikálni a következő protokollokat és portokat használja.

Az Azure Stackben Graph szolgáltatás a következő protokollok és portok segítségével kommunikál a cél az Active Directory:

|Típus|Port|Protocol|
|---------|---------|---------|
|LDAP|389|A TCP ÉS UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Az AD FS-integráció beállítása az összevonási metaadatok letöltésével

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcímeket szolgáltató neve.<br>Ezzel a módszerrel a az AD FS kezdőlapja jelenik meg.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Összevonási metaadatok hivatkozás| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Az eseményindító automation beállítása az Azure Stack a jogcím-szolgáltatói megbízhatóság

Ebben az eljárásban használja olyan számítógépre, amely képes kommunikálni az Azure Stack a kiemelt végponthoz. Valószínű, hogy a tanúsítványt használja a fiók **az AD STS FS** Azure Stack megbízhatónak.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és a kiemelt végponthoz csatlakozik.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy a kiemelt végponthoz csatlakozik, futtassa a következő parancsot, a környezetnek megfelelő paraméterek használatával:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Futtassa a következő parancsot a környezetnek megfelelő paraméterekkel az alapértelmezett szolgáltatója előfizetés tulajdonosának frissítése:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Összevonási metaadatait tartalmazó fájl azáltal, hogy az AD FS-integráció beállítása

Verziójától kezdve 1807, ezt a módszert használja, a következő feltételek bármelyikének teljesülése esetén:

- A tanúsítványlánc nem egyezik az AD FS az Azure Stackben minden más végpontok képest.
- Nincs hálózati kapcsolat a meglévő AD FS-kiszolgálóra az AD FS-példányt az Azure Stack.

Az alábbi adatokra szükség az automation-paraméterek bemenetként:


|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcímeket szolgáltató neve. Ezzel a módszerrel a az AD FS kezdőlapja jelenik meg.|Contoso|
|CustomADFSFederationMetadataFileContent|Metaadat-tartalom|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Összevonási metaadatait tartalmazó fájl létrehozása

A következő eljárás olyan számítógépen, amelyen a hálózati kapcsolat és a meglévő AD FS üzembe helyezése, amely a fiók STS válik kell használnia. Emellett a szükséges tanúsítványokkal kell telepíteni.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsot, és válasszon a környezetének megfelelő paraméterekkel:

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Másolja a metaadatait tartalmazó fájl egy számítógépre, amely képes kommunikálni a kiemelt végponthoz.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Az eseményindító automation beállítása az Azure Stack a jogcím-szolgáltatói megbízhatóság

Ez az eljárás, amely képes kommunikálni az Azure Stack a rendszerjogosultságú végpont, és hozzáférjen a metaadat-fájlt az előző lépésben létrehozott számítógépet használni.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és a kiemelt végponthoz csatlakozik.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy a kiemelt végponthoz csatlakozik, futtassa a következő parancsot, a környezetnek megfelelő paraméterek használatával:

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Futtassa a következő parancsot a környezetnek megfelelő paraméterekkel az alapértelmezett szolgáltatója előfizetés tulajdonosának frissítése:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Ha a meglévő Active Directory összevonási szolgáltatások (STS-fiók) lévő tanúsítvány forgassa be kell állítania az AD FS-integráció újbóli. Akkor is, ha a metaadatok végpontján érhető el vagy konfigurálva volt a metaadatfájl azáltal, hogy be kell állítania az integrációt.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Függő entitás konfigurálása a meglévő AD FS üzembe helyezése (STS-fiók)

A Microsoft biztosít egy parancsfájlt, amely a függőentitás-megbízhatóságot, többek között a jogcím-átalakítási szabályok konfigurálása. A parancsfájlt nem kötelező használni, mivel a parancsok manuálisan is futtatható.

A segítő szkriptet letöltheti [Azure Stack eszközök](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) a Githubon.

Ha úgy dönt, hogy manuálisan futtassa a parancsokat, kövesse az alábbi lépéseket:

1. A helyi adatközpontban az AD FS-példány vagy farm tag egy .txt fájl (például c:\ClaimRules.txt elmentve) másolja az alábbi tartalommal:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Ellenőrizze, hogy a Windows Forms-alapú hitelesítés extranetes és intranetes engedélyezve van. Először ellenőrizheti, hogy az már engedélyezve van a következő parancsmagot:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > A Windows integrált hitelesítést (WIA), az AD FS telepítése az elavult támogatott böngészőazonosító karakterláncok előfordulhat, hogy frissíteni kell a legújabb ügyfél támogatására lehet szükség. Tudjon meg a cikk további információt a WIA frissítése támogatott böngészőazonosító karakterláncok [intranetes űrlapalapú hitelesítés konfigurálása eszközökhöz, amelyek nem támogatják a WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>A további lépések elvégzésével űrlapalapú hitelesítési házirendet a cikkben vannak dokumentálva [hitelesítési házirendek konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. A függő entitás megbízhatóságának hozzáadása, futtassa a következő Windows PowerShell-parancsot az AD FS-példány vagy farm tagja. Ne feledje frissíteni az AD FS-végpont, és az 1. lépésben létrehozott fájlra mutat.

   **Az AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Az AD FS 2012/2012 R2-ben**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Kell használnia az AD FS beépülő MMC-modulban a Kiállításengedélyezési szabályok konfigurálása a Windows Server 2012 vagy 2012 R2 AD FS használata esetén.

4. Használatakor az Internet Explorer vagy a Microsoft Edge böngésző el az Azure Stack, jogkivonat-kötések kell figyelmen kívül. Ellenkező esetben a bejelentkezési kísérlet sikertelen. Az AD FS-példány vagy farm tagja futtassa a következő parancsot:

   > [!note]  
   > Ez a lépés nem alkalmazható, ha a Windows Server 2012 vagy 2012 R2 AD FS használatával. Hagyja ki ezt a parancsot, és folytassa az integráció biztonságos legyen.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Egyszerű szolgáltatásnév létrehozása

Nincsenek számos forgatókönyv, amely egy egyszerű szolgáltatásnevét (SPN) hitelesítés használatának megkövetelése. Néhány példa a következők:

- Az AD FS üzembe helyezése az Azure Stack parancssori felület használat
- A System Center felügyeleti csomag az Azure Stackhez az AD FS telepítésekor
- Erőforrás-szolgáltatók az Azure Stackben és az AD FS telepítésekor
- Számos olyan alkalmazás
- Szüksége van egy nem interaktív bejelentkezés

> [!Important]  
> Az AD FS csak az interaktív bejelentkezési munkamenetek támogatja. Ha egy nem interaktív bejelentkezést egy automatizált keresztül van szükség, egy egyszerű Szolgáltatásnevet kell használnia.

Egy egyszerű Szolgáltatásnevet létrehozásával kapcsolatos további információkért lásd: [az AD FS egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="configuration-rollback"></a>Configuration Rollback

Ha hiba lép fel, amelyek a környezetet, akkor nem tudják hitelesíteni állapotban hagyja, a visszaállítási lehetőség érhető el.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Miután a visszaállítási művelethez, az összes konfigurációs módosítás visszavonásra. Csak a beépített hitelesítés **CloudAdmin** felhasználói lehetőség.

   > [!IMPORTANT]
   > Konfigurálnia kell az eredeti tulajdonost az alapértelmezett szolgáltatója előfizetés

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>További naplók gyűjtését.

A parancsmagok bármelyike sikertelen, ha további naplók segítségével begyűjtheti a `Get-Azurestacklogs` parancsmagot.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>További lépések

[Külső figyelési megoldások integrálása](azure-stack-integrate-monitor.md)
