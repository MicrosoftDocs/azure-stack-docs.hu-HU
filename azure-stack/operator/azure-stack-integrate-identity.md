---
title: AD FS identitás integrálása az Azure Stack hub-adatközponttal
description: Megtudhatja, hogyan integrálhatja Azure Stack hub AD FS Identity providert az adatközpont AD FS.
author: BryanLa
ms.topic: article
ms.date: 04/10/2020
ms.author: bryanla
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.custom: conteperfq4
ms.openlocfilehash: 3087e7b4f84aa710a89a2f122e91bcfd643eed8d
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544190"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-hub-datacenter"></a>AD FS identitás integrálása az Azure Stack hub-adatközponttal

Azure Stack hub a Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával is üzembe helyezhető az identitás-szolgáltatóként. Azure Stack hub üzembe helyezése előtt el kell végeznie a választást. Egy csatlakoztatott forgatókönyvben kiválaszthatja az Azure AD-t vagy a AD FS. A leválasztott forgatókönyvek esetében csak AD FS támogatott. Ez a cikk bemutatja, hogyan integrálható Azure Stack hub-AD FS az adatközpont AD FS.

> [!IMPORTANT]
> Az identitás-szolgáltatót nem lehet átváltani a teljes Azure Stack hub-megoldás újbóli üzembe helyezése nélkül.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory összevonási szolgáltatások (AD FS) és gráf

A AD FS-ben való üzembe helyezése lehetővé teszi egy meglévő Active Directory erdőben lévő identitások hitelesítését Azure Stack hub erőforrásaival. A meglévő Active Directory erdőben a AD FS központi telepítése szükséges, hogy lehetővé váljon AD FS összevonási megbízhatósági kapcsolat létrehozása.

A hitelesítés az identitás egyik része. A szerepköralapú hozzáférés-vezérlés (RBAC) Azure Stack hub-ban való kezeléséhez konfigurálni kell a Graph összetevőt. Az erőforrásokhoz való hozzáférés delegálásakor a Graph-összetevő az LDAP protokoll használatával megkeresi a felhasználói fiókot a meglévő Active Directory erdőben.

![Azure Stack hub AD FS architektúra](media/azure-stack-integrate-identity/azure-stack-adfs-architecture.svg)

A meglévő AD FS a fiók biztonsági jogkivonat-szolgáltatása (STS), amely jogcímeket küld az Azure Stack hub AD FS (az erőforrás STS) számára. Azure Stack központban az Automation létrehozza a jogcím-szolgáltatói megbízhatóságot a meglévő AD FS metaadat-végpontján.

A meglévő AD FSon konfigurálni kell egy függő entitás megbízhatóságát. Ezt a lépést az Automation nem hajtja végre, és az operátornak kell konfigurálnia. A AD FS Azure Stack hub VIP-végpontját a minta használatával lehet létrehozni `https://adfs.<Region>.<ExternalFQDN>/` .

A függő entitás megbízhatóságának konfigurációjában a Microsoft által biztosított jogcím-átalakítási szabályok konfigurálására is szükség van.

A Graph-konfigurációhoz meg kell adni egy olyan szolgáltatásfiókot, amely olvasási engedéllyel rendelkezik a meglévő Active Directory. Erre a fiókra a RBAC-forgatókönyvek engedélyezéséhez bemenetként kell megadni az Automation számára.

Az utolsó lépésben új tulajdonos van konfigurálva az alapértelmezett szolgáltatói előfizetéshez. Ennek a fióknak teljes hozzáférése van az összes erőforráshoz az Azure Stack hub felügyeleti portálra való bejelentkezéskor.

Követelmények:

|Összetevő|Követelmény|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016 2019|
|AD FS|Windows Server 2012/2012 R2/2016 2019|

## <a name="setting-up-graph-integration"></a>Gráf-integráció beállítása

A Graph csak egyetlen Active Directory erdővel való integrációt támogatja. Ha több erdő létezik, a rendszer csak a konfigurációban megadott erdőt fogja használni a felhasználók és csoportok beolvasásához.

A következő információk szükségesek az Automation-paraméterek bemenetei számára:

|Paraméter|Üzembe helyezési munkalap paramétere|Leírás|Példa|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|Erdő teljes tartományneve AD FS|Annak a célként Active Directory erdőnek a teljes tartományneve, amelyet integrálni szeretne|Contoso.com|
|`CustomADAdminCredentials`| |LDAP-olvasási engedéllyel rendelkező felhasználó|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Active Directory helyek konfigurálása

