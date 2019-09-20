---
title: Azure Stack Datacenter-integráció – identitás
description: Ismerje meg, hogyan integrálható Azure Stack AD FS az adatközpontba AD FS
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: f51b0bdd4e433dd3083701e8cc967b3105d23ed6
ms.sourcegitcommit: 820ec8d10ddab1fee136397d3aa609e676f8b39d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71127515"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure Stack Datacenter-integráció – identitás

Az Azure Stack a Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával is üzembe helyezheti identitás-szolgáltatóként. A Azure Stack telepítése előtt el kell végeznie a választást. Egy csatlakoztatott forgatókönyvben kiválaszthatja az Azure AD-t vagy a AD FS. A leválasztott forgatókönyvek esetében csak AD FS támogatott.

> [!IMPORTANT]
> Az identitás-szolgáltatót nem lehet átváltani a teljes Azure Stack megoldás újbóli üzembe helyezése nélkül.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory összevonási szolgáltatások (AD FS) és gráf

A AD FS-ben való üzembe helyezése lehetővé teszi egy meglévő Active Directory erdőben lévő identitások hitelesítését a Azure Stack erőforrásaival. A meglévő Active Directory erdőben a AD FS központi telepítése szükséges, hogy lehetővé váljon AD FS összevonási megbízhatósági kapcsolat létrehozása.

A hitelesítés az identitás egyik része. A szerepkör alapú Access Control (RBAC) Azure Stack-ben való kezeléséhez konfigurálni kell a Graph összetevőt. Az erőforrásokhoz való hozzáférés delegálásakor a Graph-összetevő az LDAP protokoll használatával megkeresi a felhasználói fiókot a meglévő Active Directory erdőben.

