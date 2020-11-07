---
title: Virtuális gépek kezelése a Windows felügyeleti központtal
description: Megtudhatja, hogyan hozhat létre és kezelhet virtuális gépeket egy fürtben Azure Stack HCI-ben a Windows felügyeleti központ használatával.
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360267"
---
# <a name="manage-vms-with-windows-admin-center"></a>Virtuális gépek kezelése a Windows felügyeleti központtal

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A Windows felügyeleti központ használatával létrehozhatók és kezelhetők a virtuális gépek (VM-EK) Azure Stack HCI-ben.

## <a name="create-a-new-vm"></a>Új virtuális gép létrehozása

Egyszerűen létrehozhat egy új virtuális gépet a Windows felügyeleti központból.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Új VM-képernyő" lightbox="media/manage-vm/new-vm.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a kiszolgálót vagy fürtöt, amelyen létre szeretné hozni a virtuális gépet.
1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **virtuális gépek** területen válassza a **leltár** fület, majd kattintson a **Hozzáadás** és az **új** elemre.
1. Az **új virtuális gép** területen adja meg a virtuális gép nevét.
1. Válassza a **2. generáció (ajánlott)** lehetőséget.
1. A **gazdagép** területen válassza ki azt a kiszolgálót, amelyen a virtuális gép található.
1. Az **elérési út** alatt válassza ki a legördülő listából az előosztott fájl elérési útját, vagy a **Tallózás** gombra kattintva válassza ki azt a mappát, amelybe menteni szeretné a virtuálisgép-konfigurációt és a virtuális merevlemez (VHD) fájljait. A hálózaton található bármely elérhető SMB-megosztáshoz megkeresheti az elérési utat a *\\ server\share* érték megadásával.

1. A **virtuális processzorok** területen válassza ki a virtuális processzorok számát, valamint azt, hogy engedélyezve van-e a beágyazott virtualizálás a virtuális gépen.
1. A **memória** területen válassza ki az indítási memória mennyiségét (legalább 4 GB ajánlott), valamint a dinamikus memória minimális és maximális tartományát a virtuális gép számára.
1. A **hálózat** területen válasszon ki egy hálózati adaptert a legördülő listából.
1. A **tárterület** területen kattintson a **Hozzáadás** gombra, és válassza ki, hogy új üres virtuális merevlemezt kíván-e létrehozni, vagy meglévő virtuális merevlemezt szeretne használni. Ha meglévő virtuális merevlemezt használ, kattintson a **Tallózás** gombra, és válassza ki a megfelelő fájl elérési útját.  
1. Az **operációs rendszer** területen tegye a következők egyikét:
   - Válassza az **operációs rendszer telepítése később** lehetőséget, ha a virtuális gép létrehozása után a virtuális gép operációs rendszerét szeretné telepíteni.
   - Válassza az **operációs rendszer telepítése képfájlból (*. ISO)** lehetőséget, kattintson a **Tallózás** gombra, majd válassza ki a használni kívánt. iso lemezképfájlt.
1. Ha elkészült, kattintson a **Létrehozás** gombra a virtuális gép létrehozásához.
1. A virtuális gép indításához a **Virtual Machines** listában vigye az egérmutatót az új virtuális gépre, jelölje be a jelölőnégyzetet a bal oldalon, majd kattintson a **Start** gombra.
1. Az **állapot** alatt ellenőrizze, hogy a virtuális gép állapota **fut** -e.

## <a name="get-a-list-of-vms"></a>Virtuális gépek listájának lekérése

A kiszolgálókon vagy a fürtön egyszerűen megtekintheti az összes virtuális gépet.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Virtuális gépek képernyő" lightbox="media/manage-vm/vm-inventory.png":::

