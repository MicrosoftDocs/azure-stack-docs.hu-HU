---
title: Azure Stack HCI-fürtök frissítése
description: Operációs rendszer és belső vezérlőprogram frissítéseinek alkalmazása Azure Stack HCI-re a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 2ed1a6c2443f7222a873c412e991b4a39b253309
ms.sourcegitcommit: ec19e8455b5cb90a071afb03ec1446b0a9aafb99
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99588648"
---
# <a name="update-azure-stack-hci-clusters"></a>Azure Stack HCI-fürtök frissítése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI-fürtök frissítésekor a cél a rendelkezésre állás fenntartása úgy, hogy egyszerre csak egy kiszolgálót frissít a fürtben. Számos operációsrendszer-frissítéshez szükség van a kiszolgáló offline állapotba helyezésére, például újraindításra vagy olyan szoftverek frissítésére, mint például a hálózati verem. Javasoljuk, hogy a Cluster-Aware Update (CAU) szolgáltatást használja, amely megkönnyíti a frissítések telepítését a fürt minden kiszolgálójára, miközben az alkalmazások futnak. Ha szükséges, a frissítések telepítése és a kiszolgáló újraindítása során Cluster-Aware automatizálja a kiszolgáló bevezetését a karbantartási módból. Cluster-Aware frissítés a Windows felügyeleti központ által használt alapértelmezett frissítési módszer, amely a PowerShell használatával is kezdeményezhető.

Ez a témakör az operációs rendszerre és a szoftverfrissítésekra koncentrál. Ha a hardver karbantartásának elvégzéséhez offline állapotba kell helyeznie egy kiszolgálót, tekintse meg a [kiszolgáló karbantartáshoz való offline](maintain-servers.md)állapotba helyezését ismertető témakört.

## <a name="update-a-cluster-using-windows-admin-center"></a>Fürt frissítése a Windows felügyeleti központtal

