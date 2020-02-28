---
title: A ASDK telepítése a parancssorból a PowerShell használatával
description: Megtudhatja, hogyan helyezheti üzembe a ASDK a parancssorból a PowerShell használatával.
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 3c9c4fca443af34422d76bd30af4efe5de685936
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695665"
---
# <a name="deploy-asdk-from-the-command-line-using-powershell"></a>A ASDK telepítése a parancssorból a PowerShell használatával

A Azure Stack Development Kit (ASDK) olyan tesztelési és fejlesztési környezet, amelyet üzembe helyezhet a Azure Stack szolgáltatásainak és szolgáltatásainak kiértékelésére és bemutatására. Az üzembe helyezéshez elő kell készítenie a környezeti hardvert, és néhány parancsfájlt kell futtatnia. A parancsfájlok futtatása több óráig is eltarthat. Ezután bejelentkezhet a rendszergazda és a felhasználói portálra a Azure Stack használatának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

Készítse elő a ASDK-gazdagépet. Tervezze meg a hardvert, a szoftvert és a hálózatot. A ASDK futtató számítógépnek meg kell felelnie a hardver-, szoftver-és hálózati követelményeknek. Válasszon a Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával. A telepítés megkezdése előtt mindenképp kövesse ezeket az előfeltételeket, hogy a telepítési folyamat zökkenőmentesen fusson.

A ASDK üzembe helyezése előtt győződjön meg arról, hogy a tervezett ASDK-gazda számítógép hardvere, operációs rendszere, fiókja és hálózati konfigurációja megfelel a ASDK telepítésének minimális követelményeinek.

**[Tekintse át a ASDK telepítési követelményeit és szempontjait](asdk-deploy-considerations.md)** .

> [!TIP]
> Az operációs rendszer telepítése után a [Azure stack központi telepítési követelmények ellenőrzése eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) segítségével ellenőrizheti, hogy a hardver megfelel-e az összes követelménynek.

## <a name="download-and-extract-the-deployment-package"></a>Töltse le és csomagolja ki a központi telepítési csomagot
Miután meggyőződött arról, hogy a ASDK-gazdagép megfelel a ASDK telepítésének alapvető követelményeinek, a következő lépés a ASDK telepítési csomag letöltése és kibontása. A központi telepítési csomag tartalmazza a Cloudbuilder. vhdx fájlt, amely egy rendszerindító operációs rendszert és a Azure Stack telepítési fájlokat tartalmazó virtuális merevlemez.

A központi telepítési csomagot letöltheti a ASDK gazdagépre vagy egy másik számítógépre. A kibontott telepítési fájlok 60 GB szabad lemezterületet vesznek igénybe, így egy másik számítógép használatával csökkentheti a ASDK gazdagépre vonatkozó hardverkövetelmények mennyiségét.

