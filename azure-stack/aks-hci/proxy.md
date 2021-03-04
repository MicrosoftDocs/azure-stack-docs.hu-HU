---
title: Proxybeállítások konfigurálása az AK-hoz Azure Stack HCI-ben
description: Megtudhatja, hogyan tervezhet és konfigurálhat proxy-támogatást az internethez való csatlakozáshoz.
author: abha
ms.topic: how-to
ms.date: 01/27/2021
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 83a3a4928f5df1b6b4520cdc6ffd277e497425ff
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873971"
---
# <a name="configure-proxy-settings-on-aks-on-azure-stack-hci"></a>Proxybeállítások konfigurálása az AK-on Azure Stack HCI-on

Ha a hálózat egy proxykiszolgáló használatát igényli az internethez való csatlakozáshoz, ez a dokumentum végigvezeti Önt a AksHci és WinInetProxy PowerShell-modul használatával a proxy Azure Stack támogatásának beállításához szükséges lépéseken. 

Ha nem kívánja használni a WinInetProxy-t, használhatja az **Internet Properties** (inetcpl.cpl) szolgáltatást, majd a proxykiszolgáló beállításainak konfigurálásához és veriy kattintson a **kapcsolatok** fülre és a **helyi hálózati beállítások** lehetőségre.


## <a name="before-you-begin"></a>Előkészületek

Telepítse a [WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0) PowerShell-modult a proxykiszolgáló konfigurálásához a következő parancs futtatásával egy PowerShell felügyeleti ablakban:

```Powershell
Install-Module -Name WinInetProxy -Repository PSGallery
```

Egyéb előfeltételekhez látogasson el a [rendszerkövetelményekre](./system-requirements.md). A AksHci PowerShell-modul letöltéséhez látogasson el a [AksHci PowerShell-modul beállítására](./setup-powershell.md).

## <a name="configure-a-proxy-server-without-authentication"></a>Proxykiszolgáló konfigurálása hitelesítés nélkül

Ha olyan proxykiszolgálót szeretne konfigurálni, amely nem igényel hitelesítést, futtassa az alábbi parancsot minden Azure Stack HCI fürtcsomóponton:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
```

## <a name="configure-a-proxy-server-with-authentication"></a>Proxykiszolgáló konfigurálása hitelesítéssel

A hitelesítést igénylő proxykiszolgáló konfigurálásához konfigurálnia kell a (z) Azure Stack gazdagéphez tartozó hitelesítő adatokat a WinINet-proxyval való használathoz. Ez attól függően változhat, hogy milyen típusú hitelesítést igényel a proxy, és lehet NTLM/Kerberos vagy egyszerű hitelesítés.

> [!NOTE]
> Ha tanúsítványt szeretne használni a proxykiszolgálóhoz való csatlakozáshoz, akkor a tanúsítványt a gazdagépek megfelelő tanúsítványtárolójában kell kiépíteni, hogy az megbízható legyen.

### <a name="configure-a-proxy-server-with-ntlmkerberos-authentication"></a>Proxykiszolgáló konfigurálása NTLM/Kerberos-hitelesítéssel

Adja hozzá a Windows új hitelesítő adatait az alábbi parancs használatával. Ha a rendszer kéri, adja meg a biztonságos jelszót. Azt is megteheti, hogy a Windows hitelesítőadat-kezelőt a Windows hitelesítő **adatok** területen a Windows rendszerbeli hitelesítő adatok új bejegyzésének hozzáadására is használhatja. 

```powershell
cmdkey /generic:proxy.contoso.com /user:username /pass
```
A parancs futtatásakor a rendszer kéri, hogy adja meg a jelszót.

A PowerShell-profilban adja hozzá a következő parancsot az AK-HCI-ügynököknek a gyorsítótárazott hitelesítő adatok használatára való engedélyezéséhez:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $PROFILE
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials
```  

### <a name="configure-a-proxy-server-with-basic-authentication"></a>Proxykiszolgáló konfigurálása egyszerű hitelesítéssel

Az alapszintű hitelesítő adatokat hozzá kell adni a PowerShell-profilhoz, hogy a letöltési ügynök modul használhassa őket. 

> [!NOTE]
> A PowerShell-profilban szereplő hitelesítő adatok nincsenek titkosítva, és nem jelennek meg tiszta szövegben. Győződjön meg arról, hogy a PowerShell-profilt hozzáférés-vezérlési listák (ACL-ek) védik, így csak a rendszergazdák és a LocalSystem fiók tekinthetik meg őket.