![Azure Stack AD FS architektúra](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

A meglévő AD FS a fiók biztonsági jogkivonat-szolgáltatása (STS), amely jogcímeket küld a Azure Stack AD FS (az erőforrás STS) számára. Azure Stack az Automation létrehozza a jogcím-szolgáltatói megbízhatóságot a meglévő AD FS metaadat-végpontján.

A meglévő AD FSon konfigurálni kell egy függő entitás megbízhatóságát. Ezt a lépést az Automation nem hajtja végre, és az operátornak kell konfigurálnia. A AD FS Azure Stack VIP-végpontját a minta `https://adfs.<Region>.<ExternalFQDN>/`használatával lehet létrehozni.

A függő entitás megbízhatóságának konfigurációjában a Microsoft által biztosított jogcím-átalakítási szabályok konfigurálására is szükség van.

A Graph-konfigurációhoz meg kell adni egy olyan szolgáltatásfiókot, amely olvasási engedéllyel rendelkezik a meglévő Active Directory. Erre a fiókra a RBAC-forgatókönyvek engedélyezéséhez bemenetként kell megadni az Automation számára.

Az utolsó lépésben új tulajdonos van konfigurálva az alapértelmezett szolgáltatói előfizetéshez. Ennek a fióknak teljes hozzáférése van az összes erőforráshoz a Azure Stack felügyeleti portálra való bejelentkezéskor.

Követelmények:

|Összetevő|Követelmény|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Gráf-integráció beállítása

A Graph csak egyetlen Active Directory erdővel való integrációt támogatja. Ha több erdő létezik, a rendszer csak a konfigurációban megadott erdőt fogja használni a felhasználók és csoportok beolvasásához.

A következő információk szükségesek az Automation-paraméterek bemenetei számára:

|Paraméter|Üzembe helyezési munkalap paramétere|Leírás|Példa|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|Erdő teljes tartományneve AD FS|A cél Active Directory erdő teljes tartományneve<br>, amelyet integrálni szeretne|Contoso.com|
|`CustomADAdminCredentials`| |LDAP-olvasási engedéllyel rendelkező felhasználó|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Active Directory helyek konfigurálása

Active Directory több hellyel rendelkező üzemelő példányokhoz konfigurálja a legközelebbi Active Directory helyet a Azure Stack központi telepítéshez. A konfiguráció elkerüli, hogy a Azure Stack Graph szolgáltatás a távoli helyről származó globáliskatalógus-kiszolgáló használatával oldja fel a lekérdezéseket.

Adja hozzá a Azure Stack [nyilvános VIP hálózati](azure-stack-network.md#public-vip-network) alhálózatot a Azure Stackhoz legközelebb lévő Active Directory-helyhez. Ha például a Active Directory két, Seattle és Redmond nevű hellyel rendelkezik, Azure Stack a Seattle-i helyen helyezi üzembe, akkor a Azure Stack nyilvános VIP hálózati alhálózatot a Seattle-hez készült Active Directory webhelyhez adja hozzá.

További információ a Active Directory helyekről: [a hely topológiájának megtervezése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Ha a Active Directory egyetlen helyből áll, akkor kihagyhatja ezt a lépést. Ha van egy catch-all alhálózat konfigurálva, ellenőrizze, hogy a Azure Stack nyilvános VIP hálózati alhálózat nem része-e.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Felhasználói fiók létrehozása a meglévő Active Directoryban (nem kötelező)

Igény szerint létrehozhat egy fiókot a Graph szolgáltatáshoz a meglévő Active Directoryban. Ha még nem rendelkezik a használni kívánt fiókkal, hajtsa végre ezt a lépést.

1. A meglévő Active Directory hozza létre a következő felhasználói fiókot (javaslat):
   - **Felhasználónév**: graphservice
   - **Jelszó**: erős jelszó használata<br>Konfigurálja a jelszót, hogy soha ne járjon le.

   Nincs szükség különleges engedélyekre vagy tagságra.

#### <a name="trigger-automation-to-configure-graph"></a>Automatizálás elindítása a gráf konfigurálásához

Ehhez az eljáráshoz használjon olyan számítógépet az adatközpont-hálózaton, amely képes kommunikálni a rendszerjogosultságú végponttal Azure Stack.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a privilegizált végpont IP-címéhez. A hitelesítéshez használja a **CloudAdmin** hitelesítő adatait.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy csatlakozott a Kiemelt végponthoz, futtassa a következő parancsot: 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Ha a rendszer kéri, válassza ki a Graph szolgáltatáshoz használni kívánt felhasználói fiók hitelesítő adatait (például graphservice). A Register-DirectoryService parancsmag bemenetének az erdő erdő neve/legfelső tartományának kell lennie, nem pedig az erdőben lévő többi tartománynak.

   > [!IMPORTANT]
   > Várjon, amíg a hitelesítő adatok előugró ablaka (a lekéréses hitelesítő adatok nem támogatottak a privilegizált végponton), és adja meg a Graph szolgáltatás fiókjának hitelesítő adatait.

3. A **Register-DirectoryService** parancsmag olyan választható paramétereket tartalmaz, amelyeket bizonyos esetekben használhat, ha a meglévő Active Directory érvényesítése sikertelen. A parancsmag végrehajtásakor a rendszer ellenőrzi, hogy a megadott tartomány a legfelső szintű tartomány-e, a globáliskatalógus-kiszolgáló elérhető-e, és a megadott fiók olvasási hozzáférést biztosít.

   |Paraméter|Leírás|
   |---------|---------|
   |`-SkipRootDomainValidation`|Azt adja meg, hogy a rendszer az ajánlott legfelső szintű tartomány helyett alárendelt tartományt kell használni.|
   |`-Force`|Az összes ellenőrzési ellenőrzés mellőzése.|

#### <a name="graph-protocols-and-ports"></a>Graph protokollok és portok

A Azure Stack gráf szolgáltatása a következő protokollokat és portokat használja egy írható globáliskatalógus-kiszolgálóval (GC) és kulcsszolgáltató (KDC) való kommunikációhoz, amely képes feldolgozni a bejelentkezési kérelmeket a cél Active Directory erdőben.

A Azure Stack gráf szolgáltatása a következő protokollokat és portokat használja a célként megadott Active Directory való kommunikációhoz:

|Type|Port|Protocol|
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
|CustomAD<br>FSFederationMetadataEndpointUri|AD FS metaadat-URI|Összevonási metaadatok hivatkozása| https:\//AD01.contoso.com/federationmetadata/2007-06/federationmetadata.XML |
|SigningCertificateRevocationCheck|NA|Nem kötelező paraméter a CRL-ellenőrzés kihagyása érdekében|Nincsenek|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatizálás elindítása a jogcím-szolgáltatói megbízhatóság konfigurálásához Azure Stack

Ehhez az eljáráshoz használjon olyan számítógépet, amely képes kommunikálni a rendszerjogosultságú végponttal Azure Stack. A rendszer azt várta, hogy a fiók **STS AD FS** által használt tanúsítványa Azure stack megbízható.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és kapcsolódjon a privilegizált végponthoz.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy csatlakozott a Kiemelt végponthoz, futtassa a következő parancsot a környezetének megfelelő paraméterek használatával:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Futtassa a következő parancsot az alapértelmezett szolgáltatói előfizetés tulajdonosának frissítéséhez a környezetének megfelelő paraméterek használatával:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>AD FS integráció beállítása az összevonási metaadat-fájl biztosításával

Az 1807-es verziótól kezdődően ezt a módszert használja, ha az alábbi feltételek bármelyike igaz:

- A tanúsítványlánc különbözik a AD FS a Azure Stack többi végpontjának összehasonlítva.
- Nincs hálózati kapcsolat a meglévő AD FS-kiszolgáló Azure Stack AD FS példányával.

A következő információk szükségesek az Automation-paraméterek bemenetéhez:


|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcím-szolgáltató neve. Így jelenik meg a AD FS kezdőlapján.|Contoso|
|CustomADFSFederationMetadataFileContent|Metaadatok tartalma|$using: federationMetadataFileContent|

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

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatizálás elindítása a jogcím-szolgáltatói megbízhatóság konfigurálásához Azure Stack

Ehhez az eljáráshoz használjon olyan számítógépet, amely képes kommunikálni a Azure Stack rendszerjogosultságú végponttal, és hozzáfér az előző lépésben létrehozott metaadat-fájlhoz.

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

3. Futtassa a következő parancsot az alapértelmezett szolgáltatói előfizetés tulajdonosának frissítéséhez a környezetének megfelelő paraméterek használatával:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Ha elforgatja a tanúsítványt a meglévő AD FSon (a fiók STS-fiókjával), akkor újra be kell állítania a AD FS-integrációt. Akkor is be kell állítania az integrációt, ha a metaadat-végpont elérhető, vagy a metaadat-fájl megadásával lett konfigurálva.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Függő entitás konfigurálása meglévő AD FS üzemelő példányon (fiók STS)

A Microsoft olyan parancsfájlt biztosít, amely konfigurálja a függő entitás megbízhatóságát, beleértve a jogcím-átalakítási szabályokat is. A szkript használata nem kötelező, mert manuálisan is futtathatja a parancsokat.

A segítő parancsfájlt letöltheti [Azure stack eszközökről](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) a githubon.

Ha úgy dönt, hogy manuálisan futtatja a parancsokat, kövesse az alábbi lépéseket:

1. Másolja az alábbi tartalmat egy. txt fájlba (például c:\ClaimRules.txt mentve) az adatközpont AD FS példányán vagy a farm tagján:

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

2. Ellenőrizze, hogy engedélyezve van-e az extranetes és intranetes Windows Forms-alapú hitelesítés. Először ellenőrizze, hogy a már engedélyezve van-e a következő parancsmag futtatásával:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Előfordulhat, hogy az integrált Windows-hitelesítés (WIA) támogatott felhasználói ügynök sztringek elavultak, AD FS a központi telepítéshez szükség lehet a legújabb ügyfelek támogatására. A WIA által támogatott felhasználói ügynök sztringek frissítésével kapcsolatos további információkért tekintse meg az [intranetes űrlapalapú hitelesítés konfigurálása a WIA-t nem támogató eszközök esetében című](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia)cikket.<br>Az űrlapalapú hitelesítési házirend engedélyezésének lépéseit a cikk a [hitelesítési házirendek konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies)című cikkben ismertetett módon ismerteti.

3. A függő entitás megbízhatóságának hozzáadásához futtassa a következő Windows PowerShell-parancsot a AD FS példányán vagy egy farm tagján. Ügyeljen arra, hogy frissítse a AD FS végpontot, és mutasson az 1. lépésben létrehozott fájlra.

   **AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **2012/2012 R2 AD FS**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > A Windows Server 2012 vagy 2012 R2 AD FS használatakor a kiállítási engedélyezési szabályok konfigurálásához a AD FS MMC beépülő modult kell használnia.

4. Ha az Internet Explorer vagy a Microsoft Edge böngésző használatával fér hozzá a Azure Stackhoz, figyelmen kívül hagyhatja a jogkivonat-kötéseket. Ellenkező esetben a bejelentkezési kísérletek sikertelenek lesznek. A AD FS-példányon vagy egy farmon, futtassa a következő parancsot:

   > [!note]  
   > Ez a lépés nem alkalmazható Windows Server 2012 vagy 2012 R2 AD FS használata esetén. Ezt a parancsot nyugodtan kihagyhatja, és folytathatja az integrációt.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>SPN létrehozása

Az egyszerű szolgáltatásnév (SPN) használatának megkövetelése számos esetben szükséges a hitelesítéshez. Az alábbiakban néhány példát láthat:

- A CLI használata a Azure Stack AD FS üzembe helyezésével
- System Center felügyeleti csomag Azure Stack esetén AD FS
- Erőforrás-szolgáltatók Azure Stack a AD FS-vel való üzembe helyezéskor
- Különböző alkalmazások
- Nem interaktív bejelentkezésre van szükség

> [!Important]  
> AD FS csak az interaktív bejelentkezési munkameneteket támogatja. Ha nem interaktív bejelentkezésre van szüksége egy automatizált forgatókönyvhöz, SPN-t kell használnia.

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
   > Konfigurálnia kell az alapértelmezett szolgáltatói előfizetés eredeti tulajdonosát

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>További naplók gyűjtése

Ha a parancsmagok bármelyike meghibásodik, a `Get-Azurestacklogs` parancsmag használatával további naplókat is gyűjthet.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```


[Külső monitorozási megoldások integrálása](azure-stack-integrate-monitor.md)