1. A Windows felügyeleti központban a **Tools (eszközök** ) területen görgessen lefelé, és válassza a **Virtual Machines** lehetőséget.
1. A jobb oldali  **leltár** lap felsorolja az aktuális kiszolgálón vagy a fürtön elérhető összes virtuális gépet, valamint parancsokat biztosít az egyes virtuális gépek kezeléséhez. A következőket teheti:
    - Az aktuális kiszolgálón vagy fürtön futó virtuális gépek listájának megtekintése.
    - A virtuális gép állapotának és gazdagépének megtekintése, ha virtuális gépeket tekint meg egy fürthöz. Megtekintheti a CPU-és memóriahasználat is a gazdagép szemszögéből, beleértve a memória terhelését, a memória igényét és a hozzárendelt memóriát, valamint a virtuális gép üzemidőt, szívverési állapotát és védelmi állapotát (Azure Site Recovery használatával).
    - Hozzon létre egy új virtuális gépet.
    - Törlés, indítás, kikapcsolás, Leállítás, szüneteltetés, folytatás, alaphelyzetbe állítás vagy Átnevezés virtuális gép. Mentse a virtuális gépet is, törölje a mentett állapotot, vagy hozzon létre egy ellenőrzőpontot.
    - Virtuális gép beállításainak módosítása
    - Kapcsolódjon egy virtuálisgép-konzolhoz a Hyper-V-gazdagépen keresztül.
    - Virtuális gép replikálása Azure Site Recovery használatával.
    - Több virtuális gépen futtatható műveleteknél (például indítás, Leállítás, mentés, szüneteltetés, törlés vagy alaphelyzetbe állítás) több virtuális gépet is kijelölhet, és egyszer futtathatja a műveletet.

## <a name="view-vm-details"></a>Virtuális gép részleteinek megtekintése

Egy adott virtuális géphez tartozó részletes információkat és teljesítménymutatókat a dedikált oldaláról tekintheti meg.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Virtual Machines – részletes információ képernyő" lightbox="media/manage-vm/vm-details.png":::

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. Kattintson a jobb oldali **leltár** lapra, majd válassza ki a virtuális gépet. A következő oldalon a következőket teheti:

   - Megtekintheti a processzor, a memória, a hálózat, a IOPS és az IO átviteli sebességének élő és korábbi adatsorokhoz tartozó diagramokat (a korábbi adatok csak hiperkonvergens-fürtök esetén érhetők el)
   - Ellenőrzőpontok megtekintése, létrehozása, alkalmazása, átnevezése és törlése.
   - Megtekintheti a virtuális merevlemezek (. vhd) fájljait, a hálózati adaptereket és a gazdagép-kiszolgáló adatait.
   - A virtuális gép állapotának megtekintése.
   - Mentse a virtuális gépet, törölje a mentett állapotot, exportálja vagy Clone a virtuális gépet.
   - Módosítsa a virtuális gép beállításait.
   - Kapcsolódjon a virtuálisgép-konzolhoz a VMConnect használatával a Hyper-V-gazdagépen keresztül.
   - A virtuális gép replikálása Azure Site Recovery használatával.

## <a name="view-aggregate-vm-metrics"></a>Összesített VM-metrikák megtekintése

Megtekintheti a fürt összes virtuális gépe erőforrás-használati és teljesítmény-mérőszámait.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="gazdagép metrikái képernyője" lightbox="media/manage-vm/host-metrics.png":::

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A jobb oldali **Összefoglalás** lapon átfogó képet kaphat a Hyper-V-gazdagép erőforrásairól és teljesítményéről egy kiválasztott kiszolgálóhoz vagy fürthöz, beleértve a következőket:
    - A futó, leállított, szüneteltetett és mentett virtuális gépek száma
    - A legutóbbi állapot-riasztások vagy a Hyper-V Eseménynapló eseményei a fürtökhöz
    - CPU-és memóriahasználat a gazdagép vs vendég bontásával
    - Élő és korábbi adatsorok diagramjai a fürtök IOPS és I/O-átviteli sebességéhez

