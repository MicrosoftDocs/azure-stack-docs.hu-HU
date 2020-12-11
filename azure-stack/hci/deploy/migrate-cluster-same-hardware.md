---
title: Áttelepítés ugyanarra a hardverre Azure Stack HCI-re
description: Megtudhatja, hogyan telepíthet át egy fürtöt Azure Stack HCI-re ugyanazon a hardveren
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ea19dbbdd85f29eb036a0220828bbbb7bca33ea7
ms.sourcegitcommit: d91d44762383790a0bcfc4a85f43050c8528d5d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97069808"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>Áttelepítés ugyanarra a hardverre Azure Stack HCI-re

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Ez a témakör azt ismerteti, hogyan telepíthet át egy Windows Server 2016-es vagy Windows Server 2019-fürtöt a meglévő kiszolgálói hardver használatával Azure Stack HCI-re. Ez a folyamat telepíti az új Azure Stack HCI operációs rendszert, és megőrzi a fürt meglévő beállításait és tárhelyét, és importálja a virtuális gépeket.

Az alábbi ábra a Windows Server-fürt helyben történő áttelepítését mutatja be ugyanazon kiszolgálói hardver használatával. A fürt leállítása után Azure Stack HCI van telepítve, a tárterület újra van csatolva, és a virtuális gépek importálása és magas rendelkezésre állása (HA) történik.

:::image type="content" source="media/migrate/migrate-cluster-same-hardware.png" alt-text="Fürt átmigrálása Azure Stack HCI-re ugyanazon a hardveren" lightbox="media/migrate/migrate-cluster-same-hardware.png":::

Ha a virtuális gépeket új Azure Stack HCI-hardverre szeretné áttelepíteni, olvassa el az [áttelepítés Azure stack HCI-re új hardveren](migrate-cluster-new-hardware.md)című témakört.

> [!NOTE]
> A kiterjesztett fürtök áttelepítését ebben a cikkben nem tárgyaljuk.

## <a name="before-you-begin"></a>Előkészületek

Az áttelepítés megkezdése előtt több követelményt és megfontolandó szempontot kell figyelembe venni:

- Minden Windows PowerShell-parancsot rendszergazdaként kell futtatni.

- A Azure Stack HCI-hez rendszergazdai engedélyekkel rendelkező tartományi hitelesítő adatokkal kell rendelkeznie.

