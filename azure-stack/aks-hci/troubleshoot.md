---
title: Az AK hibaelhárítása
description: Ez a cikk az Azure Kubernetes Service (ak) Azure Stack HCI-ben történő hibaelhárításával kapcsolatos információkat tartalmaz.
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 262648ce8c7d6c94fb7f9b55deaa5f78b7c5fb56
ms.sourcegitcommit: 6a0f7f452998c404a80ca9d788dbf3cdf4d78b38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893917"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás hibaelhárítása Azure Stack HCI-ben

Ha Kubernetes-fürtöt hoz létre vagy kezel az Azure Kubernetes Service használatával Azure Stack HCI-n, időnként problémákba ütközhet. Ez a cikk a problémák megoldásához segítséget nyújtó hibaelhárítási irányelveket tartalmaz.

## <a name="troubleshooting-azure-stack-hci"></a>Azure Stack HCI hibaelhárítása
A hálózati és tárolási QoS (szolgáltatásminőség) beállításainak a Azure Stack HCI-fürtben lévő kiszolgálókon történő ellenőrzésével kapcsolatos hibák megoldásához, és ellenőrizze, hogy a fontos szabályok meg vannak-e határozva. lásd: a [fürt ellenőrzési jelentéskészítésének hibája](/azure-stack/hci/manage/validate-qos).

A CredSSP hibaelhárításával kapcsolatos további tudnivalókért lásd: [CredSSP hibaelhárítása](/azure-stack/hci/manage/troubleshoot-credssp).

