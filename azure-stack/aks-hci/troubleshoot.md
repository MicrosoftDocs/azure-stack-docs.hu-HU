---
title: Hibaelhárítás
description: Útmutató az Azure Kubernetes Service Azure Stack HCI-on való hibaelhárításához
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: e30d5ba784efc6453ce161bc2a87db7c728d3fce
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592936"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás hibaelhárítása Azure Stack HCI-ben

Ha Kubernetes-fürtöt hoz létre vagy kezel az Azure Kubernetes Service-vel Azure Stack HCI-n, időnként problémákba ütközhet. Az alábbi hibaelhárítási irányelvek segítenek a problémák megoldásában. 

## <a name="troubleshooting-azure-stack-hci"></a>Azure Stack HCI hibaelhárítása
A hálózati és tárolási QoS (szolgáltatásminőség) beállításainak a Azure Stack HCI-fürtben lévő kiszolgálókon történő ellenőrzésével kapcsolatos hibák megoldásához, és ellenőrizze, hogy a fontos szabályok meg vannak-e határozva, látogasson el a [fürt-ellenőrzési jelentéskészítéssel kapcsolatos hibákra](/azure-stack/hci/manage/validate-qos)

A CredSSP kapcsolatos problémák elhárításához látogasson el a [CredSSP hibaelhárítása](/azure-stack/hci/manage/troubleshoot-credssp)című webhelyre.

