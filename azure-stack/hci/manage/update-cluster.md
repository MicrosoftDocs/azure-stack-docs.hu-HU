---
title: Azure Stack HCI-fürtök frissítése
description: Operációs rendszer és belső vezérlőprogram frissítéseinek alkalmazása Azure Stack HCI-re a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 94908514e85df153f69cbeea81a11d4468dfc7fe
ms.sourcegitcommit: e6665cfb15fae57218e58cd6de6053f16c1f9044
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274058"
---
# <a name="update-azure-stack-hci-clusters"></a>Azure Stack HCI-fürtök frissítése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI-fürtök frissítésekor a cél a rendelkezésre állás fenntartása úgy, hogy egyszerre csak egy kiszolgálót frissít a fürtben. Számos operációsrendszer-frissítéshez szükség van a kiszolgáló offline állapotba helyezésére, például újraindításra vagy olyan szoftverek frissítésére, mint például a hálózati verem. Azt javasoljuk, hogy használjon olyan, a [fürtöket támogató frissítést (Cau)](/windows-server/failover-clustering/cluster-aware-updating), amely megkönnyíti a Windows-frissítések telepítését a fürt minden kiszolgálójára, miközben a szoftverfrissítési folyamat automatizálásával megtartja az alkalmazások működését. A fürtöket támogató frissítés a Windows Server minden kiadásában használható, beleértve a Server Core telepítéseket is, és a Windows felügyeleti központban vagy a PowerShell használatával kezdeményezhető.

## <a name="update-a-cluster-using-windows-admin-center"></a>Fürt frissítése a Windows felügyeleti központtal

A Windows felügyeleti központ megkönnyíti a fürt frissítését, valamint az operációs rendszer és a megoldás frissítéseinek egyszerű felhasználói felületen történő alkalmazását. Ha már vásárolt egy integrált rendszert egy Microsoft-hardveres partnertől, akkor a megfelelő partner-frissítési bővítmény (ek) telepítésével egyszerűen lekérheti a legújabb illesztőprogramokat, belső vezérlőprogramot és egyéb frissítéseket közvetlenül a Windows felügyeleti központból. Ha a hardvert nem integrált rendszerként vásárolta meg, akkor előfordulhat, hogy a hardver gyártójával kapcsolatos javaslatok után külön kell végrehajtani a belső vezérlőprogram és az illesztőprogram frissítéseit.

A Windows felügyeleti központ ellenőrizze, hogy a fürt megfelelően van-e konfigurálva a fürtöket támogató frissítés futtatásához, és ha szükséges, megkérdezi, hogy szeretné-e, ha a Windows felügyeleti központ konfigurálja a CAU, beleértve a CAU-fürt szerepkörének telepítését és a szükséges tűzfalszabályok engedélyezését is.

1. Amikor egy fürthöz csatlakozik, a Windows felügyeleti központ irányítópultja riasztást küld, ha egy vagy több kiszolgáló frissítésre készen áll, és megadhat egy hivatkozást a frissítéshez. Azt is megteheti, hogy a bal oldali **eszközök** menüjéből kijelöli a **frissítések** elemet.
1. Ha a Windows felügyeleti központban szeretné használni a fürtöket támogató frissítési eszközt, engedélyeznie kell a hitelesítő adatok biztonsági szolgáltatóját (CredSSP), és explicit hitelesítő adatokat kell megadnia. Ha a rendszer megkérdezi, hogy engedélyezve van-e a CredSSP, kattintson az **Igen**gombra.
1. Adja meg felhasználónevét és jelszavát, majd kattintson a **Continue (folytatás**) gombra.
1. A rendszer minden elérhető frissítést megjelenít; a lista frissítéséhez kattintson az **elérhető frissítések keresése** elemre.
1. Válassza ki a telepíteni kívánt frissítéseket, és kattintson az **összes frissítés alkalmazása**lehetőségre. Ekkor a rendszer telepíti a frissítéseket a fürt minden kiszolgálójára. Ha újraindításra van szükség, a fürt szerepkörei, például a virtuális gépek átkerülnek egy másik kiszolgálóra, hogy elkerülje a fennakadást.
1. A biztonság növelése érdekében tiltsa le a CredSSP, amint befejezte a frissítések telepítését:
    - A Windows felügyeleti központban a **minden kapcsolat**területen válassza ki az első kiszolgálót a fürtben, majd válassza a **Csatlakozás**lehetőséget.
    - Az **Áttekintés** lapon válassza a **CredSSP letiltása**lehetőséget, majd a **CredSSP letiltására** szolgáló előugró ablakban válassza az **Igen**lehetőséget.