## <a name="troubleshooting-windows-admin-center"></a>A Windows felügyeleti központ hibaelhárítása
Ez a termék nyilvános előzetes verzióban érhető el, ami azt jelenti, hogy még fejlesztés alatt áll. Jelenleg vannak problémák a Windows felügyeleti központ Azure Kubernetes Service bővítménnyel: 
* Jelenleg az Azure Kubernetes Service Azure Stack HCI-ben való beállításához használt rendszer fürtben lévő minden egyes kiszolgálójának megbízható kiszolgálónak kell lennie. Így a Windows felügyeleti központnak képesnek kell lennie CredSSP-műveletek futtatására a fürt összes kiszolgálóján, nem csupán egy vagy néhány közülük. 
* Ha a következő hibaüzenet jelenik meg `msft.sme.aks couldn't load` , és a hiba azt eredményezi, hogy nem sikerült betölteni az adattömböket, használja a Microsoft Edge vagy a Google Chrome legújabb verzióját, és próbálkozzon újra.
* Az Azure Kubernetes Service Host vagy a Kubernetes-fürt létrehozásához szükséges varázsló elindítása előtt jelentkezzen be az Azure-ba a Windows felügyeleti központon keresztül. Előfordulhat, hogy a munkafolyamatban újra be kell jelentkeznie. Ha problémába ütközik az Azure-ba való bejelentkezéssel a Windows felügyeleti központban, próbáljon meg bejelentkezni az Azure-fiókjába más forrásból, például a [Azure Portal](https://portal.azure.com/). Ha továbbra is problémákba ütközik, tekintse meg a [Windows felügyeleti központ ismert problémáit](/windows-server/manage/windows-admin-center/support/known-issues) ismertető cikket, mielőtt felvette a kapcsolatot a támogatási szolgálattal.
* Az Azure Kubernetes szolgáltatás aktuális iterációjában a Windows felügyeleti központon keresztüli Azure Stack HCI-n való üzembe helyezéskor csak az Azure Kubernetes Service-gazdagépet telepítő felhasználó hozhat létre Kubernetes-fürtöket a rendszeren. A probléma megkerüléséhez másolja a. WSSD mappát annak a felhasználónak a profiljából, aki az Azure Kubernetes Service hostt állítja be az új Kubernetes-fürtöt létrehozó felhasználó profiljába.
* Ha nem megfelelő konfigurációval kapcsolatos hibaüzenetet kap, végezze el a fürt karbantartási műveleteit. Előfordulhat, hogy ezek a műveletek magukban foglalják a C:\Program Files\AksHci\mocctl.exe fájl eltávolítását.
* Ahhoz, hogy a CredSSP sikeresen működjön a fürt létrehozása varázslóban, a Windows felügyeleti központot ugyanazzal a fiókkal kell telepíteni és használni. Ha egy fiókkal telepíti a Windows felügyeleti központot, és megpróbálja azt egy másikkal használni, hibaüzenetet kap.
* A fürt üzembe helyezése során előfordulhat, hogy a helm.zip fájlátviteli probléma merül fel. Ez a probléma gyakran olyan hibát okoz, amely szerint a helm.zip fájl elérési útja nem létezik vagy érvénytelen. A probléma megoldásához próbálja megismételni a telepítést.
* Ha az üzembe helyezés hosszabb ideig leáll, lehet, hogy CredSSP vagy kapcsolódási problémák léptek fel. Próbálja ki ezeket a lépéseket az üzemelő példány hibaelhárításához: 
    1.  A Windows felügyeleti központot futtató gépen futtassa a következő parancsot egy PowerShell-ablakban: 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  Ha ez a parancs sikeres, csatlakozhat a kiszolgálóhoz, és nincs kapcsolódási probléma.
    
    3.  Ha CredSSP problémák léptek fel, futtassa ezt a parancsot az átjáró-számítógép és a célszámítógép közötti megbízhatóság teszteléséhez: 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        A következő parancs futtatásával is tesztelheti a megbízhatóságot a helyi átjáró eléréséhez: 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* Ha az Azure arc-t használja, és több bérlői azonosítóval rendelkezik, futtassa a következő parancsot a kívánt bérlő megadásához az üzembe helyezés előtt. Ha nem, a telepítés sikertelen lehet.

   ```Azure CLI
   az login –tenant <tenant>
   ```
* Ha most létrehozott egy új Azure-fiókot, és nem jelentkezett be a fiókba az átjárót tartalmazó gépen, akkor problémákba ütközhet a Windows felügyeleti központ átjárójának az Azure-ban való regisztrálásával. A probléma megoldásához jelentkezzen be az Azure-fiókjába egy másik böngésző lapon vagy ablakban, majd regisztrálja a Windows felügyeleti központ átjáróját az Azure-ban.

### <a name="creating-windows-admin-center-logs"></a>Windows felügyeleti központ naplóinak létrehozása
Ha a Windows felügyeleti központtal kapcsolatos problémákat jelent, érdemes lehet olyan naplókat csatolni, amelyek segítenek a fejlesztői csapatnak a probléma diagnosztizálásában. A Windows felügyeleti központban előforduló hibák általában a két űrlap egyikében jönnek: 
- A Windows felügyeleti központot futtató számítógép eseménynaplójában megjelenő események 
- A böngésző konzoljának felületén előforduló JavaScript-problémák 

A Windows felügyeleti központ naplóinak összegyűjtéséhez használja a nyilvános előzetes csomagjában megadott Get-SMEUILogs.ps1 szkriptet. 
 
A parancsfájl használatához futtassa ezt a parancsot abban a mappában, ahol a szkriptet tárolja: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
A parancs a következő paraméterekkel rendelkezik:
 
* `-ComputerNames`: Azoknak a számítógépeknek a listája, amelyekről naplókat kíván gyűjteni.
* `-Destination`: Az a gép, amelyre a naplókat összesíteni szeretné.
* `-HoursAgo`: A naplók gyűjtésének kezdési időpontja a parancsfájl futtatása előtt órában megadva.
* `-NoCredentialPrompt`: A hitelesítő adatok kikapcsolására és az aktuális környezetben használt alapértelmezett hitelesítő adatok használatára szolgáló kapcsoló.
 
Ha nehézségekbe ütközik a parancsfájl futtatása során, a következő parancs futtatásával megtekintheti a Súgó szövegét: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Windows Worker-csomópontok hibaelhárítása 
Ha be szeretne jelentkezni egy Windows Worker-csomópontra, először futtassa a csomópont IP-címét `kubectl get` . Jegyezze fel az `EXTERNAL-IP` értéket.

```PowerShell
kubectl get nodes -o wide
``` 
SSH-t a csomópontra a használatával `ssh Administrator@ip` . Miután az SSH-t a csomópontba helyezi, a futtatásával `net user administrator *` frissítheti a rendszergazdai jelszavát. 

## <a name="troubleshooting-linux-worker-nodes"></a>Linux Worker-csomópontok hibaelhárítása 
A Linux Worker csomópontba való bejelentkezéshez először futtassa a csomópont IP-címét `kubectl get` . Jegyezze fel az `EXTERNAL-IP` értéket.

```PowerShell
kubectl get nodes -o wide
``` 
SSH-t a csomópontra a használatával `ssh clouduser@ip` . 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Az Azure arc Kubernetes hibaelhárítása
A kapcsolattal, az engedélyekkel és az ív ügynökkel kapcsolatos gyakori forgatókönyvek hibaelhárításával kapcsolatban lásd: [Azure arc-kompatibilis Kubernetes hibaelhárítása](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Következő lépések
Ha továbbra is problémákba kerül, amikor az Azure Kubernetes szolgáltatást használja Azure Stack HCI-n, a hibákat a [githubon](https://aka.ms/aks-hci-issues)keresztül teheti meg.  
