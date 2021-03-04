---
title: Active Directory egyszeri bejelentkezés használata az AK-ban Azure Stack HCI-hez
description: Active Directory hitelesítés használata az API-kiszolgáló és az SSO hitelesítő adatai közötti biztonságos kapcsolódáshoz
author: v-susbo
ms.topic: how-to
ms.date: 02/12/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: fd4b8982ecaa9de1b3b63dd97cb460772e25971d
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873820"
---
# <a name="use-active-directory-single-sign-in-credentials-for-aks-on-azure-stack-hci"></a>Active Directory egyszeri bejelentkezési hitelesítő adatok használata az AK-hoz Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Active Directory hitelesítés nélkül a felhasználóknak tanúsítványalapú _kubeconfig_ -fájlra kell támaszkodnia az API-kiszolgálóhoz való csatlakozáskor a `kubectl` parancs használatával. A _kubeconfig_ -fájl olyan titkos kulcsokat tartalmaz, mint például a titkos kulcsok és a gondosan terjeszthető tanúsítványok. Ez jelentős biztonsági kockázat lehet.

A tanúsítványalapú _kubeconfig_ használata alternatívájaként a Active Directory (ad) egyszeri bejelentkezés (SSO) hitelesítő adatait biztonságos módon is használhatja az API-kiszolgálóhoz való kapcsolódáshoz. Az Azure Stack HCI-n futó Azure Kubernetes szolgáltatással való AD-integráció lehetővé teszi, hogy a Windows-tartományhoz csatlakozó számítógépeken a felhasználó SSO hitelesítő adataival csatlakozzon az API-kiszolgálóhoz (a használatával `kubectl` ). Ezzel megszűnik a titkos kulcsokat tartalmazó tanúsítványalapú _kubeconfig_ -fájlok felügyeletének és terjesztésének szükségessége.

Az AD-integráció az AD _kubeconfig_-t használja, amely különbözik a tanúsítványalapú _kubeconfig_ -fájltól, és nem tartalmaz titkos kulcsot. A tanúsítványalapú _kubeconfig_ -fájl biztonsági mentési célokra használható, például a hibaelhárításhoz, ha a Active Directory hitelesítő adatokkal való kapcsolódással kapcsolatos problémák merülnek fel.