**[Töltse le és csomagolja ki a Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-asdk-host-computer"></a>A ASDK-gazda számítógép előkészítése
Mielőtt telepítené a ASDK a gazdagépen, elő kell készíteni a környezetet, és a rendszernek a VHD-ről történő rendszerindításra kell konfigurálnia. A lépés elvégzése után a ASDK-gazdagép a Cloudbuilder. vhdx (a rendszerindító operációs rendszert és a Azure Stack telepítési fájlokat tartalmazó virtuális merevlemezre fog indulni).

A PowerShell használatával konfigurálja a ASDK gazdagépet a CloudBuilder. vhdx rendszerindításra. Ezekkel a parancsokkal konfigurálhatja a ASDK gazdagépét a letöltött és kinyert Azure Stack virtuális merevlemezről történő rendszerindításra (CloudBuilder. vhdx). A lépések elvégzése után indítsa újra a ASDK-gazdagépet.

A ASDK gazdagép számítógépének beállítása a CloudBuilder. vhdx rendszerindításra:

  1. Nyisson meg egy parancssort rendszergazdaként.
  2. Futtassa az `bcdedit /copy {current} /d "Azure Stack"` parancsot.
  3. Másolás (CTRL + C) a visszaadott CLSID-érték, beleértve a szükséges kapcsos zárójeleket (`{}`). Ennek az értéknek a neve `{CLSID}`, és a többi lépésben be kell illeszteni (CTRL + V vagy jobb gombbal kattintva).
  4. Futtassa az `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` parancsot.
  5. Futtassa az `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` parancsot.
  6. Futtassa az `bcdedit /set {CLSID} detecthal on` parancsot.
  7. Futtassa az `bcdedit /default {CLSID}` parancsot.
  8. A rendszerindítási beállítások ellenőrzéséhez futtassa a `bcdedit`.
  9. Győződjön meg arról, hogy a CloudBuilder. vhdx fájl át lett helyezve a C:\ gyökerébe Indítsa el a meghajtót (`C:\CloudBuilder.vhdx`), és indítsa újra a ASDK-gazdagépet. A ASDK-gazda számítógép újraindításakor a rendszer a CloudBuilder. vhdx virtuális gép (VM) merevlemezéről indítja el a ASDK telepítésének megkezdéséhez.

> [!IMPORTANT]
> A számítógép újraindítása előtt győződjön meg arról, hogy közvetlen fizikai vagy KVM-hozzáféréssel rendelkezik a ASDK-gazdagéphez. A virtuális gép első elindításakor a rendszer felszólítja a Windows Server telepítésének befejezésére. Adja meg a ASDK-gazdaszámítógépre való bejelentkezéshez használt rendszergazdai hitelesítő adatokat.

### <a name="prepare-the-asdk-host-using-powershell"></a>A ASDK-gazdagép előkészítése a PowerShell használatával 
Miután a ASDK gazdagép sikeresen beindult a CloudBuilder. vhdx lemezképbe, jelentkezzen be ugyanazzal a helyi rendszergazdai hitelesítő adatokkal, amelyeket a ASDK-gazdaszámítógépre való bejelentkezéshez használt. Ezek ugyanazok a hitelesítő adatok, amelyeket a Windows Server telepítésének elvégzése során adott meg, amikor a gazdaszámítógép a VHD-ről elindult.

> [!NOTE]
> Szükség esetén [Azure stack telemetria beállításait](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) is megadhatja a ASDK telepítése *előtt* .

Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa az ebben a szakaszban található parancsokat a ASDK a ASDK-gazdagépen való telepítéséhez.

> [!IMPORTANT]
> A ASDK telepítése pontosan egy hálózati adaptert (NIC) támogat a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy van engedélyezve (és minden más le van tiltva) a telepítési parancsfájl futtatása előtt.

Az Azure Stack az Azure AD-vel vagy a Windows Server AD FS identitás-szolgáltatóként is üzembe helyezhető. A Azure Stack, az erőforrás-szolgáltatók és más alkalmazások ugyanúgy működnek, mint a kettő.

> [!TIP]
> Ha nem ad meg telepítési paramétereket (lásd a InstallAzureStackPOC. ps1 választható paramétereket és példákat alább), a rendszer kéri a szükséges paramétereket.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure Stack üzembe helyezése az Azure AD-vel 
Azure Stack az Azure AD-vel való üzembe helyezéséhez **használja az identitás-szolgáltatót**, vagy közvetlenül, vagy egy transzparens proxyn keresztül kell kapcsolódnia. 

Futtassa a következő PowerShell-parancsokat a ASDK üzembe helyezéséhez az Azure AD használatával:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

A ASDK telepítése néhány percet vesz igénybe az Azure AD-beli hitelesítő adatok megadásához. Adja meg az Azure AD-bérlő globális rendszergazdai hitelesítő adatait.

Az üzembe helyezést követően Azure Active Directory globális rendszergazdai jogosultság nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra. Ilyen művelet például egy erőforrás-szolgáltatót telepítő parancsfájl vagy egy olyan új szolgáltatás, amely engedély megadását igényli. Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés*tulajdonosa.

### <a name="deploy-azure-stack-using-ad-fs"></a>Azure Stack üzembe helyezése AD FS használatával 
Ha a ASDK a **AD FS használatával**szeretné központilag telepíteni az identitás-szolgáltatóként, futtassa a következő PowerShell-parancsokat (csak hozzá kell adnia a-UseADFS paramétert):

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

AD FS üzemelő példányok esetében az alapértelmezett Stamp Directory szolgáltatást használja az identitás-szolgáltatóként. A bejelentkezéshez használt alapértelmezett fiók azurestackadmin@azurestack.local, és a jelszó a PowerShell telepítési parancsainak részeként megadott értékre van állítva.

Az üzembe helyezési folyamat néhány órát is igénybe vehet, amíg a rendszer egyszer automatikusan újraindul. Ha az üzembe helyezés sikeres, a PowerShell-konzol a következőt jeleníti meg: **Befejezés: művelet "központi telepítés"** . Ha az üzembe helyezés sikertelen, próbálja meg újból futtatni a parancsfájlt az-újra paraméter használatával. Vagy újból üzembe helyezheti a [ASDK](asdk-redeploy.md) a semmiből.

> [!IMPORTANT]
> Ha figyelni szeretné a központi telepítés előrehaladását a ASDK-állomás újraindítása után, be kell jelentkeznie AzureStack\AzureStackAdmin. Ha a gazdaszámítógép újraindítása után helyi rendszergazdaként jelentkezik be (és a azurestack. local tartományhoz csatlakozik), a telepítési folyamat nem fog megjelenni. Ne futtassa újra az üzembe helyezést, hanem jelentkezzen be a AzureStack\AzureStackAdmin ugyanazzal a jelszóval, mint a helyi rendszergazda, és ellenőrizze, hogy fut-e a telepítés.


#### <a name="azure-ad-deployment-script-examples"></a>Példák az Azure AD telepítési parancsfájlokra
A teljes Azure AD-telepítést parancsfájlként is elvégezheti. Íme néhány Megjegyzés, amely tartalmaz néhány választható paramétert.

Ha az Azure AD-identitás csak **egy** Azure ad-címtárhoz van társítva:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha az Azure AD-identitása egynél **több Azure ad** -címtárhoz van társítva:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha a környezet nem rendelkezik engedélyezve DHCP-vel, akkor a fenti lehetőségek egyikére vonatkozóan a következő további paramétereket kell tartalmaznia (példaként megadott használat): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC. ps1 választható paraméterek

|Paraméter|Kötelező/nem kötelező|Leírás|
|-----|-----|-----|
|AdminPassword|Kötelező|Beállítja a helyi rendszergazdai fiókot és az összes többi felhasználói fiókot a ASDK-telepítés részeként létrehozott összes virtuális gépen. Ennek a jelszónak meg kell egyeznie a gazdagép aktuális helyi rendszergazdai jelszavával.|
|InfraAzureDirectoryTenantName|Kötelező|Beállítja a bérlői könyvtárat. Ezzel a paraméterrel megadhat egy adott könyvtárat, amelyben az Azure AD-fiók rendelkezik a több könyvtár felügyeletéhez szükséges engedélyekkel. Egy Azure AD-bérlő teljes neve. onmicrosoft.com vagy egy Azure AD által ellenőrzött egyéni tartománynév.|
|TimeServer|Kötelező|Ezzel a paraméterrel adható meg egy adott időkiszolgáló. Ezt a paramétert érvényes Time Server IP-címnek kell megadni. A kiszolgálók nevei nem támogatottak.|
|InfraAzureDirectoryTenantAdminCredential|Optional|Beállítja a Azure Active Directory felhasználónevét és jelszavát. Ezeknek az Azure-beli hitelesítő adatoknak szervezeti AZONOSÍTÓnak kell lenniük.|
|InfraAzureEnvironment|Optional|Válassza ki azt az Azure-környezetet, amellyel regisztrálni szeretné ezt az Azure Stack-telepítést. A lehetőségek közé tartozik a globális Azure, az Azure-Kína, az Azure-USA kormánya.|
|DNSForwarder|Optional|A rendszer a Azure Stack központi telepítésének részeként létrehoz egy DNS-kiszolgálót. Annak engedélyezéséhez, hogy a megoldásban lévő számítógépek a bélyegzőn kívül is feloldják a neveket, adja meg a meglévő infrastruktúra DNS-kiszolgálóját. A in-Stamp DNS-kiszolgáló ismeretlen névfeloldási kérelmeket továbbít erre a kiszolgálóra.|
|Futtassa újra|Optional|Használja ezt a jelzőt a központi telepítés újrafuttatásához. Minden korábbi bemenet használatban van. A korábban megadott adatok újbóli megadása nem támogatott, mert több egyedi érték jön létre és használatos az üzembe helyezéshez.|


## <a name="perform-post-deployment-configurations"></a>Üzembe helyezés utáni konfigurációk végrehajtása
A ASDK telepítése után néhány javasolt telepítés utáni ellenőrzés és konfigurációs módosítás szükséges. Ellenőrizze, hogy a telepítést sikeresen telepítette-e a test-AzureStack parancsmaggal, majd telepítse Azure Stack PowerShell-és GitHub-eszközöket.

Javasoljuk, hogy állítsa alaphelyzetbe a jelszó lejárati házirendjét, hogy a próbaidőszak lejárta előtt ne járjon le a ASDK-gazdagép jelszava.

> [!NOTE]
> Szükség esetén [Azure stack telemetria beállításait](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) is konfigurálhatja a ASDK telepítése *után* .

**[ASDK-telepítési feladatok közzététele](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztrálás az Azure-ban
Regisztrálnia kell Azure Stack az Azure-ban, hogy [le tudja tölteni az Azure Marketplace-elemeket](../operator/azure-stack-create-and-publish-marketplace-item.md) a Azure Stackba.

**[Azure Stack regisztrálása az Azure-ban](asdk-register.md)**

## <a name="next-steps"></a>Következő lépések
Gratulálunk! A lépések elvégzése után egy ASDK-környezettel fog rendelkezni, amely [rendszergazdai](https://adminportal.local.azurestack.external) és [felhasználói](https://portal.local.azurestack.external) portálokkal is rendelkezik. 

[ASDK-telepítési konfigurációs feladatok közzététele](asdk-post-deploy.md)

