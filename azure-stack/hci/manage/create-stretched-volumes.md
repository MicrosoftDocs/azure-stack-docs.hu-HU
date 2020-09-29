---
title: Kifeszített fürtözött kötetek létrehozása és a replikáció beállítása
description: Kötetek létrehozása és a kiterjesztett fürtök replikálásának beállítása Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.
author: v-dasis
ms.author: v-dasis
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 8c885f7a344ff9d34228bad76c79e65f1f345f25
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573683"
---
# <a name="create-stretched-cluster-volumes-and-set-up-replication"></a>Kifeszített fürtözött kötetek létrehozása és a replikáció beállítása

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a cikk bemutatja, hogyan hozhat létre köteteket, és hogyan állíthatja be a kiterjesztett fürtök replikálását Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.

A köteteket két helyen, két kiszolgálón, példaként két kiszolgálón hozzuk létre. Ne feledje azonban, hogy ha háromutas tükrözési köteteket szeretne létrehozni, akkor legalább hat kiszolgálót kell használnia.

## <a name="stretched-volumes-and-replication-using-windows-admin-center"></a>Kiterjesztett kötetek és replikálás a Windows felügyeleti központtal

Kötet létrehozása és a replikáció beállítása:

1. A Windows felügyeleti központ **eszközök**területén válassza a **kötetek**elemet.
1. A jobb oldali ablaktáblában válassza a **leltár** fület, majd kattintson a **Létrehozás**gombra.
1. A **kötet létrehozása** panelen válassza a **kötet replikálása helyek között**lehetőséget.
1. Válassza ki a helyek közötti replikációs irányt a legördülő listából.
1. A **replikálási mód**területen válassza az **aszinkron** vagy a **szinkron**lehetőséget.
1. Adja meg a forrás replikációs csoport nevét és a cél replikációs csoport nevét.
1. Adja meg a naplózási kötet kívánt méretét.
1. A **speciális**lehetőségnél tegye a következőket:
     - Adja meg vagy módosítsa a **forrás replikációs csoportjának nevét**.
     - Adja meg vagy módosítsa a **cél replikációs csoport nevét**.
     - A cél... elemnél a már beadott **blokkok használatához**jelölje be ezt a jelölőnégyzetet.
     - A **replikációs forgalom titkosításához**jelölje be ezt a jelölőnégyzetet.
     - A **konzisztencia-csoportok engedélyezéséhez**jelölje be ezt a jelölőnégyzetet.
1. Ha végzett, kattintson a **Létrehozás** gombra.
1. A jobb oldali ablaktáblában ellenőrizze, hogy az adatlemez és a naplófájl létre van-e hozva az elsődleges (aktív) helyen, valamint hogy a rendszer létrehozza-e a megfelelő és a naplózási replika lemezeket a másodlagos (passzív) helyen. Kétirányú replikáció esetén két adathalmazt és kötetet tartalmazó lemezt kell látnia.
1. Az **eszközök**területen válassza a **tárolási replika**elemet.
1. A jobb oldali ablaktábla **partnerségek**területén ellenőrizze, hogy a replikációs partnerség sikeresen létrejött-e.

Ezt követően a virtuális gépek és egyéb számítási feladatok telepítése előtt ellenőriznie kell, hogy sikerült-e a sikeres adatreplikálás a helyek között. További információért lásd a replikáció ellenőrzése című szakaszt [a fürt ellenőrzése](../deploy/validate.md) című részben.

## <a name="create-stretched-volumes-using-powershell"></a>Kiterjesztett kötetek létrehozása a PowerShell használatával

A kötet létrehozása eltérő az egyhelyes standard fürtökhöz és a kibővített (Kéthelyes) fürtökhöz. Mindkét forgatókönyv esetében azonban a `New-Volume` parancsmag használatával létrehozhat egy virtuális lemezt, particionálja és formázza azt, létrehoz egy egyező nevű kötetet, és hozzáadja azt a fürt megosztott köteteihez (CSV).

A többhelyes fürtökhöz tartozó kötetek és virtuális lemezek létrehozása valamivel többet vesz igénybe, mint a különálló fürtök esetében. A kibővített fürtök legalább négy kötetet igényelnek – két adatkötetet és két naplózási kötetet, az egyes helyekhez tartozó adat/naplózási kötet párokkal. Ezután létre kell hoznia egy replikációs csoportot az egyes helyekhez, és be kell állítania a közöttük lévő replikációt. A kiszolgálóról a kiszolgálóra történő adatcsoportokat át kell helyezni. `Move-ClusterGroup`Ezt a parancsmagot használja a rendszer.

1. Először a `Available Storage` következő parancsmagot kell áthelyezni a Storage `Server1` -erőforráskészlet `Site1` használatával `Move-ClusterGroup` :

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
    ```

1. Ezután hozza létre az első virtuális lemezt ( `Disk1` ) a következőhöz `Server1` `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Hozzon létre egy második virtuális lemezt ( `Disk2` ) a következőhöz `Server1` `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Most végezze el a `Available Storage` csoport offline állapotba helyezését:

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

1. És helyezze át a `Available Storage` csoportot a `Server3` következőre `Site2` :

    ```powershell
    Move-ClusterGroup -Name 'Available Storage' -Node Server3
    ```

1. Hozza létre az első virtuális lemezt ( `Disk3` ) a következőn `Server3` `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. És hozzon létre egy második virtuális lemezt ( `Disk4` ) a következőn `Server3` `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Most végezze el a `Available Storage` csoport offline állapotba helyezését, majd helyezze vissza a következő kiszolgálók egyikére `Site1` :

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
    ```