## <a name="troubleshooting-windows-admin-center"></a>A Windows felügyeleti központ hibaelhárítása
Ez a termék jelenleg a nyilvános előzetes verzió állapotban van, ami azt jelenti, hogy még fejlesztés alatt áll. Jelenleg számos probléma van a Windows felügyeleti központ Azure Kubernetes Service bővítménnyel: 
* Az Azure Kubernetes Service Azure Stack HCI-ben való beállításához használt rendszer fürtben jelenleg minden egyes kiszolgálójának megbízható kiszolgálónak kell lennie. Ez azt jelenti, hogy a Windows felügyeleti központnak képesnek kell lennie CredSSP-műveleteket végrehajtani a fürt minden kiszolgálóján, nem csupán egy vagy több közülük. 
* Ha hibát jelez `msft.sme.aks couldn't load` , és a hiba azt jelzi, hogy nem sikerült betölteni az adattömböket, használja a Microsoft Edge vagy a Google Chrome legújabb verzióját, és próbálkozzon újra.
* Az Azure Kubernetes Service Host beállítása varázsló vagy a Kubernetes-fürt létrehozása varázsló elindítása előtt jelentkezzen be az Azure-ba a Windows felügyeleti központon keresztül. A munkafolyamat során szükség lehet az ismételt aláírásra. Ha problémákba ütközik az Azure-ba való bejelentkezéskor a Windows felügyeleti központban, próbáljon meg bejelentkezni az Azure-fiókjába más forrásból, például a [Azure Portal](https://portal.azure.com/). Ha továbbra is problémákba ütközik, tekintse meg a [Windows felügyeleti központ ismert problémáit](/windows-server/manage/windows-admin-center/support/known-issues) ismertető cikket a támogatás elérése előtt.
* Az Azure Kubernetes szolgáltatás aktuális iterációjában a Windows felügyeleti központon keresztüli Azure Stack HCI-n való üzembe helyezéskor csak az Azure Kubernetes Service-gazdagépet telepítő felhasználó hozhat létre Kubernetes-fürtöket a rendszeren. A probléma megkerüléséhez másolja a `.wssd` mappát a felhasználói profilból, és állítsa be az Azure Kubernetes Service Host szolgáltatást azon felhasználói profilba, amely az új Kubernetes-fürtöt fogja elindítani.
* Ha nem megfelelő konfigurációval kapcsolatos hibaüzenetet kap, végezze el a fürt karbantartási műveleteit. Ez magában foglalhatja a `C:\Program Files\AksHci\mocctl.exe` fájl eltávolítását.
* Ahhoz, hogy a CredSSP sikeresen működjön a fürt létrehozása varázslóban, a Windows felügyeleti központot ugyanazzal a fiókkal kell telepíteni és használni. Ha egy fiókkal telepít, majd egy másikkal próbálja használni, a rendszer hibákat eredményez.
* A fürt üzembe helyezése során előfordulhat, hogy probléma van a helm.zip fájlátviteli művelettel. Ez gyakran olyan hibát eredményez, amely azt jelzi, hogy a helm.zip fájl elérési útja nem létezik vagy érvénytelen. A probléma megoldásához lépjen vissza, és próbálja megismételni a telepítést.
* Ha az üzembe helyezés hosszabb ideig leáll, CredSSP vagy kapcsolódási problémák merülhetnek fel. Az alábbi lépéseket követve hárítsa el az üzemelő példányt: 
    1.  A WAC-t futtató gépen futtassa a következő parancsot egy PowerShell-ablakban: 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  Ha ez a parancs sikeres, csatlakozhat a kiszolgálóhoz, és nincs csatlakozási probléma.
    
    3.  Ha CredSSP problémákba ütközik, futtassa ezt a parancsot az átjáró-számítógép és a célszámítógép közötti megbízhatóság teszteléséhez: 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    A következő parancs futtatásával is tesztelheti a megbízhatóságot a helyi átjáró eléréséhez: 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* Ha az Azure arc-t használja, és több bérlői azonosítóval rendelkezik, futtassa a következő parancsot az üzembe helyezés előtt a kívánt bérlő megadásához. Ha ezt elmulasztja, az üzembe helyezési hibát eredményezhet.

```Azure CLI
az login –tenant <tenant>
```
* Ha most létrehozott egy új Azure-fiókot, és nem jelentkezett be a fiókba az átjárót tartalmazó gépen, akkor problémákat tapasztalhat a WAC-átjáró Azure-beli regisztrálásakor. A probléma megoldásához jelentkezzen be az Azure-fiókjába egy másik böngésző lapon vagy ablakban, majd regisztrálja az WAC-átjárót az Azure-ban.

### <a name="creating-windows-admin-center-logs"></a>Windows felügyeleti központ naplóinak létrehozása
Amikor a Windows felügyeleti központban problémákba ütközik, hasznos lehet naplókat csatolni, hogy a fejlesztői csapat diagnosztizálja a problémát. A Windows felügyeleti központban előforduló hibák általában két formában jelennek meg: a Windows felügyeleti központot vagy JavaScriptet futtató gépen az eseménynaplóban megjelenő események, amelyek a böngésző konzoljának felületén jelennek meg. A Windows felügyeleti központ naplóinak összegyűjtéséhez használja a `Get-SMEUILogs.ps1` nyilvános előzetes csomagban megadott parancsfájlt. 
 
A parancsfájl használatához futtassa a következő parancsot abban a könyvtárban, ahol a parancsfájlt tárolja: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
A parancs a következő paraméterekkel rendelkezik:
 
* **-Számítógépnév**: azoknak a számítógépeknek a listája, amelyekről naplókat kíván gyűjteni.
* **-Cél**: az a gép, amelyre a naplókat összesíteni szeretné.
* **-HoursAgo**: Ez a paraméter határozza meg, hogy hány órával ezelőtt a parancsfájl-futtatókörnyezetből származó naplók gyűjtése.
* **-NoCredentialPrompt**: ez egy kapcsoló a hitelesítő adatok kikapcsolására és az alapértelmezett hitelesítő adatok használatára az aktuális környezetben.
 
Ha nehézségekbe ütközik a parancsfájl futtatása során, a következő parancs futtatásával megtekintheti a Súgó szövegét: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Windows Worker-csomópontok hibaelhárítása 
A Windows Worker csomópontba való bejelentkezéshez először futtassa a csomópont IP-címét a futtatásával, `kubectl get` és jegyezze fel a következő `EXTERNAL-IP` értéket:

```PowerShell
kubectl get nodes -o wide
``` 
SSH-t a csomóponton a használatával `ssh Administrator@ip` . Ha az SSH-t a csomópontba helyezi, a futtatásával `net user administrator *` frissítheti a rendszergazda jelszavát. 

## <a name="troubleshooting-linux-worker-nodes"></a>Linux Worker-csomópontok hibaelhárítása 
A Linux Worker csomópontba való bejelentkezéshez először futtassa a csomópont IP-címét a futtatásával, `kubectl get` és jegyezze fel a következő `EXTERNAL-IP` értéket:

```PowerShell
kubectl get nodes -o wide
``` 
SSH-t a csomóponton a használatával `ssh clouduser@ip` . 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>Az Azure arc Kubernetes hibaelhárítása
A kapcsolattal, az engedélyekkel és az ív-ügynökökkel kapcsolatos gyakori forgatókönyvek hibaelhárításához látogasson el az [Azure arc-kompatibilis Kubernetes-hibaelhárításra](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>További lépések
Ha továbbra is problémákba ütközik az Azure Kubernetes szolgáltatás használatakor Azure Stack HCI-en, a fájlok hibái a [githubon](https://aka.ms/aks-hci-issues)keresztül.  