A Windows felügyeleti központ megkönnyíti a fürt frissítését, valamint az operációs rendszer és a megoldás frissítéseinek egyszerű felhasználói felületen történő alkalmazását. Ha már vásárolt egy integrált rendszert egy Microsoft-hardveres partnertől, akkor a megfelelő partner-frissítési bővítmény (ek) telepítésével egyszerűen lekérheti a legújabb illesztőprogramokat, belső vezérlőprogramot és egyéb frissítéseket közvetlenül a Windows felügyeleti központból. Ha a hardvert nem integrált rendszerként vásárolta meg, akkor előfordulhat, hogy a hardver gyártójával kapcsolatos javaslatok után külön kell végrehajtani a belső vezérlőprogram és az illesztőprogram frissítéseit.

   > [!WARNING]
   > Ha elindítja a frissítési folyamatot a Windows felügyeleti központtal, folytassa a varázsló használatát, amíg a frissítések befejeződik. Ne kísérelje meg a Cluster-Aware frissítési eszköz használatát, vagy a Windows felügyeleti központban a frissítési folyamat részleges befejezése után frissítsen egy fürtöt a PowerShell-lel. Ha a PowerShell használatával szeretné elvégezni a frissítéseket a Windows felügyeleti központ helyett, ugorjon előre a [fürt frissítéséhez a PowerShell használatával](#update-a-cluster-using-powershell).

A frissítések telepítéséhez kövesse az alábbi lépéseket:

1. Amikor egy fürthöz csatlakozik, a Windows felügyeleti központ irányítópultja riasztást küld, ha egy vagy több kiszolgáló frissítésre készen áll, és megadhat egy hivatkozást a frissítéshez. Azt is megteheti, hogy a bal oldali **eszközök** menüjéből kijelöli a **frissítések** elemet.

2. Ha első alkalommal frissíti a fürtöt, a Windows felügyeleti központ ellenőrizze, hogy a fürt megfelelően van-e konfigurálva Cluster-Aware frissítés futtatásához, és ha szükséges, megkérdezi, hogy szeretné-e, ha a Windows felügyeleti központ konfigurálja a CAU, beleértve a CAU-fürt szerepkörének telepítését és a szükséges tűzfalszabályok engedélyezését is. A frissítési folyamat elindításához kattintson az első **lépések** elemre.

   :::image type="content" source="media/update-cluster/add-cau-role.png" alt-text="A Windows felügyeleti központ automatikusan konfigurálja a fürtöt Cluster-Aware frissítés futtatásához" lightbox="media/update-cluster/add-cau-role.png":::

   > [!NOTE]
   > Ha a Windows felügyeleti központban szeretné használni a Cluster-Aware frissítési eszközt, engedélyeznie kell a hitelesítő adatok biztonsági szolgáltatóját (CredSSP), és explicit hitelesítő adatokat kell megadnia. Ha a rendszer megkérdezi, hogy engedélyezve van-e a CredSSP, kattintson az **Igen** gombra. Adja meg felhasználónevét és jelszavát, majd kattintson a **Continue (folytatás**) gombra.

3. Ekkor megjelenik a fürt frissítési állapota. kattintson a **frissítések keresése** lehetőségre a fürt egyes kiszolgálóihoz elérhető operációsrendszer-frissítések listájának lekéréséhez. Előfordulhat, hogy rendszergazdai hitelesítő adatokat kell megadnia. Ha nincs elérhető operációsrendszer-frissítés, kattintson a **Tovább gombra: hardveres frissítések** , és folytassa a 7. lépéssel.

   > [!IMPORTANT]
   > Ha a frissítések képernyőről indul el, amíg egy frissítés folyamatban van, előfordulhat, hogy váratlan viselkedés fordul elő, például a frissítések lap előzmények szakasza nem megfelelően töltődik be, amíg az aktuális Futtatás be nem fejeződik. Javasoljuk, hogy a Windows felügyeleti központot egy új böngészőablakban vagy ablakban nyissa meg, ha továbbra is használni szeretné az alkalmazást, amíg a frissítések folyamatban vannak.

4. Válassza a Next (tovább) lehetőséget **: telepítse** az operációs rendszer frissítéseinek telepítését, vagy kattintson a **kihagyás** gombra a kizárásához. 

   :::image type="content" source="media/update-cluster/operating-system-updates.png" alt-text="Kattintson a Next (tovább) gombra: telepítse az operációs rendszer frissítéseinek telepítését, vagy kattintson a Kihagyás gombra a kizárásához" lightbox="media/update-cluster/operating-system-updates.png":::

5. Válassza a **telepítés** lehetőséget az operációs rendszer frissítéseinek telepítéséhez a fürt minden kiszolgálóján. A frissítési állapot változása "frissítések telepítése" lesz. Ha a frissítések bármelyike újraindítást igényel, akkor a kiszolgálók egy időben újraindulnak, így a fürt szerepkörei, például a kiszolgálók közötti virtuális gépek áthelyezhetők a leállás megakadályozása érdekében.

   :::image type="content" source="media/update-cluster/install-os-updates.png" alt-text="A telepítés gombra kattintva telepítheti az operációs rendszer frissítéseit a fürt minden kiszolgálójára" lightbox="media/update-cluster/install-os-updates.png":::

6. Az operációs rendszer frissítéseinek befejezését követően a frissítési állapot "sikeres" állapotúra változik. Kattintson a **Tovább gombra: a hardver frissítései** képernyőre való továbblépéshez.

7. A Windows felügyeleti központ megkeresi az adott kiszolgáló hardverét támogató telepített bővítmények fürtjét. Kattintson a **Tovább gombra: telepítse** a hardveres frissítéseket a fürt minden kiszolgálójára. Ha nem találhatók bővítmények vagy frissítések, kattintson a **Kilépés** gombra.

8. A biztonság növelése érdekében tiltsa le a CredSSP, amint befejezte a frissítések telepítését:
    - A Windows felügyeleti központban a **minden kapcsolat** területen válassza ki az első kiszolgálót a fürtben, majd válassza a **Csatlakozás** lehetőséget.
    - Az **Áttekintés** lapon válassza a **CredSSP letiltása** lehetőséget, majd a **CredSSP letiltására** szolgáló előugró ablakban válassza az **Igen** lehetőséget.

## <a name="update-a-cluster-using-powershell"></a>Fürt frissítése a PowerShell használatával

Ahhoz, hogy frissíteni lehessen a fürtöt Cluster-Aware frissítéssel, először telepítenie kell a **feladatátvételi fürtszolgáltatás eszközeit**, amelyek a **Távoli kiszolgálófelügyelet eszközei (RSAT)** részét képezik, és tartalmazzák a Cluster-Aware frissítési szoftverét. Ha meglévő fürtöt frissít, lehetséges, hogy ezek az eszközök már telepítve vannak.

Annak ellenőrzéséhez, hogy a feladatátvevő fürt megfelelően van-e beállítva a szoftverfrissítések alkalmazásához Cluster-Aware frissítéssel, futtassa a **test-CauSetup** PowerShell-parancsmagot, amely a feladatátvevő fürt és a hálózati környezet ajánlott eljárásokat elemző eszközének (BPA) vizsgálatát végzi, és riasztást küld a figyelmeztetésekről és hibákról:

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

Ez a parancs a PowerShell feladatátvevő fürt modulját is telepíti, amely a feladatátvevő fürtök kezelésére szolgáló PowerShell-parancsmagokat, valamint a PowerShell Cluster-Aware frissítési modulját tartalmazza a szoftverfrissítések feladatátvételi fürtökre történő telepítéséhez.

Ha a feladatátvételi fürtszolgáltatás már telepítve van, de a Windows PowerShell feladatátvevő fürt modulja nem, egyszerűen telepítse a fürt minden kiszolgálójára az **install-WindowsFeature** parancsmaggal:

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Frissítési mód kiválasztása

Cluster-Aware a frissítés két módban képes koordinálni a fürt teljes frissítési műveletét:  
  
-   **Önfrissítési mód** Ebben a módban Cluster-Aware a fürtözött szerepkör frissítése munkaterhelésként van konfigurálva a frissítendő feladatátvevő fürtön, és a hozzá tartozó frissítési ütemterv is meg van adva. A fürt az ütemezett időpontokban frissíti magát egy alapértelmezett vagy egyéni frissítési futtatási profil használatával. A frissítési kísérlet során a Cluster-Aware frissítés-koordinátori folyamat elindul azon a csomóponton, amely jelenleg Cluster-Aware a fürtözött szerepkör frissítését végzi, és a folyamat egymás után végrehajtja a frissítéseket az egyes fürtcsomópontokon. Az aktuális fürtcsomópont frissítéséhez Cluster-Aware a fürtözött szerepkör frissítése egy másik fürtcsomóponton történik, és a csomóponton egy új frissítési koordinátori folyamat feltételezi a frissítési kísérlet vezérlését. Az önfrissítési módban Cluster-Aware frissítés a feladatátvételi fürtöt teljesen automatizált, végpontok közötti frissítési folyamattal frissítheti. A rendszergazda ebben a módban is aktiválhatja az igény szerinti frissítéseket, vagy egyszerűen használhatja a távoli frissítési módszert, ha szükséges.
  
-   **Távoli frissítési mód** Ebben a módban egy távoli felügyeleti számítógép (általában Windows 10 rendszerű számítógép), amely hálózati kapcsolattal rendelkezik a feladatátvevő fürthöz, de nem tagja a feladatátvevő fürtszolgáltatási eszközöknek. A rendszergazda, a frissítési koordinátor néven elindít egy igény szerinti frissítési kísérletet egy alapértelmezett vagy egyéni frissítési kísérlet futtatási profil használatával. A távoli frissítési mód hasznos a valós idejű előrehaladás figyelésére a frissítési kísérlet során, valamint a Server Core telepítéseken futó fürtök esetében.  

   > [!NOTE]
   > A Windows 10 2018-es és újabb verziójának frissítése után az RSAT "igény szerinti szolgáltatások"-készletként szerepel a Windows 10 rendszertől kezdve. Egyszerűen lépjen a **beállítások > alkalmazások > alkalmazások & funkciók > választható funkciók > Hozzáadás a szolgáltatás > RSAT: feladatátvételi fürtszolgáltatás eszközei**, és válassza a **telepítés** lehetőséget. A telepítési folyamat megjelenítéséhez kattintson a Vissza gombra az állapot megtekintéséhez a "választható szolgáltatások kezelése" lapon. A telepített szolgáltatás a Windows 10 verziófrissítése alatt is megmarad. Ha az RSAT-t a Windows 10-es verzióra szeretné telepíteni az 2018-es frissítés előtt, [töltsön le egy RSAT-csomagot](https://www.microsoft.com/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>CAU-fürt szerepkör hozzáadása a fürthöz

Az önfrissítési módhoz a fürt szerepkörének Cluster-Aware frissítése szükséges. Ha a Windows felügyeleti központot használja a frissítések végrehajtásához, a rendszer automatikusan hozzáadja a fürt szerepkört.

A **`Get-CauClusterRole`** parancsmag megjeleníti a fürt szerepkörének a megadott fürtön való frissítésének Cluster-Aware konfigurációs tulajdonságait.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Ha a szerepkör még nincs konfigurálva a fürtön, a következő hibaüzenet jelenik meg:

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Ha hozzá szeretné adni a Cluster-Aware az önfrissítési módban a PowerShell használatával, használja a **`Add-CauClusterRole`** parancsmagot, és adja meg a megfelelő [paramétereket](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters), ahogy az alábbi példában látható:

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

## <a name="perform-a-fast-offline-update-of-all-servers-in-a-cluster"></a>A fürtben található összes kiszolgáló gyors, offline frissítésének végrehajtása

Ez a módszer lehetővé teszi, hogy egy fürt összes kiszolgálóját egyszerre le lehessen kapcsolni, és egyszerre frissítse őket. Ezzel időt takaríthat meg a frissítési folyamat során, de a kikapcsolás az üzemeltetett erőforrásokhoz tartozó állásidő.

Ha van olyan kritikus fontosságú biztonsági frissítés, amelyet gyorsan kell alkalmaznia, vagy meg kell győződnie arról, hogy a frissítések befejeződik a karbantartási időszakon belül, ez a módszer lehet Önnek. Ez a folyamat leállítja az Azure Stack HCI-fürtöt, frissíti a kiszolgálókat, és újra megjeleníti.

1. Tervezze meg a karbantartási időszakot.
2. A virtuális lemezek offline állapotba helyezése.
3. Állítsa le a fürtöt a tároló offline állapotba helyezéséhez. Futtassa a  **stop-cluster** parancsmagot, vagy használja a Windows felügyeleti központot a fürt leállításához.
4. Állítsa be a fürtszolgáltatást, hogy **letiltsa** a Services. msc fájlt az egyes kiszolgálókon. Ez megakadályozza, hogy a fürtszolgáltatás a frissítés közben elinduljon.
5. Alkalmazza a Windows Server összesített frissítését és az összes szükséges karbantartási verem frissítését az összes kiszolgálóra. Az összes kiszolgálót egyszerre frissítheti – nem kell megvárnia, mert a fürt le van hajtva.
6. Indítsa újra a kiszolgálókat, és győződjön meg róla, hogy minden jól látható.
7. Állítsa vissza a fürtszolgáltatást **automatikusra** az egyes kiszolgálókon.
8. Indítsa el a fürtöt. Futtassa a **Start-cluster** parancsmagot, vagy használja a Windows felügyeleti központot.

   Néhány percet is igénybe vehet.  Győződjön meg arról, hogy a tárolási készlet kifogástalan állapotban van.

9. A virtuális lemezek újbóli online állapotba helyezése.
10. A virtuális lemezek állapotának figyeléséhez futtassa a **Get-Volume** és a **Get-VirtualDisk** parancsmagot.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Fürtöket támogató frissítés (CAU)](/windows-server/failover-clustering/cluster-aware-updating)
- [A meghajtó belső vezérlőprogram frissítése Közvetlen tárolóhelyek](/windows-server/storage/update-firmware)
- [Azure Stack HCI-fürt érvényesítése](../deploy/validate.md)