## <a name="update-a-cluster-using-powershell"></a>Fürt frissítése a PowerShell használatával

Ahhoz, hogy a fürtöt a fürtöket támogató frissítéssel lehessen frissíteni, először telepítenie kell a **feladatátvételi fürtszolgáltatás eszközeit**, amelyek a **Távoli kiszolgálófelügyelet eszközei (RSAT)** részét képezik, és tartalmazzák a fürt által használható frissítési szoftvert. Ha meglévő fürtöt frissít, lehetséges, hogy ezek az eszközök már telepítve vannak.

Annak ellenőrzéséhez, hogy a feladatátvevő fürt megfelelően van-e beállítva a szoftverfrissítések alkalmazásához a fürtöket támogató frissítéssel, futtassa a **test-CauSetup** PowerShell-parancsmagot, amely a feladatátvevő fürt és a hálózati környezet ajánlott eljárásokat elemző eszközének (BPA) vizsgálatát végzi, és riasztást küld a figyelmeztetésekről és hibákról:

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

Ha funkciókat, eszközöket vagy szerepköröket kell telepítenie, tekintse meg a következő szakaszt. Ellenkező esetben ugorjon a [frissítések keresése a PowerShell](#check-for-updates-with-powershell)-lel című lépésre.

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>A feladatátvételi fürtszolgáltatás és a feladatátvételi fürtszolgáltatás eszközeinek telepítése a PowerShell használatával

Annak vizsgálatához, hogy a fürt vagy a kiszolgáló rendelkezik-e a feladatátvételi fürtszolgáltatással és a feladatátvételi fürtszolgáltatással már telepítve van-e, adja ki a **`Get-WindowsFeature`** PowerShell-parancsmagot a felügyeleti számítógépről (vagy futtassa közvetlenül a fürtön vagy a kiszolgálón, a-számítógépnév paraméter kihagyása nélkül):

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

Győződjön meg arról, hogy a "telepítési állapot" érték van telepítve, és hogy az X a feladatátvételi fürtszolgáltatás és a Windows PowerShell feladatátvevő fürt modulja előtt jelenik meg:

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

Ha a feladatátvételi fürtszolgáltatás nincs telepítve, telepítse azt a fürt minden kiszolgálójára a **`Install-WindowsFeature`** parancsmaggal, a-IncludeAllSubFeature és a-IncludeManagementTools paraméterek használatával:

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

Ez a parancs a PowerShell feladatátvevő fürt modulját is telepíti, amely tartalmazza a feladatátvevő fürtök kezelésére szolgáló PowerShell-parancsmagokat, valamint a PowerShell-hez készült, a fürthöz tartozó frissítési modult, amely a szoftverfrissítések feladatátvételi fürtökön való telepítését végzi.

Ha a feladatátvételi fürtszolgáltatás már telepítve van, de a Windows PowerShell feladatátvevő fürt modulja nem, egyszerűen telepítse a fürt minden kiszolgálójára az **install-WindowsFeature** parancsmaggal:

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Frissítési mód kiválasztása

A fürtöket támogató frissítés két módban képes koordinálni a fürt teljes frissítési műveletét:  
  
-   **Önfrissítési mód** Ebben a módban a fürttel kompatibilis frissítés fürtözött szerepkör a frissítendő feladatátvevő fürt számítási feladatának megfelelően van konfigurálva, és egy kapcsolódó frissítési ütemterv van definiálva. A fürt az ütemezett időpontokban frissíti magát egy alapértelmezett vagy egyéni frissítési futtatási profil használatával. A frissítési kísérlet során a fürtöket támogató frissítési koordinátori folyamat elindul azon a csomóponton, amely jelenleg a fürttel kompatibilis frissítés fürtözött szerepkört birtokolja, és a folyamat egymás után végrehajtja a frissítéseket az egyes fürtcsomópontokon. Az aktuális fürtcsomópont frissítéséhez a fürtözött szerepkör frissítése feladatátvételt hajt végre egy másik fürtcsomóponton, és a csomóponton egy új frissítési koordinátori folyamat feltételezi a frissítési kísérlet vezérlését. Önfrissítő módban a fürtöket támogató frissítése a teljes körűen automatizált, végpontok közötti frissítési folyamat használatával frissítheti a feladatátvevő fürtöt. A rendszergazda ebben a módban is aktiválhatja az igény szerinti frissítéseket, vagy egyszerűen használhatja a távoli frissítési módszert, ha szükséges.
  
-   **Távoli frissítési mód** Ebben a módban egy távoli felügyeleti számítógép (általában Windows 10 rendszerű számítógép), amely hálózati kapcsolattal rendelkezik a feladatátvevő fürthöz, de nem tagja a feladatátvevő fürtszolgáltatási eszközöknek. A rendszergazda, a frissítési koordinátor néven elindít egy igény szerinti frissítési kísérletet egy alapértelmezett vagy egyéni frissítési kísérlet futtatási profil használatával. A távoli frissítési mód hasznos a valós idejű előrehaladás figyelésére a frissítési kísérlet során, valamint a Server Core telepítéseken futó fürtök esetében.  


   > [!NOTE]
   > A Windows 10 2018-es és újabb verziójának frissítése után az RSAT "igény szerinti szolgáltatások"-készletként szerepel a Windows 10 rendszertől kezdve. Egyszerűen lépjen a **beállítások > alkalmazások > alkalmazások & funkciók > választható funkciók > Hozzáadás a szolgáltatás > RSAT: feladatátvételi fürtszolgáltatás eszközei**, és válassza a **telepítés**lehetőséget. A telepítési folyamat megjelenítéséhez kattintson a Vissza gombra az állapot megtekintéséhez a "választható szolgáltatások kezelése" lapon. A telepített szolgáltatás a Windows 10 verziófrissítése alatt is megmarad. Ha az RSAT-t a Windows 10-es verzióra szeretné telepíteni az 2018-es frissítés előtt, [töltsön le egy RSAT-csomagot](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>CAU-fürt szerepkör hozzáadása a fürthöz

Az önfrissítési mód esetében a fürtre vonatkozó frissítési fürt szerepkörre van szükség. Ha a Windows felügyeleti központot használja a frissítések végrehajtásához, a rendszer automatikusan hozzáadja a fürt szerepkört.

A **`Get-CauClusterRole`** parancsmag megjeleníti a fürtre vonatkozó frissítési fürt szerepkör konfigurációs tulajdonságait a megadott fürtön.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Ha a szerepkör még nincs konfigurálva a fürtön, a következő hibaüzenet jelenik meg:

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Ha az önfrissítési módban a PowerShell használatával szeretné felvenni a fürtre vonatkozó frissítési fürtszolgáltatást, használja a **`Add-CauClusterRole`** parancsmagot, és adja meg a megfelelő [paramétereket](/powershell/module/clusterawareupdating/add-cauclusterrole?view=win10-ps#parameters), ahogy az alábbi példában is látható:

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > A fenti parancsot egy felügyeleti SZÁMÍTÓGÉPRŐL vagy tartományvezérlőről kell futtatni.

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>Tűzfalszabályok engedélyezése távoli újraindítások engedélyezéséhez

A frissítési folyamat során engedélyeznie kell a kiszolgálók távoli újraindítását. Ha a Windows felügyeleti központot használja a frissítések elvégzéséhez, a Windows tűzfal szabályai automatikusan frissülnek az egyes kiszolgálókon a távoli újraindítások engedélyezéséhez. Ha a PowerShell-lel frissít, engedélyezze a távoli leállítás tűzfalszabály a Windows tűzfalon, vagy adja át a-EnableFirewallRules paramétert a parancsmagnak, például a fenti példában.

## <a name="check-for-updates-with-powershell"></a>Frissítések keresése a PowerShell-lel

A **`Invoke-CAUScan`** parancsmag segítségével beolvashatja a megfelelő frissítések kiszolgálóit, és megtekintheti a megadott fürt egyes kiszolgálóin alkalmazott frissítések kezdeti készletét:

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

A lista létrehozása néhány percet is igénybe vehet. Az előnézet lista csak a frissítések kezdeti készletét tartalmazza. nem tartalmazza azokat a frissítéseket, amelyek a kezdeti frissítések telepítése után alkalmazhatók lesznek.

## <a name="install-updates-with-powershell"></a>Frissítések telepítése a PowerShell-lel

A kiszolgálók megfelelő frissítésekhez való vizsgálatához és a megadott fürtön a teljes frissítési kísérlet végrehajtásához használja a következő **`Invoke-CAURun`** parancsmagot:

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

Ez a parancs végrehajt egy vizsgálatot és egy teljes frissítési műveletet a Cluster1 nevű fürtön. Ez a parancsmag a Microsoft. WindowsUpdatePlugin beépülő modult használja, és megköveteli, hogy az összes fürtcsomópont online állapotba kerüljön a parancsmag futtatása előtt. Emellett ez a parancsmag legfeljebb három újrapróbálkozást engedélyez a csomóponton, mielőtt a csomópontot nem sikerült megadnia, és lehetővé teszi, hogy ne kelljen egynél több csomópontot végrehajtani, mielőtt a teljes frissítési kísérletet nem sikerült megjelölni. Emellett a tűzfalszabályok lehetővé teszik a kiszolgálók távoli újraindítását. Mivel a parancs meghatározza a Force paramétert, a parancsmag megerősítési kérések megjelenítése nélkül fut.

A frissítési futtatási folyamat a következőket tartalmazza: 
- A megfelelő frissítések keresése és letöltése a fürt minden kiszolgálóján
- Jelenleg futó fürtözött szerepkörök áthelyezése az egyes kiszolgálókról
- A frissítések telepítése az egyes kiszolgálókon
- A kiszolgáló újraindítása, ha a telepített frissítések megkövetelik
- A fürtözött szerepkörök visszaállítása az eredeti kiszolgálóra

A frissítési Futtatás folyamata magában foglalja a kvórum karbantartásának biztosítását is, a további frissítéseket, amelyek csak a frissítések kezdeti készletének telepítése után telepíthetők, valamint a végrehajtott műveletek jelentésének mentése.

## <a name="check-on-the-status-of-an-updating-run"></a>Frissítési kísérlet állapotának keresése

A (z) parancsmag futtatásával a rendszergazdák összegző információkat kaphatnak a folyamatban lévő frissítési kísérletekről **`Get-CauRun`** :

```PowerShell
Get-CauRun -ClusterName Cluster1
```

Íme néhány példa a kimenetre:

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [A meghajtó belső vezérlőprogram frissítése Közvetlen tárolóhelyek](/windows-server/storage/update-firmware)
- [Azure Stack HCI-fürt ellenőrzése](../deploy/validate.md)