Active Directory több hellyel rendelkező üzemelő példányok esetében konfigurálja a legközelebbi Active Directory helyet az Azure Stack hub központi telepítésére. A konfiguráció elkerüli, hogy a Azure Stack hub Graph szolgáltatás a távoli helyről származó globáliskatalógus-kiszolgáló használatával oldja meg a lekérdezéseket.

Adja hozzá az Azure Stack hub [nyilvános VIP hálózati](azure-stack-network.md#public-vip-network) alhálózatát az Azure stack hub-hoz legközelebb lévő Active Directory-helyhez. Tegyük fel például, hogy a Active Directory két hellyel rendelkezik: Seattle és Redmond. Ha Azure Stack hub üzembe helyezése a Seattle-beli helyen történik, az Azure Stack hub nyilvános VIP hálózati alhálózatát a Seattle Active Directory webhelyére kell felvennie.

Active Directory-helyekkel kapcsolatos további információkért lásd: [a hely topológiájának megtervezése](/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Ha a Active Directory egyetlen helyből áll, akkor kihagyhatja ezt a lépést. Ha rendelkezik egy Catch-alhálózattal, ellenőrizze, hogy az Azure Stack hub nyilvános VIP-alhálózata nem része-e.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Felhasználói fiók létrehozása a meglévő Active Directoryban (nem kötelező)

Igény szerint létrehozhat egy fiókot a Graph szolgáltatáshoz a meglévő Active Directoryban. Akkor hajtsa végre ezt a lépést, ha még nem rendelkezik a használni kívánt fiókkal.

1. A meglévő Active Directory hozza létre a következő felhasználói fiókot (javaslat):
   - **Felhasználónév** : graphservice
   - **Jelszó** : Használjon erős jelszót, és konfigurálja a jelszót, hogy soha ne járjon le.

   Nincs szükség különleges engedélyekre vagy tagságra.

#### <a name="trigger-automation-to-configure-graph"></a>Automatizálás elindítása a gráf konfigurálásához

Ehhez az eljáráshoz használjon olyan számítógépet az adatközpont-hálózaton, amely képes kommunikálni az Azure Stack hub privilegizált végpontján.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a privilegizált végpont IP-címéhez. A hitelesítéshez használja a **CloudAdmin** hitelesítő adatait.

   ```powershell  
   $creds = Get-Credential
   $pep = New-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy már rendelkezik egy, a privilegizált végponttal rendelkező munkamenettel, futtassa a következő parancsot: 

   ```powershell  
    $i = @(
           [pscustomobject]@{ 
                     CustomADGlobalCatalog="fabrikam.com"
                     CustomADAdminCredential= get-credential
                     SkipRootDomainValidation = $false 
                     ValidateParameters = $true
                   }) 

    Invoke-Command -Session $pep -ScriptBlock {Register-DirectoryService -customCatalog $using:i} 


   ```

   Ha a rendszer kéri, válassza ki a Graph szolgáltatáshoz használni kívánt felhasználói fiók hitelesítő adatait (például graphservice). Az Register-DirectoryService parancsmag bemenetének az erdőben lévő erdő neve/legfelső tartományának kell lennie, nem pedig az erdő többi tartományának.

   > [!IMPORTANT]
   > Várjon, amíg a hitelesítő adatok előugró ablaka (a lekéréses hitelesítő adatok nem támogatottak a privilegizált végponton), és adja meg a Graph szolgáltatás fiókjának hitelesítő adatait.

3. A **Register-DirectoryService** parancsmag olyan választható paramétereket tartalmaz, amelyeket bizonyos esetekben használhat, ha a meglévő Active Directory érvényesítése sikertelen. A parancsmag végrehajtásakor a rendszer ellenőrzi, hogy a megadott tartomány a legfelső szintű tartomány-e, a globáliskatalógus-kiszolgáló elérhető-e, és hogy a megadott fiók olvasási hozzáférést kap-e.

   |Paraméter|Leírás|
   |---------|---------|
   |`SkipRootDomainValidation`|Megadja, hogy a rendszer a javasolt gyökértartomány helyett gyermektartomány használatát használja.|
   |`ValidateParameters`|Az összes ellenőrzési ellenőrzés mellőzése.|

#### <a name="graph-protocols-and-ports"></a>Graph protokollok és portok

Az Azure Stack hub Graph szolgáltatása a következő protokollokat és portokat használja egy írható globáliskatalógus-kiszolgálóval (GC) és kulcsszolgáltató (KDC) való kommunikációhoz, amely képes feldolgozni a bejelentkezési kérelmeket a cél Active Directory erdőben.

Az Azure Stack hub Graph szolgáltatása a következő protokollokat és portokat használja a célként megadott Active Directory való kommunikációhoz:

|Típus|Port|Protokoll|
|---------|---------|---------|
|LDAP|389|TCP & UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>AD FS integráció beállítása az összevonási metaadatok letöltésével

A következő információk szükségesek az Automation-paraméterek bemenetéhez:

|Paraméter|Üzembe helyezési munkalap paramétere|Leírás|Példa|
|---------|---------|---------|---------|
|CustomAdfsName|AD FS szolgáltató neve|A jogcím-szolgáltató neve.<br>Így jelenik meg a AD FS kezdőlapján.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|AD FS metaadat-URI|Összevonási metaadatok hivatkozása.| https: \/ /ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|NA|Nem kötelező paraméter a CRL-ellenőrzés kihagyása érdekében.|Nincsenek|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Automatizálás elindítása a jogcím-szolgáltatói megbízhatóság konfigurálásához Azure Stack központban

Ehhez az eljáráshoz használjon olyan számítógépet, amely képes kommunikálni a rendszerjogosultságú végponttal Azure Stack központban. A rendszer azt várta, hogy a fiók **STS AD FS** által használt tanúsítványát Azure stack hub megbízhatónak tekinti.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és kapcsolódjon a privilegizált végponthoz.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy csatlakozott a Kiemelt végponthoz, futtassa a következő parancsot a környezetének megfelelő paraméterek használatával:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Futtassa a következő parancsot az alapértelmezett szolgáltatói előfizetés tulajdonosának frissítéséhez a környezetének megfelelő paraméterekkel:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>AD FS integráció beállítása az összevonási metaadat-fájl biztosításával

Az 1807-es verziótól kezdődően ezt a módszert használja, ha az alábbi feltételek bármelyike igaz:

- A tanúsítványlánc különbözik a AD FS az Azure Stack hub összes többi végpontjának összehasonlítva.
- Nincs hálózati kapcsolat a meglévő AD FS-kiszolgálóval Azure Stack hub AD FS példányáról.

A következő információk szükségesek az Automation-paraméterek bemenetéhez:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcím-szolgáltató neve. Így jelenik meg a AD FS kezdőlapján.|Contoso|
|CustomADFSFederationMetadataFileContent|Metaadatok tartalma.|$using: federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Összevonási metaadatok fájljának létrehozása

A következő eljáráshoz olyan számítógépet kell használnia, amely hálózati kapcsolattal rendelkezik a meglévő AD FS üzemelő példányhoz, amely a fiók STS lesz. A szükséges tanúsítványokat is telepíteni kell.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsot a környezetének megfelelő paraméterek használatával:

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Másolja a metaadat-fájlt egy olyan számítógépre, amely képes kommunikálni a Kiemelt végponttal.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Automatizálás elindítása a jogcím-szolgáltatói megbízhatóság konfigurálásához Azure Stack központban

Ehhez az eljáráshoz használjon olyan számítógépet, amely képes kommunikálni az Azure Stack hub rendszerjogosultságú végpontjának használatával, és hozzáfér az előző lépésben létrehozott metaadat-fájlhoz.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és kapcsolódjon a privilegizált végponthoz.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy csatlakozott a Kiemelt végponthoz, futtassa a következő parancsot a környezetének megfelelő paraméterek használatával:

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Futtassa az alábbi parancsot az alapértelmezett szolgáltatói előfizetés tulajdonosának frissítéséhez. Használja a környezetének megfelelő paramétereket.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Ha elforgatja a tanúsítványt a meglévő AD FSban (a fiók STS), akkor újra be kell állítania a AD FS-integrációt. Akkor is be kell állítania az integrációt, ha a metaadat-végpont elérhető, vagy a metaadat-fájl megadásával lett konfigurálva.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Függő entitás konfigurálása meglévő AD FS üzemelő példányon (fiók STS)

A Microsoft olyan parancsfájlt biztosít, amely konfigurálja a függő entitás megbízhatóságát, beleértve a jogcím-átalakítási szabályokat is. A szkript használata nem kötelező, mert manuálisan is futtathatja a parancsokat.

A Súgó parancsfájlt a GitHubon [Azure stack Hub-eszközökről](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) töltheti le.

Ha úgy dönt, hogy manuálisan futtatja a parancsokat, kövesse az alábbi lépéseket:

1. Másolja az alábbi tartalmat egy. txt fájlba (például az c:\ClaimIssuanceRules.txt néven mentett) az adatközpont AD FS példányára vagy a farm tagjára:

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

2. Ellenőrizze, hogy engedélyezve van-e az extranetes és intranetes Windows Forms-alapú hitelesítés. A következő parancsmag futtatásával ellenőrizhető, hogy a szolgáltatás már engedélyezve van-e:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Előfordulhat, hogy a Windows beépített hitelesítés (WIA) által támogatott felhasználói ügynök sztringek elavultak a AD FS központi telepítéséhez, és frissítésre lehet szükség a legújabb ügyfelek támogatásához. A WIA által támogatott felhasználói ügynök sztringek frissítésével kapcsolatos további információkért tekintse meg az [intranetes űrlapalapú hitelesítés konfigurálása a WIA-t nem támogató eszközök esetében című](/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia)cikket.<br><br>Az űrlapalapú hitelesítési házirend engedélyezésének lépései a [hitelesítési házirendek konfigurálása](/windows-server/identity/ad-fs/operations/configure-authentication-policies)című cikkben olvashatók.

3. A függő entitás megbízhatóságának hozzáadásához futtassa a következő Windows PowerShell-parancsot a AD FS példányán vagy egy farm tagján. Ügyeljen arra, hogy frissítse a AD FS végpontot, és mutasson az 1. lépésben létrehozott fájlra.

   **AD FS 2016/2019**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **2012/2012 R2 AD FS**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > A Windows Server 2012 vagy 2012 R2 AD FS használatakor a kiállítási engedélyezési szabályok konfigurálásához a AD FS MMC beépülő modult kell használnia.

4. Ha az Internet Explorert vagy a Microsoft Edge böngészőt használja Azure Stack hub eléréséhez, figyelmen kívül hagyhatja a jogkivonat-kötéseket. Ellenkező esetben a bejelentkezési kísérletek sikertelenek lesznek. A AD FS-példányon vagy egy farmon, futtassa a következő parancsot:

   > [!note]  
   > Ez a lépés nem alkalmazható Windows Server 2012 vagy 2012 R2 AD FS használata esetén. Ebben az esetben nyugodtan kihagyhatja ezt a parancsot, és folytathatja az integrációt.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

   **AD FS 2002 és újabb rendszerekhez**

   > [!NOTE]
   > Ha `Add-ADFSRelyingPartyTrust` az ügyfél birtokolt ADFS-gazdagépen/farmon végez végrehajtást, először győződjön meg arról, hogy a TLS 1.2 kényszerítve van az ADFS-gazdagépen/farmon, és a kísérlet a következő hibaüzenetet eredményezi:

`Add-ADFSRelyingPartyTrust : The underlying connection was closed: An unexpected error occurred on a send.`

## <a name="spn-creation"></a>SPN létrehozása

Az egyszerű szolgáltatásnév (SPN) használatának megkövetelése számos esetben szükséges a hitelesítéshez. Az alábbiakban néhány példát láthat:

- A CLI használata AD FS Azure Stack hub üzembe helyezésével.
- A Azure Stack hub System Center felügyeleti csomagja AD FS-vel való üzembe helyezéskor.
- Azure Stack hub erőforrás-szolgáltatója AD FS-vel való üzembe helyezéskor.
- Különböző alkalmazások.
- Nem interaktív bejelentkezésre van szükség.

> [!Important]  
> AD FS csak az interaktív bejelentkezési munkameneteket támogatja. Ha nem interaktív bejelentkezésre van szüksége egy automatikus forgatókönyvhöz, SPN-t kell használnia.

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév létrehozása ad FShoz](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="configuration-rollback"></a>Konfiguráció visszaállítása

Ha olyan hiba történik, amely egy olyan állapotban hagyja a környezetet, ahol már nem végezhető hitelesítés, a visszaállítási lehetőség elérhető.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   A visszaállítási művelet futtatása után a rendszer az összes konfigurációs módosítást visszaállítja. Csak a beépített **CloudAdmin** -felhasználóval történő hitelesítés lehetséges.

   > [!IMPORTANT]
   > Konfigurálnia kell az alapértelmezett szolgáltatói előfizetés eredeti tulajdonosát.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>További naplók gyűjtése

Ha a parancsmagok bármelyike meghibásodik, a parancsmag használatával további naplókat is gyűjthet `Get-Azurestacklogs` .

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>Következő lépések

[Külső monitorozási megoldások integrálása](azure-stack-integrate-monitor.md)