- A forráskiszolgálón lévő összes virtuális gép biztonsági mentése. Fejezze be az összes alkalmazás és szolgáltatás összeomlás-konzisztens biztonsági mentését, valamint az összes adatbázis alkalmazás-konzisztens biztonsági mentését.  Az Azure-ba történő biztonsági mentéshez lásd: [Azure Backup használata](https://docs.microsoft.com/azure-stack/hci/manage/use-azure-backup).

- Összegyűjti az összes fürtcsomópont leltárát és konfigurációját, a fürt elnevezését, a hálózati konfigurációt, a Fürt megosztott kötete (CSV) rugalmasságot és kapacitást, valamint a kvórum tanúsító.

- A fürt virtuális gépei, a kapcsolat nélküli CSV, az offline tárolóeszközök és a fürtszolgáltatás leállítása.
- Tiltsa le a fürt neve objektumot (CNO) (később újra felhasználjuk) és:
    - Győződjön meg arról, hogy a CNO saját szervezeti egységhez (OU) hoz létre objektumokat.
    - Győződjön meg róla, hogy az örökölt házirend blokkolva van a szervezeti egységen
    - A szükséges házirend beállítása Azure Stack HCI-hez ezen a szervezeti egységen

## <a name="vm-version-support-and-update"></a>A VM verziójának támogatása és frissítése

Az alábbi táblázat a Windows Server operációsrendszer-verzióit és azok virtuálisgép-verzióit sorolja fel helyben történő áttelepítéshez ugyanazon a hardveren.

Függetlenül attól, hogy a virtuális gép operációs rendszerének milyen verziója fut a rendszeren, az Azure Stack HCI-re való áttelepítéshez támogatott minimális virtuálisgép-verzió 5,0-as verzió. Így a Windows Server 2016 vagy a Windows Server 2019 rendszeren futó 2,0, 3,0 vagy 4,0 verziójú virtuális gépeket a Migrálás előtt frissíteni kell az 5,0-es verzióra.

|Operációs rendszer verziója|VM-verzió|
|---|---|
|Windows Server 2008 SP1|2,0|
|Windows Server 2008 R2|3,0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5,0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

A Windows Server 2008 SP1, a Windows Server 2008 R2-SP1 és a Windows 2012 fürtökön futó virtuális gépek esetében a közvetlen áttelepítés Azure Stack HCI-re nem támogatott. Ezekben az esetekben két lehetőség közül választhat:

- Telepítse át ezeket a virtuális gépeket a Windows Server 2012 R2, a Windows Server 2016 vagy a Windows Server 2019 rendszerre először, frissítse a virtuális gép verzióját, majd kezdje el az áttelepítési folyamatot.

- A Robocopy paranccsal másolja az összes virtuális merevlemezt Azure Stack HCI-re. Ezután hozzon létre új virtuális gépeket, és csatolja a másolt virtuális merevlemezeket a megfelelő virtuális gépekhez Azure Stack HCI-ben. Ezzel megkerüli a virtuális gépek verziójának korlátozását a régebbi virtuális gépek esetében.

## <a name="updating-the-vm-version"></a>A virtuális gép verziójának frissítése

A következő parancs használatával jelenítheti meg az összes virtuálisgép-verziót egyetlen kiszolgálón:

```powershell
Get-VM * | Format-Table Name,Version
```

A Windows Server-fürt összes csomópontján lévő összes virtuális gép verziójának megjelenítése:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Az összes virtuális gép frissítése a legújabb verzióra az összes Windows Server-csomóponton:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>A kiszolgálók és a fürt frissítése

A Migrálás a Windows Server-példányon futó Azure Stack HCI telepítő futtatásával biztosít tiszta operációsrendszer-telepítést a virtuális gépekkel és a tárolóval. Ez lecseréli az aktuális operációs rendszert Azure Stack HCI-vel. Részletes információkért lásd: [a Azure stack HCI operációs rendszer telepítése](operating-system.md). Ezt követően hozzon létre egy új Azure Stack HCI-fürtöt, csatlakoztassa újra a tárolót, és importálja a virtuális gépeket.

1. Leállíthatja a meglévő fürtözött virtuális gépeket, a kapcsolat nélküli CSV, az offline tárolóhelyeket és a Fürtszolgáltatást.

1. Lépjen arra a helyre, ahova letöltötte a Azure Stack HCI BITS szolgáltatást, majd futtassa Azure Stack HCI telepítőt minden Windows Server-csomóponton.

1. A telepítés során válassza **az egyéni lehetőséget: telepítse a csak az Azure stack HCI újabb verzióját (speciális)**. Ismételje meg az összes kiszolgálót.

1. Hozza létre az új Azure Stack HCI-fürtöt. Ehhez a Windows felügyeleti központot vagy a Windows PowerShellt használhatja az alább leírtak szerint.  

> [!IMPORTANT]
> A Hyper-V virtuális kapcsoló ( `VMSwitch` ) nevének meg kell egyeznie a fürt konfigurációs leltárában rögzített névvel. Győződjön meg arról, hogy az Azure Stack HCI-fürtön használt virtuális kapcsoló neve megegyezik az eredeti forrás virtuális kapcsolójának nevével a virtuális gépek importálása előtt.

> [!NOTE]
> Ahhoz, hogy új virtuális gépeket hozna létre, regisztrálnia kell az Azure Stack HCI-fürtöt az Azure-ban. További információ: [regisztráció az Azure](register-with-azure.md)-ban.

### <a name="using-windows-admin-center"></a>A Windows felügyeleti központ használata

Ha a Windows felügyeleti központot használja a Azure Stack HCI-fürt létrehozásához, a fürt létrehozása varázsló automatikusan telepíti az összes szükséges szerepkört és szolgáltatást az egyes kiszolgálók csomópontjain.

A fürt létrehozásával kapcsolatos részletes információkért lásd: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központban](create-cluster.md).