## <a name="change-vm-settings"></a>Virtuális gép beállításainak módosítása

A virtuális gépek különböző beállításokkal módosíthatók.

> [!NOTE]
> Néhány beállítás nem módosítható egy rendszert futtató virtuális gép esetében, ezért először le kell állítania a virtuális gépet.

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. Kattintson a jobb oldali **leltár** fülre, válassza ki a virtuális gépet, majd kattintson a **Beállítások** elemre.

1. A virtuális gép indítási/leállítási műveleteinek és általános beállításainak módosításához válassza az **általános** lehetőséget, és tegye a következőket:
    - A virtuális gép nevének módosításához adja meg a nevet a **név** mezőben.
    - A virtuális gépek alapértelmezett indítási/leállítási műveleteinek módosításához válassza ki a megfelelő beállításokat a legördülő listából.
    - A virtuális gép szüneteltetésének vagy indításának időintervallumának módosításához adja meg a megfelelő értékeket a megjelenített mezőkben.

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="VM általános beállítások képernyő" lightbox="media/manage-vm/vm-settings-general.png":::

1. Válassza a **memória** lehetőséget a virtuális gép indítási memóriájának, a dinamikus memória tartományának, a memória pufferének százalékának és a memória súlyozásának módosításához.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="VM-memória beállításainak módosítása képernyő" lightbox="media/manage-vm/vm-settings-memory.png":::

1. Válassza a **processzorok** lehetőséget a virtuális processzorok számának módosításához, a beágyazott virtualizálás engedélyezéséhez vagy az egyidejű TÖBBSZÁLÚ (SMT) engedélyezéséhez.

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Virtuálisgép-processzor beállításainak módosítása képernyő" lightbox="media/manage-vm/vm-settings-processor.png":::

1. Egy meglévő lemez méretének módosításához módosítsa a **méret (GB)** értéket. Új virtuális lemez hozzáadásához válassza a **lemezek** lehetőséget, majd válassza ki, hogy üres virtuális lemezt kíván-e létrehozni, vagy meglévő virtuális lemezt vagy ISO-fájlt (. ISO) szeretne használni. Kattintson a **Tallózás** gombra, és válassza ki a virtuális lemez vagy képfájl elérési útját.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="VM-lemez beállításainak módosítása képernyő" lightbox="media/manage-vm/vm-settings-disk.png":::

1. A hálózati adapter beállításainak hozzáadásához, eltávolításához vagy módosításához válassza a **hálózatok** lehetőséget, és tegye a következőket:
    - Határozza meg a használni kívánt virtuális kapcsolót, valamint azt, hogy engedélyezi-e a virtuális helyi hálózat azonosítását (a VLAN-azonosítót is meg kell adnia)
    - A hálózati adapter adapter további beállításainak módosításához kattintson a **speciális** gombra a következő lehetőségekhez:
        - A dinamikus vagy a statikus MAC-címek típusának kiválasztása
        - MAC-címek hamisításának engedélyezése
        - A sávszélesség-kezelés engedélyezése és a maximális/minimális tartomány meghatározása

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Virtuálisgép-hálózat beállításainak módosítása képernyő" lightbox="media/manage-vm/vm-settings-network.png":::

1. Rendszerindító eszközök hozzáadásához és a virtuális gép rendszerindítási sorrendjének módosításához válassza a **rendszerindítás sorrendjét** .

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Virtuális gép rendszerindítási sorrendjének módosítása képernyő" lightbox="media/manage-vm/vm-settings-boot.png":::

