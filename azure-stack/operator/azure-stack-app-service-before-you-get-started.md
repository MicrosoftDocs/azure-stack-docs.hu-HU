---
title: Az Azure Stack App Service üzembe helyezése előtt |} A Microsoft Docs
description: Lépést végre kell hajtania, az Azure Stack App Service üzembe helyezése
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: bb9d49c7feebc03f0f2f5bbaca084e9141f601e9
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269201"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Mielőtt elkezdené, az Azure Stack App Service-szel

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure App Service az Azure Stack üzembe helyezése, előtt elvégzése szükséges az ebben a cikkben előfeltételként felsorolt lépéseket.

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1904 frissítés alkalmazása, vagy a legújabb Azure Stack Development Kit (ASDK) üzembe helyezése, Azure App Service 1.6-os üzembe helyezése előtt.

## <a name="download-the-installer-and-helper-scripts"></a>A telepítő és a segítő szkripteket letöltése

1. Töltse le a [App Service-ben az Azure Stack üzembe helyezési segítő szkripteket](https://aka.ms/appsvconmashelpers).
2. Töltse le a [App Service-ben az Azure Stack-telepítő](https://aka.ms/appsvconmasinstaller).
3. Csomagolja ki a fájlokat a segítő szkripteket tartalmazó .zip fájl. Ki kell olvasni a következő fájlok és mappák:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Modulok mappa
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Szindikálása az egyéni szkriptek futtatására szolgáló bővítmény a Marketplace-ről

Az Azure Stack az Azure App Service egyéni szkriptek futtatására szolgáló bővítmény v1.9.1 igényel.  A bővítmény kell [hírcsatorna-a Marketplace-ről](azure-stack-download-azure-marketplace-item.md) előtt a központi telepítés vagy frissítés az Azure App Service kezdve az Azure Stackben

## <a name="get-certificates"></a>Tanúsítványok beolvasása

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Az Azure Resource Manager főtanúsítványt az Azure Stackhez

Nyisson meg egy emelt szintű PowerShell-munkamenetet egy olyan számítógépen, a kiemelt végponthoz az Azure Stackkel integrált rendszereknél vagy az Azure Stack Development Kit gazdagép elérheti.

Futtassa a *Get-AzureStackRootCert.ps1* parancsfájlt a mappát, amelyikbe kibontotta a segítő szkripteket. A parancsfájl ugyanabban a mappában, amelyet az App Service tanúsítványok létrehozására szolgáló szkriptet hoz létre egy főtanúsítványt.

A következő PowerShell-parancs futtatásakor kell a AzureStack\CloudAdmin adja meg a kiemelt végponthoz és a hitelesítő adatokat.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Kötelező | AzS-ERCS01 | Kiemelt végponthoz |
| CloudAdminCredential | Kötelező | AzureStack\CloudAdmin | Tartományi fiók hitelesítő adatait az Azure Stack-felhő rendszergazdái |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Az Azure App Service ASDK telepítéshez szükséges tanúsítványok

A *létrehozás-AppServiceCerts.ps1* parancsfájl együttműködik az Azure Stack hitelesítésszolgáltató a négy olyan tanúsítványokat hoznak létre az App Service van szüksége.

| Fájlnév | Használat |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service API alapértelmezett SSL-tanúsítványa |
| api.appservice.local.azurestack.external.pfx | App Service API SSL-tanúsítvány |
| ftp.appservice.local.azurestack.external.pfx | App Service publisher SSL certificate |
| sso.appservice.local.azurestack.external.pfx | App Service-ben identitás alkalmazástanúsítványa |

A tanúsítványok létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack Development Kit állomás AzureStack\AzureStackAdmin fiókkal.
2. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet.
3. Futtassa a *létrehozás-AppServiceCerts.ps1* parancsfájlt a mappát, amelyikbe kibontotta a segítő szkripteket. Ez a szkript létrehoz négy tanúsítványok ugyanabban a mappában, amelyet az App Service tanúsítványok létrehozására szolgáló szkriptet.
4. Adjon meg egy jelszót a .pfx fájlok védelmét, és jegyezze fel azt. Adja meg az App Service az Azure Stack-telepítő a kell.

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 script parameters

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| pfxPassword | Kötelező | Null | Jelszó, amely segít megvédeni a tanúsítvány titkos kulcsa |
| Tartománynév | Kötelező | local.azurestack.external | Az Azure Stack régió és a tartományi utótag |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Az Azure Stack éles környezetben az Azure App Service-ben a szükséges tanúsítványok

Futtassa az erőforrás-szolgáltató éles környezetben, meg kell adni a következő tanúsítványokra:

- Alapértelmezett tartomány tanúsítványt
- API-tanúsítvány
- Közzétételi tanúsítványt
- Identitás tanúsítványa.

#### <a name="default-domain-certificate"></a>Alapértelmezett tartomány tanúsítványt

Az alapértelmezett tartományi tanúsítvány el van helyezve az előtér szerepkör. Felhasználói alkalmazások helyettesítő vagy az alapértelmezett tartományi kérelem Azure App Service-ben a tanúsítvány használatához. A tanúsítvány is szolgál a forrás-ellenőrzési műveletek (Kudu).

A tanúsítvány .pfx formátumúnak kell lennie, és a egy három-tulajdonos helyettesítő tanúsítványt kell lennie. Ez a követelmény lehetővé teszi, hogy egy tanúsítványt, hogy biztosítsák az alapértelmezett tartomány és a forrás ellenőrzési műveletek SCM végpontját.

| Formátum | Példa |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>API-tanúsítvány

Az API-tanúsítvány a felügyeleti szerepkör el van helyezve. Az erőforrás-szolgáltató a biztonságos API-hívásokat használja azt. A tanúsítvány közzététele egy témát, amely megfelel az API DNS-bejegyzést kell tartalmaznia.

| Formátum | Példa |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Közzétételi tanúsítványt

A tanúsítvány a közzétevői szerepkör tartalmat feltölteni, amikor az alkalmazástulajdonosok védelemmel látja el az FTPS-forgalmat. A tanúsítvány közzététele egy témát, amely megfelel a FTPS DNS-bejegyzést kell tartalmaznia.

| Formátum | Példa |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitás tanúsítványa.

Az identitás-alkalmazáshoz tartozó tanúsítvány a következőket:

- Integráció az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) directory, Azure Stack és App Service-ben támogatásához a számítási erőforrás-szolgáltató-integráció között.
- Egyszeri bejelentkezéses forgatókönyvek használatához az Azure App Service az Azure Stacken speciális fejlesztői eszközöket.

Identitás tanúsítványa egy témát, amely megfelel a következő formátumban kell tartalmaznia.

| Formátum | Példa |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Tanúsítványok ellenőrzése

Az app service erőforrás-szolgáltató üzembe helyezése előtt kell [használandó tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) elérhető az Azure Stack készültségi ellenőrző eszköz használatával a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker). Az Azure Stack készültségi ellenőrző eszköz ellenőrzi, hogy a generált PKI-tanúsítványokat app services üzembe helyezési alkalmasak.