> [!IMPORTANT]
> Ugorja át a **4,1** lépést a fürt létrehozása varázslóban. Ellenkező esetben törölheti a meglévő virtuális gépeket és a tárolót.

1. Indítsa el a fürt létrehozása varázslót. A **4. lépés: Storage**:

1. Hagyja ki a 4,1 lépést a **tiszta meghajtókon**. Ezt ne tegye meg.

1. Lépjen ki a varázslóból.

1. Nyissa meg a PowerShellt, és futtassa a következő parancsmagot az új azonosító létrehozásához `Storagesubsystem Object` , az összes tárolóhely újbóli felderítéséhez és a SES-meghajtók számának hozzárendeléséhez:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    Ha a Windows Server 2016 rendszerről végez áttelepítést, akkor ez egy új `ClusterperformanceHistory` ReFS-kötetet is létrehoz, és hozzárendeli azt a SDDC-fürterőforrás-csoporthoz.

    Ha a Windows Server 2019 rendszerről végez áttelepítést, akkor hozzáadja a meglévő `ClusterperformanceHistory` ReFS-kötetet, és hozzárendeli azt a SDDC-fürterőforrás-csoporthoz.

1. Lépjen vissza a varázslóba. A 4,2. lépésben **ellenőrizze a meghajtókat**, és ellenőrizze, hogy az összes meghajtó megjelenik-e figyelmeztetések és hibák nélkül.

1. Végezze el a varázsló lépéseit.

### <a name="using-windows-powershell"></a>A Windows PowerShell használata

Ha a PowerShell használatával hozza létre az Azure Stack HCI-fürtöt, a következő parancsmaggal kell telepíteni a következő szerepköröket és szolgáltatásokat az egyes Azure Stack HCI-fürtcsomópontokon:

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

A fürt PowerShell használatával történő létrehozásával kapcsolatos további információkért lásd: [Azure stack HCI-fürt létrehozása a Windows PowerShell használatával](create-cluster-powershell.md).

> [!NOTE]
> Használja újra ugyanazt a nevet a korábban letiltott fürt neve objektumhoz.

1. Futtassa a következő parancsmagot a fürt létrehozásához:

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. Futtassa az alábbi parancsmagot az új azonosító létrehozásához `Storagesubsystem Object` , az összes tárolóhely újbóli felderítéséhez, valamint a SES-meghajtók számának hozzárendeléséhez:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. Ha a Windows Server 2016 rendszerről végez áttelepítést, akkor ez egy új `ClusterperformanceHistory` ReFS-kötetet is létrehoz, és hozzárendeli azt a SDDC-fürterőforrás-csoporthoz.

    > [!NOTE]
    > Ha egy tároló a másodlagos lemez hibáit mutatja (megtekinthető a Fürtfelügyelőben), futtassa újra a `Enable-ClusterS2D -verbose` parancsmagot.

1. A Fürtfelügyelő használatával engedélyezzen minden CSV- `ClusterperformanceHistory` t a kötet kivételével, amely egy ReFS kötet (Ügyeljen arra, hogy ez ne REFS CSV-fájl).

1. Ha a Windows Server 2019 rendszerről végez áttelepítést, futtassa újra a `Enable-ClusterS2D -verbose` parancsmagot. Ez a ReFS- `ClusterperformanceHistory` kötetet társítja a SDDC fürterőforrás-csoporttal.