1. Válassza az **ellenőrzőpontok** lehetőséget a virtuális gép ellenőrzőpontok engedélyezéséhez, válassza az ellenőrzőpont típusa lehetőséget, és adja meg az ellenőrzőpont-fájl helyét.

    > [!NOTE]
    > Az **éles** ellenőrzőpont beállítás ajánlott, és a vendég operációs rendszer biztonsági mentési technológiáját használja az adatkonzisztens ellenőrzőpontok létrehozásához. A **standard** beállítás VHD-pillanatképeket használ az alkalmazás-és szolgáltatási állapotú ellenőrzőpontok létrehozásához.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="VIRTUÁLIS gépek ellenőrzőpontjának módosítása képernyő" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. Válassza az **affinitási szabályok** lehetőséget egy virtuális gép affinitási szabályának létrehozásához. Az affinitási szabályok létrehozásával kapcsolatos további információkért lásd: [kiszolgáló-és hely-affinitási szabályok létrehozása virtuális gépekhez](vm-affinity.md).

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="VM-affinitási szabály képernyője" lightbox="media/manage-vm/vm-affinity.png":::

1. A virtuális gép biztonsági beállításainak módosításához válassza a **Biztonság** lehetőséget, és tegye a következőket:
    - Válassza a **biztonságos rendszerindítás engedélyezése** lehetőséget, hogy megakadályozza a jogosulatlan kód futtatását a rendszerindításkor (ajánlott). A legördülő listából válassza ki a Microsoft vagy a nyílt forráskódú sablont is
    - A **sablon** mezőben válassza ki a használni kívánt biztonsági sablont

    - A **titkosítás támogatása** lehetőségnél

        - A hardveres titkosítási szolgáltatás moduljának használatához válassza az **platformmegbízhatósági modul engedélyezése** lehetőséget.

        - Az állapot és a virtuális gép áttelepítési forgalmának titkosításának engedélyezése

        > [!NOTE]
        > A titkosítási támogatáshoz a virtuális gép Key Protector (KP) szükséges. Ha még nem jelennek meg, az egyik lehetőség kiválasztásával létrejön egy olyan KP, amely lehetővé teszi a virtuális gép futtatását ezen a gazdagépen.

    - A **biztonsági házirend** területen válassza a védelem **engedélyezése** lehetőséget a virtuális gép további védelmi lehetőségeihez.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Virtuális gép biztonsági beállításainak módosítása" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>Virtuális gép áthelyezése másik kiszolgálóra vagy fürtre

A virtuális gépet egyszerűen áthelyezheti egy másik kiszolgálóra vagy egy másik fürtre a következőképpen:

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **leltár** lapon válasszon ki egy virtuális gépet a listából, és válassza a **kezelés > áthelyezés** lehetőséget.
1. Válasszon ki egy kiszolgálót a listából, és válassza az **Áthelyezés** lehetőséget.
1. Ha a virtuális gépet és a tárolót is át szeretné helyezni, válassza ki, hogy áthelyezi-e egy másik fürtre vagy egy másik kiszolgálóra ugyanabban a fürtben.

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="VM-képernyő áthelyezése" lightbox="media/manage-vm/vm-more-move.png":::

1. Ha csak a virtuális gép tárterületét szeretné áthelyezni, válassza az egyiket, hogy ugyanarra az elérési útra váltson, vagy válasszon különböző elérési utakat a konfigurációhoz, az ellenőrzőponthoz vagy az intelligens lapozáshoz.

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="VM-tároló áthelyezése képernyő" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>Virtuális gép csatlakoztatása tartományhoz

Egyszerűen csatlakoztathat egy virtuális gépet egy tartományhoz az alábbiak szerint:

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="VM-tartományhoz való csatlakozás képernyő áthelyezése" lightbox="media/manage-vm/vm-domain-join.png":::

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **leltár** lapon válasszon ki egy virtuális gépet a listából, és válassza a **kezelés > tartományhoz illesztés** lehetőséget.
1. Adja meg annak a tartománynak a nevét, amelyhez csatlakozni szeretne, valamint a tartomány felhasználónevét és jelszavát.
1. Adja meg a virtuális gép felhasználónevét és jelszavát.
1. Ha elkészült, kattintson a **Csatlakozás** elemre.

## <a name="clone-a-vm"></a>Virtuális gép klónozása