Ajánlott eljárásként, azokkal a szükséges munka során [Azure Stack PKI-tanúsítványokat](azure-stack-pki-certs.md), meg kell terveznie, hogy elegendő idő teszteléséhez, és adja ki újból tanúsítványok, ha szükséges.

## <a name="virtual-network"></a>Virtuális hálózat

> [!NOTE]
> Az üzem előtti létrehozása egy egyéni virtuális hálózat nem kötelező használni, mivel az Azure App Service az Azure Stacken hozhat létre a szükséges virtuális hálózati, de az SQL és a fájlkiszolgáló nyilvános IP-cím használatával kommunikálni kell.

Az Azure Stack az Azure App Service lehetővé teszi az erőforrás-szolgáltató üzembe helyezése meglévő virtuális hálózattal, vagy lehetővé teszi a virtuális hálózat létrehozása a központi telepítésének részeként. Meglévő virtuális hálózattal való csatlakozáshoz a fájlkiszolgáló és az Azure App Service az Azure Stacken szükséges az SQL server belső IP-címek használatát teszi lehetővé. A virtuális hálózat az Azure Stack az Azure App Service telepítése előtt a következő címtartományt és alhálózatokat kell konfigurálni:

Virtuálishálózat - /16

Alhálózatok

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Szükséges fájlkiszolgáló és az SQL Server licencelési megjegyzése

Az Azure App Service az Azure Stacken fájlkiszolgáló és a működéséhez az SQL Server szükséges.  Ön ingyenes kívül az Azure Stack üzemelő példány már meglévő erőforrásait, vagy az Azure Stack alapértelmezett szolgáltató előfizetésen belüli erőforrások üzembe helyezése.

Üzembe helyezése az Azure Stack alapértelmezett szolgáltató előfizetésen belüli erőforrások választja, ha ezeket az erőforrásokat (Windows Server-licencek és az SQL Server-licencekkel) licencét szerepelnek-e az Azure App Service az Azure Stacken költsége korlátozások:

