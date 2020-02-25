---
title: Az Azure App Service Azure Stack hub-beli üzembe helyezésének előfeltételei
description: Ismerkedjen meg az előfeltételként elvégzendő lépésekkel, mielőtt telepíti a Azure App Servicet Azure Stack hub-on.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 8d995550ead87f31a9024cc9c87ba45f0800a78d
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568452"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>Az App Service Azure Stack hub-beli üzembe helyezésének előfeltételei

A Azure App Service Azure Stack hub-on való üzembe helyezése előtt végre kell hajtania a jelen cikkben ismertetett előfeltételeket.

> [!IMPORTANT]
> Alkalmazza az 1910-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,8 telepítése előtt.

## <a name="download-the-installer-and-helper-scripts"></a>A telepítő és a segítő parancsfájlok letöltése

1. Töltse le a [App Service Azure stack hub üzembe helyezési segítő parancsfájljaiban](https://aka.ms/appsvconmashelpers).
2. Töltse le a [app Servicet Azure stack hub telepítőn](https://aka.ms/appsvconmasinstaller).
3. Bontsa ki a fájlokat a Helper scripts. zip fájlból. A következő fájlok és mappák vannak kibontva:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Modulok mappa
     - GraphAPI.psm1

## <a name="download-items-from-the-azure-marketplace"></a>Elemek letöltése az Azure Marketplace-ről

A Azure App Service on Azure Stack hub-on az [Azure Marketplace](azure-stack-download-azure-marketplace-item.md)-en kell letölteni az elemeket, így azok elérhetővé válnak az Azure stack hub piactéren. Ezeket az elemeket le kell tölteni, mielőtt megkezdené az üzembe helyezést vagy a Azure App Service frissítését Azure Stack hub-on:

1. A Windows Server 2016 Datacenter virtuálisgép-lemezkép legújabb verziója.
2. Egyéni szkript kiterjesztése v 1.9.1 vagy újabb. Ez a virtuálisgép-bővítmény.

## <a name="get-certificates"></a>Tanúsítványok beolvasása

### <a name="azure-resource-manager-root-certificate-for-azure-stack-hub"></a>Azure Stack hub főtanúsítványának Azure Resource Manager

Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet egy olyan számítógépen, amely elérheti az Azure Stack hub integrált rendszer-vagy ASDK-gazdagépen található privilegizált végpontot.

Futtassa a *Get-AzureStackRootCert. ps1* parancsfájlt abban a mappában, ahova kibontotta a segítő parancsfájlokat. A parancsfájl egy főtanúsítványt hoz létre ugyanabban a mappában, mint a parancsfájlt, amely App Service a tanúsítványok létrehozásához szükséges.

A következő PowerShell-parancs futtatásakor meg kell adnia a rendszerjogosultságú végpontot és a AzureStack\CloudAdmin. tartozó hitelesítő adatokat.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Kötelező | AzS-ERCS01 | Emelt szintű végpont |
| CloudAdminCredential | Kötelező | AzureStack\CloudAdmin | Tartományi fiók hitelesítő adatai Azure Stack hub Cloud rendszergazdák számára |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>A Azure App Service ASDK telepítéséhez szükséges tanúsítványok

A *create-AppServiceCerts. ps1* parancsfájl az Azure stack hub hitelesítésszolgáltatóval együttműködve hozza létre a négy, app Service szükséges tanúsítványt.

| Fájlnév | Használat |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Alapértelmezett SSL-tanúsítvány App Service |
| api.appservice.local.azurestack.external.pfx | App Service API SSL-tanúsítványa |
| ftp.appservice.local.azurestack.external.pfx | App Service közzétevő SSL-tanúsítványa |
| sso.appservice.local.azurestack.external.pfx | App Service Identity Application-tanúsítvány |

A tanúsítványok létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a ASDK-gazdagépre a AzureStack\AzureStackAdmin-fiók használatával.
2. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet.
3. Futtassa a *create-AppServiceCerts. ps1* parancsfájlt abban a mappában, ahova kibontotta a segítő parancsfájlokat. Ez a parancsfájl négy tanúsítványt hoz létre ugyanabban a mappában, mint a App Service a tanúsítványok létrehozásához szükséges parancsfájl.
4. Adjon meg egy jelszót a. pfx fájlok biztonságossá tételéhez, és jegyezze fel azt. A Azure Stack hub telepítőjének App Service kell megadnia.

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts. ps1 parancsfájl-paraméterek

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| pfxPassword | Kötelező | Null | A tanúsítvány titkos kulcsának megvédését segítő jelszó |
| DomainName | Kötelező | local.azurestack.external | Azure Stack hub-régió és tartomány utótagja |

### <a name="certificates-required-for-azure-stack-hub-production-deployment-of-azure-app-service"></a>A Azure App Service Azure Stack hub éles üzembe helyezéséhez szükséges tanúsítványok

Az erőforrás-szolgáltató éles környezetben való futtatásához a következő tanúsítványokat kell megadnia:

- Alapértelmezett tartományi tanúsítvány
- API-tanúsítvány
- Tanúsítvány közzététele
- Identitás tanúsítványa

#### <a name="default-domain-certificate"></a>Alapértelmezett tartományi tanúsítvány

Az alapértelmezett tartományi tanúsítvány az előtér-szerepkörre van helyezve. A tanúsítvány használatát Azure App Service helyettesítő vagy alapértelmezett tartományi kérést használó felhasználói alkalmazások. A tanúsítvány a forrás-ellenőrzési műveletekhez (kudu) is használatos.

A tanúsítványnak. pfx formátumúnak kell lennie, és három tárgyú helyettesítő tanúsítványnak kell lennie. Ez a követelmény lehetővé teszi, hogy az egyik tanúsítvány az alapértelmezett tartományra és az SCM-végpontra vonatkozzon a verziókövetés műveleteihez.

| Formátum | Példa |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>API-tanúsítvány

Az API-tanúsítvány a felügyeleti szerepkörbe kerül. Az erőforrás-szolgáltató az API-hívások biztonságossá tételéhez használja. A közzétételi tanúsítványnak tartalmaznia kell egy olyan tulajdonost, amely megfelel az API DNS-bejegyzésének.

| Formátum | Példa |
| --- | --- |
| az API. appservice.\<régió\>.\<tartománynév\>.\<bővítmény\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Tanúsítvány közzététele

A közzétevői szerepkör tanúsítványa biztosítja az FTPS forgalmát, amikor feltöltik a tartalmat. A közzétételi tanúsítványnak tartalmaznia kell egy, a FTPS DNS-bejegyzésnek megfelelő tárgyat.

| Formátum | Példa |
| --- | --- |
| FTP. appservice.\<régió\>.\<tartománynév\>.\<bővítmény\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitás tanúsítványa

Az Identity app tanúsítványa a következőket teszi lehetővé:

- Integráció a Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) könyvtár, Azure Stack hub és App Service között a számítási erőforrás-szolgáltatóval való integráció támogatásához.
- Egyszeri bejelentkezési forgatókönyvek az Azure Stack hub Azure App Serviceán belüli speciális fejlesztői eszközökhöz.

Az identitás tanúsítványának tartalmaznia kell egy tulajdonost, amely megfelel a következő formátumnak.

| Formátum | Példa |
| --- | --- |
| SSO. appservice.\<régió\>.\<tartománynév\>.\<bővítmény\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Tanúsítványok ellenőrzése

A App Service erőforrás-szolgáltató üzembe helyezése előtt ellenőrizze [a használni kívánt tanúsítványokat](azure-stack-validate-pki-certs.md) a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker)elérhető Azure stack hub Readiness-ellenőrző eszköz használatával. Az Azure Stack hub Readiness-ellenőrző eszköz ellenőrzi, hogy a generált PKI-tanúsítványok megfelelőek-e App Service központi telepítéshez.