1. A `Get-ClusterResource` parancsmag használatával győződjön meg arról, hogy négy virtuális lemez lett létrehozva, kettő minden egyes tárolási készletben:

    ```powershell
    Get-ClusterResource -Cluster ClusterS1
    ```

1. Most adja hozzá `Disk1` a fürt megosztott köteteihez:

    ```powershell
    Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
    ```

Befejezte a kötetek létrehozását, és készen áll a tárolási replika beállítására a replikáláshoz.

## <a name="set-up-replication-using-powershell"></a>Replikáció beállítása a PowerShell használatával

Ha a PowerShell használatával állít be tárolási replikát egy kibővített fürthöz, a rendszer a forrásadatok során használt lemezt Fürt megosztott kötete (CSV) kell hozzáadnia. Az összes többi lemez nem CSV-meghajtóként kell maradnia a rendelkezésre álló tár csoportban. Ezeket a lemezeket ezután a rendszer fürt megosztott Köteteiként adja hozzá a tárolási replika létrehozási folyamata során.

Az előző lépésben a virtuális lemezek a meghajtóbetűjelek használatával lettek hozzáadva, hogy könnyebben azonosíthatók legyenek. A Storage-replika egy-az-egyhez replikáció, ami azt jelenti, hogy egyetlen lemez replikálható egy másik lemezre.

### <a name="step-1-validate-the-topology-for-replication"></a>1. lépés: replikációs topológia ellenőrzése

A Kezdés előtt futtassa a `Test-SRTopology` parancsmagot hosszabb ideig (például több óráig). A `Test-SRTopology` parancsmag érvényesít egy lehetséges replikációs partnert, és érvényesíti a helyi gazdagépet a célkiszolgálón, vagy távolról a forrás-és a célkiszolgáló között.

Ez a parancsmag ellenőrzi a következőket:

- Az SMB a hálózaton keresztül érhető el, ami azt jelenti, hogy a 445-es és a 5445-es TCP-port a kétirányúan nyitott.
- A WS-MAN HTTP-n keresztül érhető el a hálózaton, ami azt jelenti, hogy a 5985-es és a 5986-es TCP-port nyitva van.
- Az SR WMIv2-szolgáltató hozzáfér, és fogadja a kérelmeket.
- A forrás-és a célhely adatkötetei léteznek és írhatók.
- A forrás-és a cél naplózási kötetek NTFS-formázással vagy ReFS formázással és elegendő szabad hellyel rendelkeznek.
- A tárterület GPT formátumban van inicializálva, nem MBR-ben, a megfelelő szektor méretével.
- Elegendő fizikai memória áll rendelkezésre a replikáció futtatásához.

Emellett a parancsmag a `Test-SRTopology` következőket is méri:

- Az ICMP időközbeni késése és a jelentés átlaga.
- Teljesítményszámlálók a bemeneti/kimeneti adatok írásához és a köteten látható átlag jelentéséhez.
- A kezdeti szinkronizálás becsült ideje.

A test-SRTopology befejezését követően a rendszer létrehoz egy. html-fájlt (TestSrTopologyReport a dátummal és idővel) a Windows Temp mappában. Minden figyelmeztetést vagy hibát meg kell vizsgálni, mert a tárolási replika nem hozható létre megfelelően.

Egy példa, amely 5 órán keresztül fog futni:

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="step-2-create-the-replication-partnership"></a>2. lépés: a replikációs partnerség létrehozása

Most, hogy befejezte a `Test-SRTopology` teszteket, készen áll a tárolási replika konfigurálására és a replikációs partnerség létrehozására. Dióhéjban a Storage-replikát úgy konfigurálja, hogy minden helyhez létrehoz egy replikációs csoportot (RG), és megadja az adatköteteket és a naplózási köteteket a hely1 (Kiszolgáló1, Kiszolgáló2) és a cél (replikált) kiszolgálói csomópontok között a Site2-ben (Server3, Server4).

Kezdjük:

1. Adja hozzá a hely1 adatlemezt Fürt megosztott köteteként (CSV):

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. A rendelkezésre álló tár csoportnak az aktuálisan bejelentkezett csomópontnak kell lennie. A csoport a következő paranccsal helyezhető át a Kiszolgáló1-be:

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. A replikációs partnerség létrehozásához használja a `New-SRPartnership` parancsmagot. Ezzel a parancsmaggal megadhatja a forrás adatmennyiséget és a naplózási kötetek nevét is:

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

A `New-SRPartnership` parancsmag replikációs kapcsolatot hoz létre a két hely két replikációs csoportja között. Ebben a példában `Replication1` a hely1-ben a Kiszolgáló1 elsődleges csomópontjának replikációs csoportja, `Replication2` a pedig a Site2 csomópont Server3 replikációs csoportja.

A Storage-replika mostantól mindent beállít. Ha bármilyen replikált adattal rendelkezik, azt itt fogja elvégezni. A replikáláshoz szükséges adatmennyiségtől függően ez eltarthat egy ideig. Azt javasoljuk, hogy a folyamat befejezéséig ne helyezzen át egyetlen csoportot sem.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó témakörökhöz és az egyéb tárolási felügyeleti feladatokhoz lásd még:

- [A kifeszített fürt áttekintése](../concepts/stretched-clusters.md)
- [Kötetek megtervezése](../concepts/plan-volumes.md)
- [Kötetek kiterjesztése](extend-volumes.md)
- [Kötetek törlése](delete-volumes.md)