- az infrastruktúra van üzembe helyezve a **szolgáltatói előfizetés alapértelmezett**;
- az infrastruktúra kizárólag az Azure App Service az Azure Stack erőforrás-szolgáltatót használják.  Más számítási feladatok, rendszergazdai (egyéb erőforrás-szolgáltatók, például az SQL-RP) vagy bérlői (például bérlői alkalmazások, amelyek egy adatbázis szükséges), nem engedélyezettek, győződjön meg arról, hogy az infrastruktúra használata.

## <a name="prepare-the-file-server"></a>A fájlkiszolgáló előkészítése

Az Azure App Service egy fájlkiszolgálóhoz van szükség. Éles környezetekben üzemelő példányok a fájlkiszolgáló kell konfigurálni kell a magas rendelkezésre állású és hibák kezelésére képes.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Gyorsindítási sablon fájlkiszolgáló ASDK futó Azure App Service-környezetekben.

Csak az Azure Stack Development Kit központi telepítése esetén használhatja a [példa az Azure Resource Manager üzembe helyezési sablon](https://aka.ms/appsvconmasdkfstemplate) konfigurált egy csomópontos fájlkiszolgáló üzembe helyezéséhez. Egy csomópontos fájlkiszolgáló munkacsoport lesz.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Magas rendelkezésre állású fájl kiszolgáló és az SQL Server gyorsindítási sablon

A [referencia architektúra gyorsindítási sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) már elérhető, amely telepíti a fájlkiszolgáló, az SQL Server, az Active Directory támogatása a virtuális hálózati infrastruktúra úgy konfigurálva, hogy egy magas rendelkezésre állású telepítésének támogatása Az Azure App Service az Azure Stackben.

### <a name="steps-to-deploy-a-custom-file-server"></a>Egy egyéni fájlkiszolgáló telepítése

>[!IMPORTANT]
> Ha egy meglévő virtuális hálózatot az App Service üzembe helyezése, a fájlkiszolgáló az App Service-ből egy külön alhálózatot kell telepíteni.

>[!NOTE]
> Ha úgy döntött, a fájlkiszolgálók üzembe helyezése a fent említett gyorsindítási sablonok valamelyikét használja, kihagyhatja a ebben a szakaszban, a kiszolgálók úgy vannak konfigurálva, a sablon központi telepítésének részeként fájllal.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Csoportok és az Active Directory fiókok kiépítése

1. Hozza létre a következő Active Directory globális biztonsági csoportokat:

   - FileShareOwners
   - FileShareUsers

2. Hozzon létre a következő Active Directory-fiókokat szolgáltatásfióknak:

   - FileShareOwner
   - FileShareUser

   Biztonsági ajánlott eljárás, a felhasználók ezen fiókok (és az összes webes szerepkörök) kell egyedinek lennie, és erős felhasználónevei és jelszavai között van. Állítsa be a jelszavakat a következő feltételeknek:

   - Engedélyezése **jelszó sohasem jár le**.
   - Engedélyezése **nem lehet változtatni a jelszót**.
   - Tiltsa le **kell változtatni a jelszót a következő bejelentkezéskor**.

3. A következő hozzáadhatja a fiókokat a csoporttagság:

   - Adjon hozzá **FileShareOwner** , a **FileShareOwners** csoport.
   - Adjon hozzá **FileShareUser** , a **FileShareUsers** csoport.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Csoportok és munkacsoportban lévő fiókok kiépítése

>[!NOTE]
> Ha egy fájlkiszolgáló, futtassa az alábbi parancsokat a konfigurálásakor egy **rendszergazdai parancssorból**. <br>***Ne használja a PowerShell.***

Az Azure Resource Manager-sablon használatakor a felhasználók már jönnek létre.

1. Futtassa az alábbi parancsokat a FileShareOwner és FileShareUser fiókok létrehozását. Cserélje le `<password>` a saját értékeire.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Állítsa be a jelszavakat a fiókokat soha ne járjon le a következő WMIC-parancsok futtatásával:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Hozza létre a helyi csoportokat FileShareUsers és FileShareOwners, és adja hozzá a fiókokat az első lépésben őket:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>A tartalommegosztás kiépítése

A tartalommegosztás bérlői webhely tartalmát tartalmazza. A tartalommegosztás a fájlkiszolgáló telepítéséhez az eljárás megegyezik az Active Directory és a munkacsoport-környezeteket. De másik feladatátvevő fürt az Active Directoryban.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>A tartalommegosztás az egyetlen fájlkiszolgáló (Active Directory vagy munkacsoporthoz) üzembe helyezése

Egyetlen fájlkiszolgáló futtassa a következő parancsokat egy rendszergazda jogú parancssorba. Cserélje le az értéket a `C:\WebSites` a környezetben a megfelelő elérési úttal.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>A megosztások hozzáférés-vezérlés konfigurálása

A következő parancsokat egy rendszergazda jogú parancssorban a fájlkiszolgálón vagy a feladatátvevő fürtcsomóponton, amely az aktuális fürt erőforrás tulajdonosa. Cserélje le a dőlt értékek értékek, amelyek az adott környezetre jellemző.

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

## <a name="prepare-the-sql-server-instance"></a>Az SQL Server-példány előkészítése

>[!NOTE]
> Ha úgy döntött, a rövid útmutató sablon üzembe helyezéséhez a magas rendelkezésre állású fájl kiszolgáló és az SQL Server kihagyhatja ebben a szakaszban, a sablon üzembe helyez, és beállítja az SQL Server magas rendelkezésre ÁLLÁSÚ konfigurációban.

Az Azure App Service az Azure Stack üzemeltetési és a mérési adatbázis, az SQL Server-példányt, amely tárolja az App Service-adatbázisokat elő kell készítenie.

Az Azure Stack Development Kit központi telepítéséhez, használhatja az SQL Server Express 2014 SP2 vagy újabb.

Éles és magas rendelkezésre állás céljából kell használjon teljes verzióját az SQL Server 2014 SP2 vagy újabb, vegyes módú hitelesítés engedélyezése, és végzi az üzembe helyezést egy [magas rendelkezésre állású konfigurációban](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Az SQL Server-példány az Azure App Service az Azure Stack App Service-ben az összes szerepkör elérhetőknek kell lenniük. Telepítheti az SQL Server az alapértelmezett szolgáltatója az előfizetésen belül az Azure Stackben. Lehetőség a szervezeten belül a meglévő infrastruktúra használata (feltéve, nincs kapcsolat Azure stackhez). Ha az Azure Marketplace-rendszerképpel használja, ne felejtse el ennek megfelelően konfigurálja a tűzfalat.

> [!NOTE]
> Egy szám, az SQL IaaS virtuális gépek lemezképeit a Marketplace-en felügyeleti szolgáltatáson keresztül érhetők el. Ellenőrizze, hogy Ön mindig Piactéri virtuális gép üzembe helyezése előtt töltse le a legújabb verzióját az SQL IaaS-bővítményt. Az SQL-rendszerképek ugyanazok, mint az SQL virtuális gépek az Azure-ban elérhető. Az SQL virtuális gépek-képekből létrehozott ezeket, az IaaS-bővítményt, és a megfelelő portál fejlesztések automatikus javítás és a biztonsági mentési funkcióit funkciókat biztosítanak.
>
> Az SQL Server-szerepkörök bármelyikéhez egy alapértelmezett vagy megnevezett példányt is használhatja. Ha egy megnevezett példányt használ, mindenképpen manuálisan indítsa el az SQL Server Browser szolgáltatást, és nyissa meg az 1434-es portot.

Az App Service-telepítő ellenőrzi az SQL Server adatbázis tartalmazási engedélyezve legyen. Ahhoz, hogy az SQL Server, az App Service-adatbázisokat üzemeltető adatbázis tartalmazás, a következő SQL-parancsok futtatása:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> Ha úgy dönt, hogy egy meglévő virtuális hálózatot az App Service üzembe helyezése az SQL Server, az App Service-ben és a fájlkiszolgáló egy külön alhálózatot kell telepíteni.
>

## <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Adja meg az Azure AD-szolgáltatásnév támogatásához a következő műveleteket:

- Virtuálisgép-méretezési csoport integráció beállítása a feldolgozói rétegek.
- Egyszeri bejelentkezés az Azure Functions fejlesztői portál és a speciális eszközök.

Ezeket a lépéseket csak az Azure AD által védett Azure Stack-környezet a alkalmazni.

A rendszergazdák egyszeri Bejelentkezést, hogy kell konfigurálni:

- Engedélyezze a speciális fejlesztői eszközök (Kudu) App szolgáltatáson belül.
- Az Azure Functions portálja használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\AzureStackAdmin.
2. Lépjen arra a helyre, azokat a parancsprogramokat, letöltött és kibontott az a [előfeltétel-ellenőrzési lépés](azure-stack-app-service-before-you-get-started.md).
3. [Az Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).
4. Futtassa a **létrehozás-AADIdentityApp.ps1** parancsfájlt. Ha kéri, adja meg az Azure AD-bérlő azonosítója, amely az Azure Stack üzemelő példány használja. Adja meg például **myazurestack.onmicrosoft.com**.
5. Az a **Credential** ablakban adja meg az Azure AD szolgáltatás-rendszergazdai fiókot és jelszót. Kattintson az **OK** gombra.
6. Adja meg a tanúsítványfájl elérési útja és a tanúsítvány jelszavát a [korábban létrehozott tanúsítvány](azure-stack-app-service-before-you-get-started.md). Alapértelmezés szerint ez a lépés a létrehozott tanúsítványt **sso.appservice.local.azurestack.external.pfx**.
7. A szkript létrehoz egy új alkalmazás a bérlő Azure AD-példányt. Jegyezze fel az Alkalmazásazonosítót, a PowerShell-kimeneti adja vissza. Ezt az információt a telepítés során kell.
8. Nyisson meg egy új böngészőablakot, és jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure Active Directory szolgáltatás-rendszergazdaként
9. Nyissa meg az erőforrás-szolgáltató Azure ad-ben.
10. Válassza ki **Alkalmazásregisztrációk**.
11. Keresse meg az Alkalmazásazonosítót, 7. lépés részeként adja vissza. Az App Service-alkalmazás szerepel a listán.
12. Válassza ki **alkalmazás** a listában.
13. Válassza ki **beállítások**.
14. Válassza ki **szükséges engedélyek** > **engedélyeket** > **Igen**.

```powershell
    Create-AADIdentityApp.ps1
```

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| DirectoryTenantName | Kötelező | Null | Az Azure AD-bérlő azonosítója. Adja meg a GUID Azonosítót vagy karakterlánc. Ez például akkor myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Kötelező | Null | Rendszergazdai Azure Resource Manager-végpontot. Ez például akkor adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Kötelező | Null | A bérlői Azure Resource Manager-végpontot. Ez például akkor management.local.azurestack.external. |
| AzureStackAdminCredential | Kötelező | Null | Az Azure AD szolgáltatás rendszergazdai hitelesítő adataihoz. |
| CertificateFilePath | Kötelező | Null | **Teljes elérési útja** , az identitás alkalmazástanúsítvány-fájlja korábban létrehozott. |
| CertificatePassword | Kötelező | Null | Olyan jelszót, amely segít megvédeni a tanúsítvány titkos kulcsa. |
| Környezet | Választható | AzureCloud | A neve, a támogatott Felhőbeli környezet, amelyben a cél Azure Active Directory Graph szolgáltatás érhető el.  Megengedett értékek: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory összevonási szolgáltatások alkalmazás létrehozása

Az Azure Stack-környezetekben az AD FS által védett konfigurálnia kell egy AD FS egyszerű szolgáltatást az alábbiak támogatásához:

- Virtuálisgép-méretezési csoport integráció beállítása a feldolgozói rétegek.
- Egyszeri bejelentkezés az Azure Functions fejlesztői portál és a speciális eszközök.

A rendszergazdák egyszeri Bejelentkezést, hogy kell konfigurálni:

- Konfigurálja a virtuális gép scale set-integráció egyszerű szolgáltatás a feldolgozói rétegek.
- Engedélyezze a speciális fejlesztői eszközök (Kudu) App szolgáltatáson belül.
- Az Azure Functions portálja használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\AzureStackAdmin.
2. Lépjen arra a helyre, azokat a parancsprogramokat, letöltött és kibontott az a [előfeltétel-ellenőrzési lépés](azure-stack-app-service-before-you-get-started.md).
3. [Az Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).
4. Futtassa a **létrehozás-ADFSIdentityApp.ps1** parancsfájlt.
5. Az a **Credential** ablakban adja meg az AD FS felhőalapú rendszergazdai fiók és jelszó. Kattintson az **OK** gombra.
6. Adja meg a tanúsítványfájl elérési útja és a tanúsítvány jelszavát a [korábban létrehozott tanúsítvány](azure-stack-app-service-before-you-get-started.md). Alapértelmezés szerint ez a lépés a létrehozott tanúsítványt **sso.appservice.local.azurestack.external.pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| AdminArmEndpoint | Kötelező | Null | Rendszergazdai Azure Resource Manager-végpontot. Ez például akkor adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Kötelező | Null | Kiemelt végponthoz. Ez például akkor AzS-ERCS01. |
| CloudAdminCredential | Kötelező | Null | Tartományi fiók hitelesítő adatai Azure Stack-felhő rendszergazdái számára. Ez például akkor Azurestack\CloudAdmin. |
| CertificateFilePath | Kötelező | Null | **Teljes elérési útja** az identitásalkalmazáshoz tanúsítvány PFX-fájlba. |
| CertificatePassword | Kötelező | Null | Olyan jelszót, amely segít megvédeni a tanúsítvány titkos kulcsa. |

## <a name="next-steps"></a>További lépések

[Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)