Az ajánlott eljárás az, ha a szükséges [Azure stack hub PKI-tanúsítványokkal](azure-stack-pki-certs.md)dolgozik, érdemes megterveznie a szükséges időt a tanúsítványok teszteléséhez és kikiadásához.

## <a name="virtual-network"></a>Virtuális hálózat

> [!NOTE]
> Az egyéni virtuális hálózatok előállítása nem kötelező, mert az Azure Stack hub-beli Azure App Service létre tudja hozni a szükséges virtuális hálózatot, de a nyilvános IP-címeken keresztül kommunikálni kell az SQL-sel és a fájlkiszolgálón.

Azure App Service on Azure Stack hub lehetővé teszi az erőforrás-szolgáltató központi telepítését egy meglévő virtuális hálózatra, vagy lehetővé teszi a virtuális hálózatok létrehozását a telepítés részeként. Egy meglévő virtuális hálózat használata lehetővé teszi a belső IP-címek használatát a fájlkiszolgálón való kapcsolódáshoz, és az Azure Stack hub Azure App Service szükséges SQL Server. A virtuális hálózatot a következő címtartomány és alhálózatok konfigurálásával kell konfigurálni a Azure App Service Azure Stack hub-on való telepítése előtt:

Virtuális hálózat –/16

Alhálózatok