A virtuális gépeket a következőképpen telepítheti egyszerűen:

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. Válassza ki a **leltár** fület a jobb oldalon. Válasszon ki egy virtuális gépet a listából, és válassza a **> klónozás kezelése** lehetőséget.
1. Adja meg a klónozott virtuális gép nevét és elérési útját.
1. Futtassa a Sysprep eszközt a virtuális gépen, ha még nem tette meg.

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="Virtuális gép klónozása képernyő" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>Virtuális gép importálása vagy exportálása

Egyszerűen importálhat vagy exportálhat egy virtuális gépet. A következő eljárás ismerteti az importálási folyamatot.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Virtuális gép importálása képernyő" lightbox="media/manage-vm/vm-more-import.png":::

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **leltár** lapon válassza a **Hozzáadás > importálás** lehetőséget.
1. Adja meg a virtuális gépet tartalmazó mappa nevét, vagy kattintson a **Tallózás** gombra, és válasszon egy mappát.
1. Válassza ki az importálni kívánt virtuális gépet.
1. Szükség esetén hozzon létre egyedi azonosítót a virtuális géphez.
1. Ha elkészült, válassza az **Importálás** lehetőséget.

A virtuális gépek exportálásához a folyamat hasonló:

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **leltár** lapon válassza ki azt a virtuális gépet, amelyet exportálni szeretne a listában.
1. Válassza a **kezelés > exportálás** lehetőséget.
1. Adja meg a virtuális gép exportálásának elérési útját.

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="VM-képernyő exportálása" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>VIRTUÁLIS gépek eseménynaplóinak megtekintése

A virtuális gépek eseménynaplóit az alábbiak szerint tekintheti meg:

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A jobb oldali **Összefoglalás** lapon válassza az **összes esemény megtekintése** lehetőséget.
1. Válassza ki az esemény kategóriáját, és bontsa ki a nézetet.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Kapcsolódás virtuális géphez Távoli asztal használatával

A Windows felügyeleti központ használata helyett a Hyper-V-gazdagépen egy RDP protokoll (RDP) kapcsolat használatával is kezelheti a virtuális gépeket.

1. Az **eszközök** alatt görgessen le, és válassza a **virtuális gépek** lehetőséget.
1. A **leltár** lapon válassza ki a virtuális gép kiválasztása elemet a listából, és válassza a **csatlakozási > csatlakozási** vagy **csatlakozási > RDP-fájl letöltése** lehetőséget. Mindkét lehetőség a VMConnect eszközzel csatlakozik a vendég virtuális géphez a Hyper-V-gazdagépen keresztül, és a Hyper-V-gazdagéphez meg kell adnia a rendszergazdai felhasználónevet és a jelszóhoz tartozó hitelesítő adatokat.

    - A **kapcsolódási** lehetőség a virtuális géphez a webböngészőben távoli asztal használatával csatlakozik.

    - Az **RDP-fájl letöltése** lehetőség letölt egy. rdp fájlt, amelyet megnyithat a távoli asztali kapcsolat alkalmazással (mstsc.exe) való kapcsolódáshoz.

## <a name="protect-vms-with-azure-site-recovery"></a>Virtuális gépek Azure Site Recovery-védelemmel való ellátása

A Windows felügyeleti központ használatával konfigurálhatja Azure Site Recovery és replikálhatja a helyszíni virtuális gépeket az Azure-ba. Ez egy nem kötelezően megadandó szolgáltatás. Első lépésként tekintse meg a [virtuális gépek Azure site Recovery használatával történő védelemét](azure-site-recovery.md)ismertető témakört.

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="A telepítő Azure Site Recovery képernyő" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>További lépések

A virtuális gépeket a Windows PowerShell használatával is létrehozhatja és kezelheti. További információ: [virtuális gépek kezelése Azure stack HCI-ben a Windows PowerShell használatával](vm-powershell.md).