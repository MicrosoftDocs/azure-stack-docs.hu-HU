---
title: Üzembe helyezése az Azure Stack – PowerShell |} A Microsoft Docs
description: Ez a cikk a ASDK a parancssorból, PowerShell használatával telepítse.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 05a3d0a88f4852395942d58ac798d2eb06a1d766
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617563"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>A parancssorból a ASDK üzembe helyezése
A ASDK olyan kiértékeléséhez, és mutassa be az Azure Stack-szolgáltatások és szolgáltatások telepítését, tesztelési és fejlesztési környezet. Letöltés működik, és szüksége a környezet hardver előkészítése, és néhány (Ez több óráig fog tartani) parancsfájlok futtatása. Ezt követően bejelentkezhet a rendszergazdai és felhasználói portált az Azure Stack használatának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek 
Készítse elő a development kit gazdaszámítógépen. Tervezze meg a hardveres, szoftveres és hálózati. A fejlesztői készlet (development kit állomás) üzemeltető számítógép hardveres, szoftveres és hálózati követelményeknek kell megfelelnie. Az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) között is ki kell választania. Győződjön meg arról, ahhoz, hogy ezeket az előfeltételeket, hogy a telepítési folyamat problémamentesen fut a telepítés megkezdése előtt. 

Mielőtt telepítené a ASDK, győződjön meg róla a tervezett development kit gazdagép számítógép hardverének, az operációs rendszer, a fiók és a hálózati konfigurációk megfelelnek a ASDK telepítése minimális követelményeinek.

**[Tekintse át az ASDK telepítésével kapcsolatos megfontolások](asdk-deploy-considerations.md)**

> [!TIP]
> Használhatja a [Azure Stack üzembe helyezési követelményeket ellenőrizze az eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) győződjön meg arról, hogy a hardver összes követelményének megfelel-e az operációs rendszer telepítése után.

## <a name="download-and-extract-the-deployment-package"></a>Töltse le és csomagolja ki a központi telepítési csomag
Miután meggyőződött arról, hogy a fejlesztői csomag fogadó számítógép megfelel-e a ASDK telepítésére vonatkozó minimális követelményeinek, a következő lépés az töltse le és csomagolja ki a ASDK központi telepítési csomag. A központi telepítési csomag tartalmazza a Cloudbuilder.vhdx fájlt, amely egy rendszerindításra alkalmas operációs rendszer és az Azure Stack telepítési fájljait tartalmazó virtuális merevlemez.

Letöltheti a központi telepítési csomag development kit gazdagépre vagy egy másik számítógépre. A kibontott üzembe helyezési fájlokat is igénybe vehet 60 GB szabad lemezterület szükséges, ezért egy másik számítógép segítségével csökkentheti a development kit gazdagép hardverkövetelményeinek.