1. Határozza meg az aktuális Storage-készlet nevét és verzióját a következő futtatásával:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Most határozza meg az új Storage-készlet nevét és verzióját:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Hozza létre a kvórum tanúsító. További információ: a [tanúsító fürt beállítása](https://docs.microsoft.com/azure-stack/hci/deploy/witness).

1. Győződjön meg arról, hogy a tárterület-javítási feladatok befejeződtek a következők szerint:

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >Ez a frissítés során futó virtuális gépek számától függően jelentős időt is igénybe vehet.

1. Ellenőrizze, hogy az összes lemez kifogástalan állapotban van-e:

    ```powershell
    Get-VirtualDisk
    ```

1. Határozza meg a fürtcsomópont verziószámát, amely a és a értéket jeleníti meg `ClusterFunctionalLevel` `ClusterUpgradeVersion` . Futtassa az alábbi parancsot a beszerzéséhez:

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` a automatikusan be van állítva, `10` és nem igényel frissítést az operációs rendszer és a fürt új létrehozása miatt.

1. Frissítse a Storage-készletet a következőképpen:

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>ReFS-kötetek

Ha a Windows Server 2016 rendszerről végez áttelepítést, a rugalmas fájlrendszer (ReFS) kötetei támogatottak, de ezek a kötetek nem élvezik az Azure Stack HCI következő teljesítménybeli fejlesztéseit:

- Tükrözött, gyorsított paritás
- TÉRKÉPi napló megkerülése

Ezeknek a fejlesztéseknek új ReFS-kötetet kell létrehoznia a `New-Volume` parancsmag használatával.

> [!NOTE]
> A Windows Server 2016 Mirror gyorsított paritású kötetek esetében a ReFS tömörítés nem volt elérhető, ezért a kötetek újbóli csatolása rendben van, de kevésbé lesz végrehajtva, mint egy Azure Stack HCI-fürtön lévő új TÉRKÉPi kötet létrehozásához.

## <a name="import-the-vms"></a>A virtuális gépek importálása

Az ajánlott eljárás az, hogy a fürt csomópontjain hozzon létre legalább egy Fürt megosztott kötete (CSV), hogy az egyes CSV-tulajdonosok számára egyenletes egyensúlyt biztosítson a virtuális gépek számítási feladatainak nagyobb rugalmassága, teljesítménye és méretezése érdekében. Alapértelmezés szerint ez az egyenleg öt percenként automatikusan megtörténik, és figyelembe kell venni, amikor a Robocopy a forrás-és a cél fürt csomópontja között van, hogy a forrás és a cél CSV-tulajdonosa megfelel a legoptimálisabb átviteli útvonalnak és sebességnek.

A virtuális gépek importálásához hajtsa végre az alábbi lépéseket a Azure Stack HCI-fürtön, hogy azok elérhetők legyenek, és indítsa el őket:

1. Futtassa a következő parancsmagot az összes CSV tulajdonosi csomópont megjelenítéséhez:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Az egyes kiszolgálói csomópontok esetében válassza `C:\Clusterstorage\Volume` a lehetőséget, és adja meg az elérési utat az összes virtuális gép számára – például: `C:\Clusterstorage\volume01` .

1. Futtassa a következő parancsmagot az egyes CSV-tulajdonosi csomópontokon, hogy a virtuális gép összes VMCX-fájljának elérési útja megjelenjen a virtuálisgép-importálás előtt. Módosítsa az elérési utat a környezetének megfelelően:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

1. Futtassa az alábbi parancsmagot minden egyes kiszolgáló-csomóponthoz az összes virtuális gép importálásához és regisztrálásához, és tegye azokat elérhetővé minden CSV-tulajdonosi csomóponton. Ez biztosítja a virtuális gépek egyenletes eloszlását a processzor és a memória optimális kiosztása érdekében:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Minden cél virtuális gép elindítása minden csomóponton:

    ```powershell
    Start-VM -Name
    ```

1. Jelentkezzen be, és ellenőrizze, hogy minden virtuális gép fut-e, és hogy az összes alkalmazás és adatok elérhetők-e:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Végül frissítse a virtuális gépeket a legújabb Azure Stack HCI-verzióra, hogy kihasználhassa az összes előrelépést:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>Következő lépések

- A fürt érvényesítése az áttelepítés után. Lásd: [Azure stack HCI-fürt ellenőrzése](validate.md).
- Ha Windows Server rendszerű virtuális gépeket szeretne áttelepíteni új Azure Stack HCI-hardverre, tekintse [meg az áttelepítés Azure stack a HCI-re új hardveren](migrate-cluster-new-hardware.md)