- ControllersSubnet/24
- ManagementServersSubnet /24
- FrontEndsSubnet/24
- PublishersSubnet /24
- WorkersSubnet/21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>A szükséges fájlkiszolgáló és SQL Server licencelési problémái

A Azure Stack hub Azure App Service a szükséges fájlkiszolgáló és a SQL Server működéséhez.  Ingyenesen használhatja a Azure Stack hub központi telepítésén kívül található meglévő erőforrásokat, vagy az Azure Stack hub alapértelmezett szolgáltatói előfizetésén belül helyezhet üzembe erőforrásokat.

Ha úgy dönt, hogy az Azure Stack hub alapértelmezett szolgáltatói előfizetésén belül helyezi üzembe az erőforrásokat, akkor az adott erőforráshoz tartozó licencek (Windows Server-licencek és SQL Server-licencek) a Azure Stack hub-beli Azure App Service díjait tartalmazzák következő korlátozások:

- az infrastruktúra üzembe helyezése az **alapértelmezett szolgáltatói előfizetésben**történik;
- az infrastruktúrát kizárólag az Azure Stack hub erőforrás-szolgáltató Azure App Service használja.  Más munkaterhelések, adminisztratív (más erőforrás-szolgáltatók, például SQL-RP) vagy bérlő (például: bérlői alkalmazások, amelyeknek adatbázisra van szükségük) használata engedélyezett az infrastruktúra használatának elvégzésére.

## <a name="prepare-the-file-server"></a>A fájlkiszolgáló előkészítése

Azure App Service egy fájlkiszolgáló használatát igényli. Éles környezetekben a fájlkiszolgáló számára úgy kell beállítani, hogy legyen elérhető, és képes legyen a hibák kezelésére.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>A fájlkiszolgáló gyors üzembe helyezési sablonja Azure App Service a ASDK-on.