Az AD-integráció egy másik biztonsági előnye, hogy a felhasználók és a csoportok [biztonsági azonosítóként (SID)](https://docs.microsoft.com/troubleshoot/windows-server/identity/security-identifiers-in-windows)vannak tárolva. A csoportok neveivel ellentétben a SID-ek nem változtathatók meg és nem egyediek, ezért nem találhatók meg az elnevezési ütközések.

> [!Note] 
> Ebben az előzetes kiadásban csak a munkaterhelés-fürtökhöz biztosítunk AD egyszeri bejelentkezéses kapcsolatot. 

Ez a dokumentum végigvezeti a következő lépéseken, amelyekkel beállíthatja a Active Directoryt az identitás-szolgáltatóként, és engedélyezheti az egyszeri bejelentkezést az alábbiakon keresztül `kubectl` :

- Hozza létre az AD-fiókot az API-kiszolgálóhoz, majd hozza létre a fiókhoz társított [keytab kiterjesztésű](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) -fájlt. Lásd: [AD-hitelesítés létrehozása a keytab kiterjesztésű fájllal](#create-ad-auth-using-the-keytab-file) az ad-fiók létrehozásához és a keytab kiterjesztésű fájl létrehozásához.
- A [keytab kiterjesztésű](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) fájl segítségével telepítse az ad autht a Kubernetes-fürtön. Ennek a lépésnek a részeként automatikusan létrejön egy alapértelmezett szerepköralapú hozzáférés-vezérlési (RBAC) konfiguráció.
- A csoportok nevének konvertálása a SID-re és fordítva a RBAC-konfigurációk létrehozásakor vagy szerkesztésekor: [az ad-csoport szerepkör-kötésének létrehozása és frissítése](#create-and-update-the-ad-group-role-binding) az utasításokhoz.
 
## <a name="before-you-begin"></a>Előkészületek

A Active Directory SSO hitelesítő adatok konfigurálásának megkezdése előtt győződjön meg arról, hogy a következő elemek érhetők el:

 - A legújabb **AK-HCI PowerShell-** modul telepítve van. Ha nem, olvassa el [a AksHci PowerShell-modul letöltése és telepítése](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)című témakört. 
 - Az AK-gazdagép telepítve és konfigurálva van. Ha nem, kövesse az üzemelő [példány konfigurálása](./setup-powershell.md#step-4-configure-your-deployment)című témakört.  
 - Győződjön meg arról, hogy a keytab kiterjesztésű-fájl használható. Ha nem, tekintse meg [az API-kiszolgáló ad-fiókjának és a keytab kiterjesztésű fájljának létrehozását](#create-the-api-server-ad-account-and-the-keytab-file)ismertető témakört. 
 - A rendszer létrehoz egy adott egyszerű szolgáltatásnév (SPN) keytab kiterjesztésű-fájlját, és ennek az SPN-nek meg kell egyeznie a munkaterhelés-fürthöz tartozó API Server AD-fiókkal. Győződjön meg arról is, hogy ugyanazt az egyszerű szolgáltatásnevet használja az AD-hitelesítési folyamat során. A keytab kiterjesztésű-fájlnak a _current. keytab kiterjesztésű_ nevűnek kell lennie.
 - Azure Stack HCI munkaterhelési fürtben található minden egyes AK esetében ellenőrizze, hogy van-e elérhető egy API Server AD-fiók.
 - Az ügyfélszámítógépnek Windows-tartományhoz csatlakoztatott számítógépnek kell lennie.

## <a name="create-ad-auth-using-the-keytab-file"></a>AD-hitelesítés létrehozása a keytab kiterjesztésű fájl használatával

### <a name="step-1-create-the-workload-cluster-and-enable-ad-authentication"></a>1. lépés: a munkaterhelés-fürt létrehozása és az AD-hitelesítés engedélyezése

Az AD-hitelesítés telepítése előtt létre kell hoznia egy AK-t Azure Stack HCI munkaterhelés-fürtön, és engedélyeznie kell az AD-hitelesítés bővítményt a fürtön. Ha nem engedélyezi az AD-hitelesítést az új fürt létrehozásakor, később nem fogja tudni engedélyezni.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot a parancs **-enableADAuth** paraméter használatával `New-AksHciCluster` :

```powershell
New-AksHciCluster -name mynewcluster1 -enableADAuth
```

Minden munkaterhelés-fürthöz ellenőrizze, hogy van-e elérhető egy API-kiszolgáló AD-fiók.

A munkaterhelés-fürt létrehozásával kapcsolatos részletekért lásd: [Kubernetes-fürtök létrehozása a Windows PowerShell használatával](./create-kubernetes-cluster-powershell.md).

### <a name="step-2-install-ad-authentication"></a>2. lépés: az AD-hitelesítés telepítése

Az AD-hitelesítés telepítése előtt ellenőrizze, hogy telepítve van-e a munkaterhelés-fürt, és hogy engedélyezve van-e az AD-hitelesítés a fürtön. Az AD-hitelesítés telepítéséhez használja az alábbi lehetőségek egyikét.

#### <a name="option-1"></a>1\. lehetőség

Tartományhoz csatlakoztatott Azure Stack HCI-fürthöz nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8s/apiserver@CONTOSO.COM -adminUser contoso\bob
```  

#### <a name="option-2"></a>2\. lehetőség

Ha a fürt gazdagépe nincs tartományhoz csatlakoztatva, az alábbi példában látható módon használja a rendszergazda felhasználónevet vagy a csoport nevét SID formátumban:
 
```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8
```  

A felhasználói fiók biztonsági azonosítójának megkereséséhez tekintse [meg a felhasználó vagy csoport biztonsági azonosítójának meghatározása](#determine-the-user-or-group-security-identifier)című témakört. 

Mielőtt továbblép a következő lépésekre, jegyezze fel a következő elemeket:

- Győződjön meg arról, hogy a keytab kiterjesztésű fájl neve _current. keytab kiterjesztésű_.
- Cserélje le a környezetének megfelelő SPN-t.
- a **-adminUser** egy megfelelő szerepkör-kötést hoz létre a megadott ad-csoport számára a fürt rendszergazdai jogosultságokkal. Cserélje le a _contoso\bob_ az adott környezetnek megfelelő ad-csoportra vagy-felhasználóra.

### <a name="step-3-test-the-ad-webhook-and-keytab-file"></a>3. lépés: az AD webhook és a keytab kiterjesztésű fájl tesztelése

Győződjön meg arról, hogy az AD webhook fut az API-kiszolgálón, és a keytab kiterjesztésű-fájl Kubernetes-titokként van tárolva. A következő lépések végrehajtásával kérheti le a számítási feladatok fürtje tanúsítvány alapú _kubeconfig_ -fájlját:

1. Tanúsítvány alapú _kubeconfig_ -fájl beszerzése. A _kubeconfig_ -fájl segítségével helyi gazdagépként csatlakozhat a fürthöz a következő paranccsal:

   ```powershell
   Get-AksHciCredential -name mynewcluster1
   ```  

2. Futtassa `kubectl` a (z) szolgáltatást azon a kiszolgálón, amelyhez kapcsolódott (a korábban létrehozott tanúsítványalapú _kubeconfig_ -fájllal), majd ellenőrizze az Active Directory webhook üzembe helyezését, és győződjön meg róla, hogy az _ad-Auth-webhook-XXXX_ formátumban van.  

    ```bash
    kubectl get pods -n=kube-system
    ```

3. Futtassa a parancsot, `kubectl` és győződjön meg arról, hogy a keytab kiterjesztésű-fájl titkosként van telepítve, és Kubernetes-titokként van felsorolva: 

   ```bash
   kubectl get secrets -n=kube-system
   ```

### <a name="step-4-create-the-ad-kubeconfig-file"></a>4. lépés: az AD kubeconfig-fájl létrehozása

Az Active Directory webhook és a keytab kiterjesztésű sikeres üzembe helyezése után hozza létre az AD _kubeconfig_ -fájlt.
A fájl létrehozása után másolja az AD _kubeconfig_ -fájlt az ügyfélszámítógépre, és használja az API-kiszolgáló hitelesítéséhez. A tanúsítványalapú _kubeconfig_ -fájltól eltérően az ad _kubeconfig_ nem titkos, és az egyszerű szövegként való másolás biztonságos.

Nyissa meg a PowerShellt rendszergazdaként, majd futtassa a következő parancsot:  

   ```powershell
   Get-AksHciCredential -name mynewcluster1 -outputLocation .\AdKubeconfig -adAuth
   ```

### <a name="step-5-copy-kubeconfig-and-other-files-to-the-client-machine"></a>5. lépés: a kubeconfig és más fájlok másolása az ügyfélszámítógépre

Másolja az alább felsorolt három fájlt a Azure Stack HCI-fürtről az ügyfélszámítógépre:

- Másolja az előző lépésben létrehozott AD _kubeconfig_ -fájlt $env: felhasználói profil \. kube\config.

- Hozza létre a mappa elérési útját `c:\adsso` , és másolja a következő fájlokat az Azure stack HCI-fürtről az ügyfélszámítógépre.
  - Kubectl.exe `$env:ProgramFiles\AksHci` a c:\adsso
  - Kubectl-adsso.exe `$env:ProgramFiles\AksHci` a c:\adsso

### <a name="step-6-connect-to-the-api-server-from-the-client-machine"></a>6. lépés: Kapcsolódás az API-kiszolgálóhoz az ügyfélgépről

Az előző lépések elvégzése után jelentkezzen be a Windows-tartományhoz csatlakoztatott ügyfélgépre az egyszeri bejelentkezéshez használt hitelesítő adataival. Nyissa meg a PowerShellt, majd próbálja meg elérni az API-kiszolgálót a használatával `kubectl` . Ha a rendszer hitelesítést kér, akkor sikeresen beállította az AD SSO-t.

## <a name="create-and-update-the-ad-group-role-binding"></a>Az AD-csoport szerepkör-kötésének létrehozása és frissítése

Ahogy azt a 2. lépésben már említettük, a rendszer létrehoz egy alapértelmezett szerepkör-kötést a fürt rendszergazdai jogosultságokkal a felhasználó és/vagy a telepítés során megadott csoport számára. A Kubernetes szerepkör-kötése határozza meg az AD-csoportok hozzáférési szabályzatait. Ez a lépés azt ismerteti, hogyan lehet a RBAC használatával új AD-csoportbeli szerepkör-kötéseket létrehozni a Kubernetes-ben, és szerkesztheti a meglévő szerepkör-kötéseket. Előfordulhat például, hogy a fürt rendszergazdája további jogosultságokat szeretne adni a felhasználóknak az AD-csoportok használatával (ami hatékonyabbá teszi a folyamatot). További információ a RBAC: a [RBAC-hitelesítés használata](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

További AD Group RBAC-bejegyzések létrehozásakor vagy szerkesztésekor a tulajdonos nevét "" előtaggal kell ellátni `microsoft:activedirectory:CONTOSO\\group name` . Vegye figyelembe, hogy a névnek tartalmaznia kell egy tartománynevet és egy idézőjelek közé foglalt előtagot.

Bemutatunk két példát:

**1\. példa**

```bash
apiVersion: rbac.authorization.k8s.io/v1 
 kind: ClusterRoleBinding 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: ClusterRole 
   name: cluster-admin 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\Bob" 
```

**2\. példa**

Az alábbi példa bemutatja, hogyan hozhat létre egyéni szerepkört és szerepkör-kötést egy AD-csoporttal rendelkező [névtérhez](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) . A példában a "SREGroup" egy már létező csoport a contoso Active Directoryban. A felhasználók az AD-csoportba való felvételekor azonnal kapnak jogosultságokat.

```bash
kind: Role 
 apiVersion: rbac.authorization.k8s.io/v1 
 metadata: 
   name: sre-user-full-access 
   namespace: sre 
 rules: 
 - apiGroups: ["", "extensions", "apps"] 
   resources: ["*"] 
   verbs: ["*"] 
 - apiGroups: ["batch"] 
   resources: 
   - jobs 
   - cronjobs 
   verbs: ["*"] 
 apiVersion: rbac.authorization.k8s.io/v1 
 kind: RoleBinding 
 namespace: sre 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: Role 
   name: sre-user-full-access 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\SREGroup" 
```

A YAML-fájl alkalmazása előtt a **csoport-és felhasználóneveket** mindig biztonsági azonosítóra kell konvertálni a következő paranccsal:

```bash
kubectl-adsso nametosid <rbac.yml>
```  

Hasonlóképpen, ha egy meglévő RBAC szeretne frissíteni, a módosítások végrehajtása előtt konvertálhatja a biztonsági azonosítót egy felhasználóbarát csoport nevére. A SID átalakításához használja a következő parancsot: 

```bash
kubectl-adsso sidtoname <rbac.yml>
```

## <a name="change-the-ad-account-password-associated-with-the-api-server-account"></a>Az API-kiszolgáló fiókjához társított AD-fiók jelszavának módosítása

Az API-kiszolgáló fiók jelszavának módosításakor el kell távolítania az AD-hitelesítési bővítményt, és újra kell telepítenie a frissített aktuális és előző fülek használatával. 

Minden alkalommal, amikor módosítja a jelszót, át kell neveznie az aktuális keytab kiterjesztésű (_current. keytab kiterjesztésű_) az _előző. keytab kiterjesztésű_ értékre, majd győződjön meg róla, hogy az új jelszót _aktuális. keytab kiterjesztésű_ néven adja meg.

> [!Important] 
> A fájloknak a _current. keytab kiterjesztésű_ és az _előző. keytab kiterjesztésű_ névvel kell rendelkezniük. Ez a meglévő szerepkör-kötéseket nem érinti.

### <a name="uninstall-and-reinstall-ad-authentication"></a>AD-hitelesítés eltávolítása és újratelepítése

Előfordulhat, hogy újra kell telepítenie az AD SSO-t az API-kiszolgáló fiókjának módosításakor, a jelszó frissítésekor vagy a hiba hibaelhárítása érdekében.

Az AD-hitelesítés eltávolításához nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot:  

```powershell
Uninstall-AksHciAdAuth -name mynewcluster1
```

Az AD-hitelesítés újratelepítéséhez nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

> [!Note] 
> A `-previousKeytab` paraméter csak akkor szükséges, ha megváltoztatja a jelszót. Ezzel elkerülhető az állásidő, ha az ügyfelek gyorsítótárazott jegyekkel rendelkeznek.

## <a name="create-the-api-server-ad-account-and-the-keytab-file"></a>Az API-kiszolgáló AD-fiókjának és a keytab kiterjesztésű fájljának létrehozása

A folyamat két lépést vesz fel. Először hozzon létre egy új AD-fiókot/-felhasználót az API-kiszolgálóhoz az egyszerű szolgáltatásnév (SPN) és a második, keytab kiterjesztésű-fájl létrehozása az AD-fiókhoz.

### <a name="step-1-create-a-new-ad-account-or-user-for-the-api-server"></a>1. lépés: új AD-fiók vagy-felhasználó létrehozása az API-kiszolgálóhoz

A [New-ADUser PowerShell-](https://docs.microsoft.com/powershell/module/addsadministration/new-aduser?view=win10-ps&preserve-view=true) paranccsal hozzon létre egy új ad-fiókot vagy-felhasználót az egyszerű szolgáltatásnév használatával. Bemutatunk egy példát: 

```powershell 
New-ADUser -Name apiserver -ServicePrincipalNames k8s/apiserver -AccountPassword (ConvertTo-SecureString "password" -AsPlainText -Force) -KerberosEncryptionType AES128 -Enabled 1
```

### <a name="step-2-create-the-keytab-file-for-the-ad-account"></a>2. lépés: az AD-fiókhoz tartozó keytab kiterjesztésű-fájl létrehozása

A keytab kiterjesztésű-fájl létrehozásához használja a [ktpass](https://docs.microsoft.com/windows-server/administration/windows-commands/ktpass) Windows parancsot. 

Íme egy példa a ktpass használatával:

```bash
ktpass /out current.keytab /princ k8s/apiserver@BCONTOSO.COM /mapuser contoso\apiserver_acct /crypto all /pass p@$$w0rd /ptype KRB5_NT_PRINCIPAL
```

> [!NOTE]
> Ha ezt a hibát látja, a **DsCrackNames 0x2 adott vissza**, győződjön meg arról, hogy a paraméter a (z) `/mapuser` olyan formában van, `mapuser DOMAIN\user` ahol a tartomány az ECHO kimenete `%userdomain%` .

  
## <a name="determine-the-user-or-group-security-identifier"></a>A felhasználó vagy csoport biztonsági azonosítójának meghatározása

A következő két lehetőség egyikével megkeresheti a fiókja vagy más fiókok biztonsági azonosítóját.

A fiókhoz társított biztonsági azonosító megkereséséhez írja be a következőt a kezdőkönyvtár parancssorából:

```bash
whoami /user
``` 

Egy másik fiókhoz tartozó SID megkereséséhez nyissa meg a PowerShellt rendszergazdaként, és futtassa a következőt:

```powershell
(New-Object System.Security.Principal.NTAccount(<CONTOSO\Bob>)).Translate([System.Security.Principal.SecurityIdentifier]).value
```

## <a name="next-steps"></a>Következő lépések 

Ebben a útmutatóban megtanulta, hogyan konfigurálhatja az AD-hitelesítést úgy, hogy biztonságosan kapcsolódjon az API-kiszolgálóhoz SSO hitelesítő adatokkal. Következő lépésként a következőket teheti:

- [Linux-alkalmazások üzembe helyezése Kubernetes-fürtön](./deploy-linux-application.md).
- [Windows Server-alkalmazás üzembe helyezése Kubernetes-fürtön](./deploy-windows-application.md).