**[Töltse le és csomagolja ki az Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>A fejlesztői csomag fogadó számítógép előkészítése
A ASDK a gazdagépen telepítése előtt elő kell készíteni a környezetet, és a rendszer úgy rendszerindító VHD-ből. Elvégezte a lépést a fejlesztői csomag gazdagép fog indulni, a Cloudbuilder.vhdx (egy virtuális merevlemez, amely tartalmazza a rendszerindításra alkalmas operációs rendszer és az Azure Stack-telepítési fájlok).

A ASDK gazdaszámítógépet, hogy a CloudBuilder.vhdx a PowerShell használatával. Ezek a parancsok a ASDK gazdaszámítógépet, hogy a alá a letöltött és kibontott Azure Stack virtuális merevlemez (CloudBuilder.vhdx) konfigurálása. Ezek a lépések elvégzése után indítsa újra a ASDK gazdaszámítógépen.

A ASDK gazdaszámítógép CloudBuilder.vhdx rendszerindítás konfigurálása:

  1. Nyisson meg egy parancssort rendszergazdaként.
  2. Futtassa a `bcdedit /copy {current} /d "Azure Stack"` parancsot.
  3. Másolás (CTRL + C) a CLSID értéket ad vissza, beleértve a szükséges {}"s. Ez az érték {CLSID} néven, és a hátralévő lépések során (CTRL + V vagy kattintson a jobb gombbal) beillesztésre váró kell.
  4. Futtassa a `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` parancsot. 
  5. Futtassa a `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` parancsot. 
  6. Futtassa a `bcdedit /set {CLSID} detecthal on` parancsot. 
  7. Futtassa a `bcdedit /default {CLSID}` parancsot.
  8. Az indítási beállítások ellenőrzéséhez futtassa `bcdedit`. 
  9. Győződjön meg arról, hogy a fájl a legfelső szintű a C:\ meghajtó (C:\CloudBuilder.vhdx) át lett helyezve, és indítsa újra a development kit gazdaszámítógép CloudBuilder.vhdx. A ASDK számítógép újraindítása után a rendszer azt merevlemez-meghajtóról a CloudBuilder.vhdx virtuális gép ASDK üzembe helyezés megkezdéséhez indítsa. 

> [!IMPORTANT]
> Gondoskodjon arról, hogy közvetlen fizikai gép vagy a fejlesztői csomag gazdagépnek KVM-hozzáférés, az újraindítás előtt. Amikor először indítja el a virtuális gép, felszólítja, hogy a Windows Server-telepítés befejezéséhez. Adja meg a fejlesztői csomag gazdaszámítógép-ba való bejelentkezéshez használt hitelesítő adatokkal rendszergazda. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>A PowerShell-lel development kit gazdagép előkészítése 
A fejlesztői csomag gazdaszámítógép után sikeresen a CloudBuilder.vhdx lemezképpel indul el a rendszer a azonos helyi rendszergazdai hitelesítő adatokkal jelentkezhetnek a development kit gazdaszámítógép-ba való bejelentkezéshez használt (és a Windows Server véglegesítése részeként megadott A telepítő a számítógép indított VHD-ből). 

> [!NOTE]
> Igény szerint beállíthatja úgy is [Azure Stack telemetriai beállítások](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *előtt* a ASDK telepítése.

Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa a parancsokat ebben a szakaszban a ASDK a development kit gazdagépen üzembe helyezéséhez.

> [!IMPORTANT] 
> ASDK telepítési pontosan egy hálózati kártya (NIC) támogatja a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy van engedélyezve (és a többi le van tiltva) az üzembe helyezési parancsfájl futtatása előtt.

Telepítheti az Azure Stack az Azure ad-vel vagy Windows Server AD FS Identitásszolgáltatóként. A mindkettő ugyanúgy működik, az Azure Stack, erőforrás-szolgáltatók és más alkalmazások.

> [!TIP]
> Ha nem ad meg a telepítő paramétereket (lásd a választható paraméterek: InstallAzureStackPOC.ps1 és az alábbi példák), a szükséges paraméterek kéri.

### <a name="deploy-azure-stack-using-azure-ad"></a>Az Azure AD-vel az Azure Stack üzembe helyezése 
Az Azure Stack üzembe helyezése **az Azure AD Identitásszolgáltatóként**, közvetlenül vagy a transzparens proxyn keresztüli internetkapcsolattal kell rendelkeznie. 

Futtassa a következő PowerShell-parancsok az Azure AD-vel fejlesztőkészlet telepítése:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Az Azure AD hitelesítő adatait, meg kell adnia ASDK telepítési néhány percig. Az Azure AD-bérlő globális rendszergazdai hitelesítő adatait kell megadnia. 

Az üzembe helyezést követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen érvényességének visszaállítása a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Az AD FS az Azure Stack üzembe helyezése 
A csomag telepítéséhez **az AD FS használata Identitásszolgáltatóként**, futtassa a következő PowerShell-parancsokat (egyszerűen adja hozzá a - UseADFS paraméter): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Az AD FS-telepítések az alapértelmezett stamp címtárszolgáltatás használja Identitásszolgáltatóként. Jelentkezzen be az alapértelmezett fiók azurestackadmin@azurestack.local, és a jelszót a PowerShell telepítő-parancsok részeként megadott értékre lesz beállítva.

Az üzembe helyezési folyamat néhány órát, amely idő alatt a rendszer automatikusan újraindítás után is igénybe vehet. Az üzembe helyezés sikeres, a PowerShell-konzolon jeleníti meg: **HAJTSA VÉGRE: A művelet "Telepítés"**. Ha a központi telepítés sikertelen, próbálkozzon újra a szkript futtatása a - Újrafuttatás paramétert. Is [ismételt üzembe helyezése ASDK](asdk-redeploy.md) sablon nélkül.

> [!IMPORTANT]
> Ha azt szeretné, a telepítési folyamat állapotának monitorozásához a ASDK gazdagép újraindítása után, jelentkezzen be, AzureStack\AzureStackAdmin. Ha bejelentkezik egy helyi rendszergazdaként után a számítógép újraindítása (és a azurestack.local tartományhoz csatlakoztatott), meg nem jelenik meg az üzembehelyezési folyamatot. Futtassa újra a központi telepítés, ne inkább jelentkezzen be, AzureStack\AzureStackAdmin ugyanazt a jelszót a helyi rendszergazdaként, hogy fut-e a telepítés ellenőrzése.


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD-üzembe helyezési példaszkriptek
A teljes parancsfájlt, az Azure AD központi telepítés. Az alábbiakban néhány megjegyzéssel ellátott példát, beleértve néhány választható paraméter.

Ha az Azure AD identity csak társított **egy** Azure AD-címtárban:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha az Azure AD identity társítva van **nagyobb, mint egy** Azure AD-címtárban:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha a környezet nem rendelkezik DHCP-kompatibilis, majd meg kell adnia a következő további paramétereket a fenti (a megadott példa használati) lehetőségek közül: 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 választható paraméterek:

|Paraméter|Kötelező/választható|Leírás|
|-----|-----|-----|
|AdminPassword|Szükséges|A helyi rendszergazda fiók és minden egyéb felhasználói fiók beállítása development kit központi telepítésének részeként létrehozott összes virtuális gépet. Ezt a jelszót meg kell egyeznie a jelenlegi helyi rendszergazda jelszavát, a gazdagépen.|
|InfraAzureDirectoryTenantName|Szükséges|Beállítja a bérlőcímtárat. Használja ezt a paramétert, egy adott címtár megadására, ahol az AAD-fiók jogosult több címtár kezelésére. Teljes név formátumban egy AAD Directory-bérlő. onmicrosoft.com vagy egy Azure AD ellenőrizte az egyéni tartomány nevét.|
|TimeServer|Szükséges|Ez a paraméter használatával adja meg a megadott idő kiszolgálót. Ez a paraméter egy érvényes idő-kiszolgáló IP-címét meg kell adni. Kiszolgálók nevei nem támogatottak.|
|InfraAzureDirectoryTenantAdminCredential|Választható|Beállítja az Azure Active Directory felhasználónevet és jelszót. Ezeket az Azure a hitelesítő adatokat kell lennie a szervezeti azonosítóval.|
|InfraAzureEnvironment|Választható|Válassza ki a Azure-környezet, amellyel az Azure Stack üzemelő példányához regisztrálni szeretne. Globális Azure, Azure - Kína, Azure - US Government a lehetőségek között.|
|DNSForwarder|Választható|DNS-kiszolgáló létrehozása az Azure Stack üzembe helyezés részeként. Ahhoz, hogy a számítógépek belül a megoldás a stamp kívül nevek feloldása, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ezen a kiszolgálón ismeretlen névfeloldási kérelmeket továbbítja.|
|Ismétlés|Választható|Ez a jelző használatával futtassa újra a központi telepítés. Az összes korábbi bemenet használata esetén. Korábban megadott adatok ismételt megadása nem támogatott, mert több egyedi értékeket jönnek létre és üzembe helyezéshez használt.|


## <a name="perform-post-deployment-configurations"></a>Üzembe helyezés utáni konfigurációk végrehajtása
Miután telepítette a ASDK, nincsenek néhány ajánlott telepítés utáni ellenőrzést és konfigurációs változásokat, mértékét. Ellenőrizheti a telepítés sikerült a test-AzureStack parancsmaggal lett telepítve, és telepítse az Azure Stack PowerShell és a GitHub-eszközöket. 

Győződjön meg arról, hogy a jelszó a development kit gazdagép le nem jár, a kiértékelési időszak vége előtt, a jelszó-elévülési szabályzatának is vissza kell állítania.

> [!NOTE]
> Igény szerint beállíthatja úgy is [Azure Stack telemetriai beállítások](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *után* a ASDK telepítése.

**[Utáni ASDK üzembe helyezési feladatok](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztráljon az Azure-ral
Regisztrálnia kell az Azure Stack az Azure-ral, hogy [töltse le az Azure marketplace-elemek](../operator/azure-stack-create-and-publish-marketplace-item.md) az Azure Stackhez.

**[Regisztráljon az Azure Stack az Azure-ral](asdk-register.md)**

## <a name="next-steps"></a>További lépések
Gratulálunk! Ezek a lépések elvégzése után rendelkezni fog egy development kit következőket tartalmazó környezet [rendszergazda](https://adminportal.local.azurestack.external) és [felhasználói](https://portal.local.azurestack.external) portálokat. 

[ASDK telepítés utáni konfigurációs feladatok](asdk-post-deploy.md)