Csak ASDK üzemelő példányok esetén a [példa Azure Resource Manager központi telepítési sablonnal](https://aka.ms/appsvconmasdkfstemplate) telepítheti a konfigurált egycsomópontos fájlkiszolgáló-kiszolgálót. Az egycsomópontos fájlkiszolgáló munkacsoportban lesz.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Gyors üzembe helyezési sablon a jól elérhető fájlkiszolgálón és SQL Server

Mostantól elérhető egy olyan [hivatkozási architektúra gyors sablonja](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) , amely egy fájlkiszolgáló és SQL Server telepítését végzi. Ez a sablon támogatja a Active Directory infrastruktúrát egy olyan virtuális hálózatban, amely a Azure App Service a Azure Stack hub-on való, magasan elérhető központi telepítésének támogatására van konfigurálva.

### <a name="steps-to-deploy-a-custom-file-server"></a>Egyéni fájlkiszolgáló üzembe helyezésének lépései

>[!IMPORTANT]
> Ha úgy dönt, hogy a App Servicet egy meglévő virtuális hálózaton telepíti, a fájlkiszolgálón egy különálló alhálózatba kell helyeznie App Service.

>[!NOTE]
> Ha úgy döntött, hogy egy fájlkiszolgálót telepít a fent említett rövid útmutató-sablonok valamelyikével, kihagyhatja ezt a szakaszt, mivel a fájlkiszolgálók a sablon központi telepítésének részeként vannak konfigurálva.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Csoportok és fiókok kiépítése Active Directory

1. Hozza létre a következő Active Directory globális biztonsági csoportokat:

   - FileShareOwners
   - FileShareUsers

2. Hozza létre a következő Active Directory fiókokat szolgáltatásfiókokként:

   - FileShareOwner
   - FileShareUser

   Ajánlott biztonsági eljárásként ezeknek a fiókoknak (és az összes webes szerepkör esetében) a felhasználóknak egyedinek kell lenniük, és erős felhasználóneveket és jelszavakat kell rendelkezniük. Állítsa be a jelszavakat a következő feltételekkel:

   - A **jelszó engedélyezése soha nem jár le**.
   - A **felhasználó engedélyezése nem változtathatja**meg a jelszót.
   - **A következő bejelentkezéskor a felhasználónak le kell változtatnia a jelszót**.

3. Adja hozzá a fiókokat a csoporttagságok a következő módon:

   - Adja hozzá a **FileShareOwner** a **FileShareOwners** -csoporthoz.
   - Adja hozzá a **FileShareUser** a **FileShareUsers** -csoporthoz.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Csoportok és fiókok kiépítése munkacsoportban

>[!NOTE]
> Fájlkiszolgáló konfigurálásakor futtassa a következő parancsokat egy **rendszergazdai parancssorból**. <br>***Ne használja a PowerShellt.***

A Azure Resource Manager sablon használatakor a felhasználók már létre vannak hozva.

1. Futtassa a következő parancsokat a FileShareOwner és a FileShareUser fiókok létrehozásához. Cserélje le a `<password>`t a saját értékeire.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Állítsa be a fiókok jelszavait úgy, hogy soha ne járjanak le az alábbi WMIC-parancsok futtatásával:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Hozza létre a FileShareUsers és a FileShareOwners helyi csoportokat, és adja hozzá a fiókokat az első lépésben:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>A tartalom megosztásának kiépítése

A tartalom megosztása bérlői webhely tartalmát tartalmazza. A tartalmi megosztás egyetlen fájlkiszolgálón való kiépítésének eljárása azonos a Active Directory és a munkacsoportbeli környezetek esetében is. A Active Directory feladatátvételi fürtök esetében azonban eltér.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>A tartalom megosztásának kiépítése egyetlen fájlkiszolgálón (Active Directory vagy munkacsoport)

Egyetlen fájlkiszolgálón futtassa a következő parancsokat egy rendszergazda jogú parancssorban. Cserélje le a `C:\WebSites` értékét a környezetének megfelelő elérési utakra.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Hozzáférés-vezérlés konfigurálása a megosztásokhoz

Futtassa a következő parancsokat egy rendszergazda jogú parancssorban a fájlkiszolgálón vagy a feladatátvevő fürt csomópontján, amely az aktuális fürterőforrás-tulajdonos. Cserélje le a dőlt betűvel jelölt értékeket a környezetre jellemző értékekre.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Munkacsoport

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>A SQL Server példány előkészítése

>[!NOTE]
> Ha úgy döntött, hogy üzembe helyezi a magas rendelkezésre állású fájlkiszolgáló és a SQL Server rövid útmutató sablonját, kihagyhatja ezt a szakaszt, mert a sablon üzembe helyezi és konfigurálja SQL Server egy HA konfigurációban.

Az Azure Stack hub üzemeltetési és mérési adatbázisának Azure App Service elő kell készítenie egy SQL Server példányt a App Service-adatbázisok tárolására.

ASDK üzemelő példányok esetén SQL Server Express 2014 SP2 vagy újabb verzió használható. A SQL Servert úgy kell konfigurálni, hogy támogassa a **vegyes módú** hitelesítést, mert az Azure stack hub-beli app Service **nem** támogatja a Windows-hitelesítést.

Éles és magas rendelkezésre állás érdekében a SQL Server 2014 SP2 vagy újabb verziójának teljes verzióját kell használnia, engedélyezni kell a vegyes módú hitelesítést, és magas rendelkezésre állású [konfigurációban](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)kell üzembe helyezni.

Azure Stack hub Azure App Service SQL Server példányának elérhetőnek kell lennie az összes App Service szerepkörből. SQL Server a Azure Stack hub alapértelmezett szolgáltatói előfizetésében is üzembe helyezhető. Vagy igénybe veheti a szervezeten belüli meglévő infrastruktúrát (feltéve, hogy van kapcsolata Azure Stack hub-hoz). Ha Azure Marketplace-rendszerképet használ, ne feledje, hogy ennek megfelelően konfigurálja a tűzfalat.

> [!NOTE]
> A piactér felügyeleti funkciójával számos SQL IaaS virtuálisgép-lemezkép érhető el. Győződjön meg arról, hogy mindig letölti az SQL IaaS bővítmény legújabb verzióját, mielőtt üzembe helyezi a virtuális gépet a Piactéri elemmel. Az SQL-lemezképek ugyanazok, mint az Azure-ban elérhető SQL virtuális gépek. A rendszerképekből létrehozott SQL virtuális gépek esetében a IaaS-bővítmény és a hozzá tartozó portál-fejlesztések olyan funkciókat biztosítanak, mint az automatikus javítás és a biztonsági mentési funkciók.
>
> Bármelyik SQL Server szerepkörhöz használhat alapértelmezett példányt vagy megnevezett példányt. Ha elnevezett példányt használ, akkor a SQL Server Browser-szolgáltatást manuálisan indítsa el, és nyissa meg a 1434-es portot.

A App Service telepítője ellenőrizni fogja, hogy a SQL Server rendelkezik-e az adatbázis-tárolók beállításával. Ha engedélyezni szeretné az adatbázis-tárolást azon a SQL Serveron, amely a App Service-adatbázisokat fogja tárolni, futtassa az alábbi SQL-parancsokat:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> Ha úgy dönt, hogy a App Servicet egy meglévő virtuális hálózaton telepíti, a SQL Server a App Service és a fájlkiszolgáló különálló alhálózatára kell telepíteni.
>

## <a name="create-an-azure-active-directory-app"></a>Azure Active Directory-alkalmazás létrehozása

Konfigurálja az Azure AD egyszerű szolgáltatást a következő műveletek támogatásához:

- Virtuálisgép-méretezési csoport integrálása a feldolgozói rétegekbe.
- Egyszeri bejelentkezés a Azure Functions portál és a speciális fejlesztői eszközök számára.

Ezek a lépések csak az Azure AD-védelemmel ellátott Azure Stack hub-környezetekre vonatkoznak.

A rendszergazdáknak be kell állítania az SSO-t:

- Engedélyezze a fejlett fejlesztői eszközöket App Serviceon (kudu) belül.
- A Azure Functions portál használatának engedélyezése.

Az alábbi lépéseket követve hozza létre az egyszerű szolgáltatásnevet az Azure AD-bérlőben:

1. PowerShell-példány megnyitása azurestack\AzureStackAdmin.
2. Nyissa meg az [előfeltételként](azure-stack-app-service-before-you-get-started.md)letöltött és kibontott parancsfájlok helyét.
3. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).
4. Futtassa a **create-AADIdentityApp. ps1** parancsfájlt. Amikor a rendszer kéri, adja meg az Azure Stack hub üzembe helyezéséhez használt Azure AD-bérlői azonosítót. Írja be például a következőt: **myazurestack.onmicrosoft.com**.
5. A **hitelesítő adatok** ablakban adja meg az Azure ad-szolgáltatás rendszergazdai fiókját és jelszavát. Kattintson az **OK** gombra.
6. Adja meg a tanúsítvány fájljának elérési útját és a tanúsítvány jelszavát a [korábban létrehozott tanúsítványhoz](azure-stack-app-service-before-you-get-started.md). Az ehhez a lépéshez létrehozott tanúsítvány alapértelmezés szerint **SSO. appservice. local. azurestack. external. pfx**.
7. Jegyezze fel a PowerShell kimenetében visszaadott alkalmazás AZONOSÍTÓját. A következő lépésekben szereplő AZONOSÍTÓval megadhatja az alkalmazás engedélyeit, és a telepítés során. 
8. Nyisson meg egy új böngészőablakot, és jelentkezzen be a [Azure Portalba](https://portal.azure.com) Azure Active Directory szolgáltatás-rendszergazdaként.
9. Nyissa meg a Azure Active Directory szolgáltatást.
10. Válassza az **alkalmazás-regisztrációk** lehetőséget a bal oldali ablaktáblán.
11. Keresse meg a 7. lépésben feljegyzett alkalmazás AZONOSÍTÓját. 
12. Válassza ki a App Service alkalmazás regisztrációját a listából.
13. Válassza ki az **API-engedélyeket** a bal oldali ablaktáblán.
14. Válassza a **rendszergazdai jóváhagyás megadása \<bérlő\>** , ahol a \<bérlő\> az Azure ad-bérlő neve. Az **Igen gombra**kattintva erősítse meg a jóváhagyást.

```powershell
    Create-AADIdentityApp.ps1
```

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| DirectoryTenantName | Kötelező | Null | Azure AD-bérlő azonosítója. Adja meg a GUID azonosítót vagy a karakterláncot. Ilyen például a myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Kötelező | Null | Felügyeleti Azure Resource Manager végpont. Példa: adminmanagement. local. azurestack. external. |
| TenantARMEndpoint | Kötelező | Null | Bérlői Azure Resource Manager végpont. Ilyen például a Management. local. azurestack. external. |
| AzureStackAdminCredential | Kötelező | Null | Az Azure AD szolgáltatás rendszergazdai hitelesítő adatai. |
| CertificateFilePath | Kötelező | Null | A korábban létrehozott Identity Application Certificate fájl **teljes elérési útja** . |
| CertificatePassword | Kötelező | Null | A tanúsítvány titkos kulcsának megvédését segítő jelszó. |
| Környezet | Optional | AzureCloud | A támogatott felhőalapú környezet neve, amelyben a cél Azure Active Directory Graph szolgáltatás elérhető.  Megengedett értékek: "AzureCloud", "AzureChinaCloud", "AzureUSGovernment", "AzureGermanCloud".|

## <a name="create-an-active-directory-federation-services-app"></a>Active Directory összevonási szolgáltatások (AD FS)-alkalmazás létrehozása

AD FS által védett Azure Stack hub-környezetek esetében konfigurálnia kell egy AD FS egyszerű szolgáltatást a következő műveletek támogatásához:

- Virtuálisgép-méretezési csoport integrálása a feldolgozói rétegekbe.
- Egyszeri bejelentkezés a Azure Functions portál és a speciális fejlesztői eszközök számára.

A rendszergazdáknak be kell állítania az SSO-t:

- Konfiguráljon egy egyszerű szolgáltatásnevet a virtuálisgép-méretezési csoportnak a feldolgozói rétegekbe való integrálásához.
- Engedélyezze a fejlett fejlesztői eszközöket App Serviceon (kudu) belül.
- A Azure Functions portál használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. PowerShell-példány megnyitása azurestack\AzureStackAdmin.
2. Nyissa meg az [előfeltételként](azure-stack-app-service-before-you-get-started.md)letöltött és kibontott parancsfájlok helyét.
3. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).
4. Futtassa a **create-ADFSIdentityApp. ps1** parancsfájlt.
5. A **hitelesítő adatok** ablakban adja meg AD FS Felhőbeli rendszergazdai fiókját és jelszavát. Kattintson az **OK** gombra.
6. Adja meg a tanúsítvány fájljának elérési útját és a tanúsítvány jelszavát a [korábban létrehozott tanúsítványhoz](azure-stack-app-service-before-you-get-started.md). Az ehhez a lépéshez létrehozott tanúsítvány alapértelmezés szerint **SSO. appservice. local. azurestack. external. pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| AdminArmEndpoint | Kötelező | Null | Felügyeleti Azure Resource Manager végpont. Példa: adminmanagement. local. azurestack. external. |
| PrivilegedEndpoint | Kötelező | Null | Emelt szintű végpont. Példa: AzS-ERCS01. |
| CloudAdminCredential | Kötelező | Null | Tartományi fiók hitelesítő adatai Azure Stack hub felhőalapú rendszergazdái számára. Példa Azurestack\CloudAdmin. |
| CertificateFilePath | Kötelező | Null | Az Identity alkalmazás tanúsítvány PFX-fájljának **teljes elérési útja** . |
| CertificatePassword | Kötelező | Null | A tanúsítvány titkos kulcsának megvédését segítő jelszó. |

## <a name="next-steps"></a>Következő lépések

[A App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)