Szerkessze a PowerShell-profilt úgy, hogy a felhasználónevet és a jelszót az alapszintű hitelesítési hitelesítő adatokkal cserélje le a lent látható módon:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $profile
[System.Net.WebRequest]::DefaultWebProxy.Credentials = new-object System.Net.NetworkCredential("username", "password")
```

## <a name="deploy-aks-on-azure-stack-hci-host-using-a-proxy-server"></a>AK üzembe helyezése Azure Stack HCI-gazdagépen proxykiszolgáló használatával

Miután konfigurálta a proxykiszolgálót, beállíthatja a proxy konfigurációját egy AK-gazdagép telepítéséhez a `Set-AksHciConfig` parancs használatával. Különböző lépések tartoznak attól függően, hogy a proxykiszolgáló hitelesítést igényel-e.

Miután konfigurálta az üzemelő példányt az alább felsorolt beállításokkal, [telepíthet egy AK-gazdagépet Azure stack HCI](./setup-powershell.md) -re, és [létrehozhat AK-fürtöket a PowerShell használatával](./create-kubernetes-cluster-powershell.md).

### <a name="configure-an-aks-host-for-a-proxy-server-with-basic-authentication"></a>AK-gazdagép konfigurálása egyszerű hitelesítéssel rendelkező proxykiszolgáló számára  

Ha a proxykiszolgáló hitelesítést igényel, futtassa a következő parancsokat a hitelesítő adatok beszerzéséhez, majd állítsa be a konfiguráció részleteit.

```powershell
$proxyCred = Get-Credential
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCredential $ProxyCred
```

## <a name="configure-an-aks-host-for-a-proxy-server-with-ntlmkerberos-authentication"></a>Az AK-gazdagépek konfigurálása NTLM-vagy Kerberos-hitelesítéssel rendelkező proxykiszolgáló számára

```powershell
$credential = Get-Credential # get the credential for the proxy server
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerCredential $credential
```

### <a name="configure-an-aks-host-for-a-proxy-server-without-authentication"></a>AK-gazdagép konfigurálása hitelesítés nélkül  

Ha a proxykiszolgáló nem igényel hitelesítést, nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888"
```

### <a name="configure-an-aks-host-for-a-proxy-server-with-a-trusted-certificate"></a>AK-gazdagép konfigurálása megbízható tanúsítvánnyal rendelkező proxykiszolgáló számára

Ha a proxykiszolgáló megköveteli, hogy a proxykiszolgáló megbízhatónak minősítse a tanúsítványokat, adja meg a fájl futtatását `Set-AksHciConfig` . A tanúsítványfájl formátuma a *Base-64 kódolású X. 509*. Ezáltal lehetővé válik a tanúsítvány kiépítése és megbízhatósága a veremben:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCertFile "C:\proxycertificate.crt"
```

> [!NOTE]
> A proxy-tanúsítványok még nincsenek kiépítve/megbízhatók a Windows Kubernetes Worker csomópontjain. A Windows-feldolgozók támogatása egy későbbi kiadásban lesz engedélyezve.


## <a name="exclude-specific-hosts-or-domains-from-using-the-proxy-server"></a>Adott gazdagépek vagy tartományok kizárása a proxykiszolgáló használatával

A legtöbb hálózatban ki kell zárnia bizonyos hálózatokat, állomásokat vagy tartományokat a proxykiszolgálón keresztül való hozzáféréshez. Ezt úgy teheti meg, hogy a (z) paraméterének használatával kizárja a címek karakterláncait `-proxyServerNoProxy` `Set-AksHciConfig` .

Az alapértelmezett értéke a `proxyServerNoProxy` következő: `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`

A parancs futtatásakor a rendszer kizárja a következőket:

- A localhost-forgalom (localhost, 127.0.0.1)
- Belső Kubernetes-szolgáltatási forgalom (. SVC), ahol az _. SVC_ helyettesítő nevet jelöl. Ez a következőhöz hasonló:*. SVC, de nem* használja ebben a sémában.
- A magánhálózati címtartomány (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). Vegye figyelembe, hogy a magánhálózati címtartomány fontos hálózatokat tartalmaz, például a Kubernetes Service CIDR (10.96.0.0/12) és a Kubernetes POD CIDR (10.244.0.0/16).

Habár ezek az alapértelmezett értékek sok hálózat esetében is működni fognak, előfordulhat, hogy további alhálózati tartományokat és/vagy neveket kell hozzáadnia a kivételek listájához. Előfordulhat például, hogy fel szeretné venni a vállalati névteret (. contoso.com) a proxyn keresztül történő átirányításra. Ezt úgy érheti el, ha megadja az értékeket a proxyServerNoProxy listán:

```powershell
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.contoso.com"
```

## <a name="next-steps"></a>Következő lépések

- [Linux-alkalmazás üzembe helyezése a Kubernetes-fürtön](./deploy-linux-application.md).
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
