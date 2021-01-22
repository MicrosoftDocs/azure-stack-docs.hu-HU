---
title: Azure Stack HCI-fürt létrehozása a Windows Admin Centerrel
description: Megtudhatja, hogyan hozhat létre kiszolgálófürt Azure Stack HCI-hez a Windows felügyeleti központtal
author: v-dasis
ms.topic: how-to
ms.date: 01/20/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 0d92840b8a98d612c971895cd3cc9f39815eed75
ms.sourcegitcommit: c87d1e26a4f96be4651f63fbf5ea3d98d6f14832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659408"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Azure Stack HCI-fürt létrehozása a Windows Admin Centerrel

> A következőre vonatkozik Azure Stack HCI, Version v20H2

Ebből a cikkből megtudhatja, hogyan használhatja a Windows felügyeleti központot olyan Azure Stack HCI-fürt létrehozásához, amely Közvetlen tárolóhelyekt használ. A Windows felügyeleti központban a fürt létrehozása varázsló a nagy mennyiségű felemelést végzi el Önnek. Ha inkább a PowerShell-lel szeretné elvégezni, tekintse meg [az Azure stack HCI-fürt létrehozása a PowerShell használatával](create-cluster-powershell.md)című témakört. A PowerShell-cikk emellett jó információforrás a varázsló csuklyáján és hibaelhárítási célokra szolgáló információkkal.

Két fürt típusának létrehozása közül választhat:

- Standard fürt legalább két, egyetlen helyen található kiszolgáló-csomóponttal.
- A kiterjesztett fürt legalább négy olyan kiszolgáló-csomóponttal rendelkezik, amely két helyen található, és legalább két csomóponttal rendelkezik.

További információ a kifeszített fürtökről: a [kiterjesztett fürtök áttekintése](../concepts/stretched-clusters.md).

Ha érdekli a Azure Stack HCI kipróbálása, de korlátozott vagy nem rendelkezik tartalék hardverrel, tekintse meg a [Azure stack HCI-értékelési útmutatót](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), ahol áttekintheti a Azure stack HCI-t a beágyazott virtualizálás használatával, akár az Azure-ban, akár a helyszínen, akár egyetlen fizikai rendszeren.

## <a name="before-you-run-the-wizard"></a>A varázsló futtatása előtt

A fürt létrehozása varázsló futtatása előtt győződjön meg róla, hogy:

- Olvassa el a hardverre és a kapcsolódó követelményekre vonatkozó követelményeket a [rendszerkövetelményekben](../concepts/system-requirements.md).
- Olvassa el a Azure Stack HCI [fizikai hálózati követelményeit](../concepts/physical-network-requirements.md) és a [gazdagép hálózati követelményeit](../concepts/host-network-requirements.md) .
- Telepítse a Azure Stack HCI operációs rendszert a fürt minden kiszolgálóján. Lásd: [a Azure stack HCI operációs rendszer telepítése](operating-system.md).
- Olyan fiókkal kell rendelkeznie, amely tagja a helyi Rendszergazdák csoportnak az egyes kiszolgálókon.
- Telepítse a Windows felügyeleti központot egy számítógépre vagy kiszolgálóra a felügyelethez. Lásd: a [Windows felügyeleti központ telepítése](/windows-server/manage/windows-admin-center/deploy/install).
- A többhelyes fürtök esetében a két helyet előre kell beállítani Active Directoryban. De ne aggódjon, a varázsló az Ön számára is beállíthatja őket.

Ha a Windows felügyeleti központot egy kiszolgálón (helyi számítógép helyett) futtatja, olyan fiókot használjon, amely az átjáró-rendszergazdák csoport tagja, vagy a helyi Rendszergazdák csoport a Windows felügyeleti központ kiszolgálóján.

Emellett a Windows felügyeleti központ felügyeleti számítógépének ugyanahhoz a Active Directory tartományhoz kell tartoznia, amelyben létre kívánja hozni a fürtöt, vagy egy teljesen megbízható tartományt. A fürthöz tartozó kiszolgálóknak még nem kell a tartományhoz tartoznia; a fürt létrehozása során hozzáadhatók a tartományhoz.

A fürt létrehozása varázsló főbb lépései:

1. Első **lépések** – gondoskodik arról, hogy minden kiszolgáló teljesítse a fürthöz való csatlakozáshoz szükséges előfeltételeket és szolgáltatásokat.
1. **Hálózatkezelés** – hálózati adapterek hozzárendelésére és konfigurálására szolgál, és létrehozza a virtuális kapcsolókat az egyes kiszolgálókhoz.
1. **Fürtözés** – ellenőrzi, hogy a fürt megfelelően van-e beállítva. A kifeszített fürtök esetében a két helyet is beállítja.
1. **Storage** – közvetlen tárolóhelyek konfigurálása.

A varázsló befejezése után be kell állítania a tanúsító fürtöt, regisztrálnia kell az Azure-ban, és létre kell hoznia a köteteket (amely a helyek közötti replikációt is beállítja, ha kifeszített fürtöt hoz létre).

A varázsló elindítása előtt ellenőrizze, hogy telepítve van-e a legújabb Windows felügyeleti központ-bővítmények, különösen a fürt létrehozási bővítménye. Ehhez tegye a következőket:

1. Nyissa meg a Windows felügyeleti központot, és kattintson a beállítások (fogaskerék ikon) elemre a jobb felső sarokban.
1. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet.
1. Válassza a **fürt létrehozása** lehetőséget, majd kattintson a **telepítés** gombra.
1. Válassza ki a **Fürtfelügyelő** elemet, majd kattintson a **telepítés** gombra.

Most már készen áll, ezért kezdjük:

1. A Windows felügyeleti központban a **minden kapcsolat** területen kattintson a **Hozzáadás** elemre.
1. Az **erőforrások hozzáadása vagy létrehozása** panel **kiszolgálófürtök területén válassza az** **új létrehozása** lehetőséget.
1. **1. Válassza a fürt típusa** lehetőséget, majd **Azure stack HCI** elemet.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Fürt létrehozása varázsló – HCI lehetőség" lightbox="media/cluster/create-cluster-type.png":::

1. A **kiszolgáló helyeinek kiválasztása** területen válasszon egyet a következők közül:

    - **Az egyik helyen lévő összes kiszolgáló**
    - **Kiszolgálók két helyen** (a kifeszített fürthöz)

1. Ha végzett, kattintson a **Létrehozás** gombra. Ekkor megjelenik a fürt létrehozása varázsló, az alábbi ábrán látható módon.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Fürt létrehozása varázsló – első lépések" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>1. lépés: első lépések

A varázsló 1. lépése végigvezeti az összes előfeltétel teljesítésének biztosításán, a kiszolgálói csomópontok hozzáadásán, a szükséges funkciók telepítésén, majd az egyes kiszolgálók újraindításának lépésein, ha szükséges.

1. Tekintse át a **1,1. Ellenőrizze a varázslóban felsorolt előfeltételeket** , és győződjön meg arról, hogy minden kiszolgáló-csomópont fürtre kész. Amikor végzett, kattintson a **Tovább** gombra.
1. A **1,2-kiszolgálók hozzáadásához** adja meg a fiókja felhasználónevét és jelszavát, majd kattintson a **tovább** gombra. Ennek a fióknak a helyi Rendszergazdák csoport tagjának kell lennie az egyes kiszolgálókon.
1. Adja meg a hozzáadni kívánt első kiszolgáló nevét, majd kattintson a **Hozzáadás** gombra.
1. Ismételje meg a 3. lépést minden olyan kiszolgáló esetében, amely a fürt részét képezi majd. Amikor végzett, kattintson a **Tovább** gombra.
1. Ha szükséges, a **1,3 csatlakozás tartományhoz** területen válassza ki azt a tartományt, amelyhez csatlakoztatni kívánja a kiszolgálókat és a használni kívánt fiókot. Igény szerint átnevezheti a kiszolgálókat, ha szeretné. Ezután kattintson a **Tovább** gombra.
1. Az **1,4-es szolgáltatások telepítése** után szükség szerint tekintse át és adja hozzá a szolgáltatásokat. Amikor végzett, kattintson a **Tovább** gombra.

    A varázsló a következő szükséges szolgáltatásokat telepíti:

    - BitLocker
    - Adatközpont-áthidalás (RoCEv2 hálózati adapterek esetében)
    - Feladatátvételi fürtszolgáltatás
    - Fájlkiszolgáló
    - FS – adatmásolási modul
    - Hyper-V
    - RSAT-AD-PowerShell modul
    - Storage-replika (a kiterjesztett fürtökhöz telepítve)

1. Az operációs rendszer frissítéseinek telepítéséhez a **1,5**-es frissítések telepítéséhez kattintson a **frissítések telepítése** szükség szerint lehetőségre. Ha kész van, kattintson a **Next** (Tovább) gombra.
1. A **1,6-es hardveres frissítések telepítéséhez** kattintson a **frissítések beolvasása** igény szerint lehetőségre a forgalmazói hardver frissítéseinek beszerzéséhez.
1. A frissítések hardverre való telepítéséhez kövesse a gyártó által meghatározott lépéseket. Ezek a lépések magukban foglalják a hardveren a szimmetria és a megfelelőség ellenőrzését a sikeres frissítés biztosítása érdekében. Előfordulhat, hogy néhány lépést újra kell futtatnia.
1. 1,7-es **Újraindítási kiszolgálók** esetében kattintson a **kiszolgálók újraindítása** lehetőségre, ha szükséges. Ellenőrizze, hogy minden kiszolgáló sikeresen elindult-e.

## <a name="step-2-networking"></a>2. lépés: hálózatkezelés

A varázsló 2. lépése végigvezeti a virtuális kapcsolók, a hálózati adapterek és a fürt egyéb hálózati elemeinek konfigurálásán. A RDMA (iWARP és RoCE) hálózati adapterek is támogatottak.

További információ a RDMA és a Hyper-V gazdagép hálózatkezeléséről Azure Stack HCI esetében: a [gazdagép hálózati követelményei](../concepts/host-network-requirements.md).

> [!NOTE]
> Ha a hálózat vagy a virtuális kapcsoló lépései során felsorolt hibákat látja, válassza az **alkalmaz és tesztelés** lehetőséget.

1. Válassza a **Tovább: Hálózatkezelés** lehetőséget.
1. A **2,1-es hálózati adapterek vizsgálatakor** várjon, amíg a zöld jelölőnégyzetek megjelennek az egyes adapterek mellett, majd válassza a **tovább** lehetőséget.

1. Az **2,2-es felügyeleti adapterek kiválasztása** lapon válasszon ki egy vagy két, az egyes kiszolgálókhoz használandó felügyeleti adaptert. Legalább az egyik adaptert ki kell választani a felügyeleti célokra, mivel a varázsló legalább egy dedikált fizikai hálózati adaptert igényel a fürtözés kezeléséhez.  Miután kijelölt egy adaptert a felügyelethez, a rendszer kizárja a varázsló többi munkafolyamatát.

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="Fürt létrehozása varázsló – felügyeleti adapterek kiválasztása" lightbox="media/cluster/create-cluster-management-adapters.png":::

    A felügyeleti adapterek két konfigurációs lehetőséggel rendelkeznek:

    - **Egy fizikai hálózati adapter felügyelethez**. Ehhez a beállításhoz a DHCP vagy a statikus IP-cím hozzárendelés is támogatott.

    - **Két fizikai hálózati adapter a felügyelethez**. Az összevont adapterek összevonásakor a rendszer csak a statikus IP-címek hozzárendelését támogatja. Ha a kiválasztott adapterek DHCP-címzést használnak (akár egyet, akár mindkettőt), a DHCP IP-címei statikus IP-címekre lesznek konvertálva a virtuális kapcsoló létrehozása előtt.

    Az összevont adapterek használatával egyetlen kapcsolatban áll több kapcsolóval, de csak egyetlen IP-címet használ. A terheléselosztás elérhetővé válik, és a hibatűrés azonnali, a DNS-rekordok frissítésére való várakozás helyett.

    Most végezze el a következőket az egyes kiszolgálókon:

    - Jelölje be a **Leírás** jelölőnégyzetet. Vegye figyelembe, hogy az összes adapter ki van választva, és a varázsló javaslatot tehet Önnek.
    - Törölje a jelölőnégyzeteket azokhoz az adapterekhez, amelyeket nem kíván használni a fürtszolgáltatáshoz.

    > [!NOTE]
    > 1 GB-os adaptereket felügyeleti adapterként használhat, de javasoljuk, hogy 10 GB-nyi vagy gyorsabb adaptert használjon a tárolási és számítási feladatok (VM) forgalmának tárolásához.

1. A módosítások elvégzése után kattintson az **alkalmaz és a tesztelés** elemre.
1. A **hálózatok meghatározása** területen győződjön meg arról, hogy minden egyes kiszolgáló hálózati adaptere egyedi statikus IP-címmel, alhálózati maszkkal és VLAN-azonosítóval rendelkezik. Vigye az egérmutatót az egyes táblázatos elemek fölé, és szükség szerint adja meg vagy módosítsa az értékeket. Ha elkészült, kattintson **az alkalmaz és tesztelés** elemre.

    > [!NOTE]
    > A fürt VLAN-azonosító konfigurációjának támogatásához az összes kiszolgálón lévő összes hálózati adapternek támogatnia kell a VLANID tulajdonságot.

1. Várjon, amíg az **állapot** oszlop **megjelenik az** egyes kiszolgálókon, majd kattintson a **tovább** gombra. Ez a lépés ellenőrzi az összes adapter közötti hálózati kapcsolatot ugyanazzal az alhálózattal és VLAN-AZONOSÍTÓval. A megadott IP-címeket a rendszer a fizikai adapterről a virtuális adapterekre helyezi át, ha a következő lépésben a virtuális kapcsolók jönnek létre. A konfigurált adapterek számától függően több percet is igénybe vehet.

1. A **2,3 virtuális kapcsoló** alatt válasszon a következő lehetőségek közül a megfelelő módon. Attól függően, hogy hány hálózati adapter van, nem minden beállítás érhető el:

    - **Virtuális kapcsoló létrehozásának kihagyása** – válassza ki, hogy később szeretné-e beállítani a virtuális kapcsolókat.
    - **Hozzon létre egy virtuális kapcsolót a számításhoz és a tároláshoz együtt** – válassza ki, hogy ugyanazt a virtuális kapcsolót szeretné-e használni a virtuális gépekhez és a közvetlen tárolóhelyek. Ez a "konvergens" lehetőség.
    - **Csak számításhoz hozzon létre egy virtuális kapcsolót** – válassza ki, hogy csak a virtuális gépek virtuális kapcsolóját kívánja használni.
    - **Hozzon létre két virtuális kapcsolót** – válassza ki, hogy a virtuális gépekhez és a közvetlen tárolóhelyekhoz szeretne-e dedikált virtuális kapcsolót használni.

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="Fürt létrehozása varázsló – virtuális kapcsolók" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    A következő táblázat bemutatja, hogy mely virtuális kapcsolók támogatottak és engedélyezettek a különböző hálózati adapterek konfigurációjában:

    | Beállítás | 1-2 adapter | 3 + adapter | összevont adapterek |
    | :------------- | :--------- |:--------| :---------|
    | egyetlen kapcsoló (számítás + tárolás) | engedélyezve | engedélyezve  | nem támogatott |
    | egyetlen kapcsoló (csak számítás) | nem támogatott| engedélyezve | engedélyezve |
    | két kapcsoló | nem támogatott | engedélyezve | engedélyezve |

1. Szükség szerint módosítsa a kapcsoló nevét és az egyéb konfigurációs beállításokat, majd kattintson az **alkalmaz és a tesztelés** gombra. A virtuális kapcsolók létrehozása után az **állapot** oszlopnak az összes kiszolgáló számára **átadottnak** kell lennie.

1. Az **2,4** -es lépés RDMA megadása nem kötelező. Ha a RDMA-t használja, jelölje be a **RDMA konfigurálása (ajánlott)** jelölőnégyzetet, majd kattintson a **tovább** gombra.

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="Fürt létrehozása varázsló – RDMA konfigurálása" lightbox="media/cluster/create-cluster-rdma.png":::

    A sávszélesség-foglalások hozzárendelésével kapcsolatos információkért lásd a [forgalmi sávszélesség kiosztása](../concepts/host-network-requirements.md#traffic-bandwidth-allocation) szakaszt a [gazdagép hálózati követelményei](../concepts/host-network-requirements.md)című részben.

1. Válassza a **speciális** lehetőséget, majd válassza az **adatközpont-áthidaló (DCB)** jelölőnégyzetet.

1. A **fürt szívverése** területen rendeljen hozzá egy prioritási szintet és egy sávszélesség-foglalási százalékot.

1. A **tárterület** területen rendeljen prioritási szintet és sávszélesség-foglalási százalékot.

1. Ha elkészült, válassza a **módosítások alkalmazása** elemet, majd kattintson a **tovább** gombra.

1. A **2,5-es hálózatokat definiálva** megtekintheti és szerkesztheti az egyes felsorolt adapterek nevét, IP-címét, alhálózati maszkját, VLAN-azonosítóját és alapértelmezett átjáró-mezőit.

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="Fürt létrehozása varázsló – hálózatok meghatározása" lightbox="media/cluster/create-cluster-define-networks.png":::

1. Ha elkészült, kattintson **az alkalmaz és tesztelés** elemre. Előfordulhat, hogy **újra kell próbálkoznia a kapcsolat tesztelésével** , ha az állapot nem ok az adapterhez.

## <a name="step-3-clustering"></a>3. lépés: fürtözés

A varázsló 3. lépése gondoskodik arról, hogy minden eddig helyesen legyen beállítva, automatikusan két helyet állít be a kihelyezett fürtök üzembe helyezése esetén, majd ténylegesen létrehozza a fürtöt. A helyeket előre is beállíthatja Active Directoryban.

1. Válassza a **Tovább: fürtözés** lehetőséget.
1. Az 3,1-as számítógépen **ellenőrizze a fürtöt**, majd kattintson az **Érvényesítés** elemre. Az érvényesítés több percet is igénybe vehet.

    Ha megjelenik a **hitelesítő adatok biztonsági szolgáltatójának (CredSSP)** előugró ablaka, válassza az **Igen** lehetőséget a CredSSP ideiglenes engedélyezéséhez a varázsló folytatásához. Miután létrehozta a fürtöt, és a varázsló befejeződött, a biztonság fokozása érdekében letiltja a CredSSP. Ha a CredSSP kapcsolatos problémákat tapasztal, további információért tekintse meg a [CredSSP hibaelhárításával](../manage/troubleshoot-credssp.md) foglalkozó témakört.

1. Tekintse át az összes érvényesítési állapotot, töltse le a jelentést, hogy részletes információkat kapjon a hibákról, végezze el a módosításokat, majd kattintson **újra az érvényesítés** igény szerint lehetőségre. A **jelentést is letöltheti** . Szükség esetén ismételje meg a műveletet, amíg az összes érvényesítési ellenőrzés be nem fejeződik. Ha minden rendben van, kattintson a **tovább** gombra.
1. A **3,2 fürt létrehozása** területen adja meg a fürt nevét.

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="Fürt létrehozása varázsló – fürt létrehozása" lightbox="media/cluster/create-cluster.png":::

1. Az **IP-cím** területen válassza ki a használni kívánt statikus vagy dinamikus IP-címeket. Az IP-címet a következő formátumban kell megadni: *IP-cím/aktuális alhálózat hossza*. Például: 10.0.0.200/24.
1. Válassza az **Advanced** (Speciális) lehetőséget. Néhány lehetőség közül választhat:

    - **A fürt regisztrálása a DNS-sel és Active Directory**
    - **Jogosult tár hozzáadása a fürthöz (ajánlott)**

1. A **hálózatok** területen válassza ki, hogy az összes hálózatot szeretné-e **használni (ajánlott)** , vagy **adjon meg egy vagy több használni nem kívánt hálózatot**.

1. Ha elkészült, kattintson a **fürt létrehozása** elemre.

1. A **többhelyes fürtök esetében az 3,3-kiszolgálókat rendelje hozzá a kiszolgálókhoz**, nevezze el a két használni kívánt helyet.

1. Ezután rendeljen hozzá minden kiszolgálót egy helyhez. A helyek közötti replikációt később fogja beállítani. Ha elkészült, kattintson a **módosítások alkalmazása** lehetőségre.

## <a name="step-4-storage"></a>4. lépés: tároló

A varázsló 4. lépése végigvezeti a fürt Közvetlen tárolóhelyekának beállításán.

1. Válassza a **Next (tovább): Storage** lehetőséget.
1. **4,1 tiszta meghajtókon** kiválaszthatja a **meghajtók törlése** lehetőséget, ha ésszerű az üzembe helyezése.
1. Az **4,2-es meghajtókon** kattintson az **>** egyes kiszolgálók melletti ikonra annak ellenőrzéséhez, hogy a lemezek működnek és csatlakoztatva vannak-e. Ha minden rendben van, kattintson a **tovább** gombra.
1. A **tárterület ellenőrzése 4,3** kattintson a **tovább** gombra.
1. Töltse le és tekintse át az ellenőrzési jelentést. Ha minden jó, kattintson a **tovább** gombra. Ha nem, futtassa **újra az érvényesítést**.
1. Az **4,4-as közvetlen tárolóhelyek engedélyezéséhez** kattintson az **Engedélyezés** gombra.
1. Töltse le és tekintse át a jelentést. Ha minden rendben van, kattintson a **Befejezés** gombra. 
1. Válassza **az Ugrás a kapcsolatok listájára** lehetőséget.
1. Néhány perc elteltével a fürtnek a listában kell megjelennie. Válassza ki a fürt áttekintő oldalának megtekintéséhez.

Eltarthat egy ideig, amíg a fürt neve replikálódik a tartományon belül, különösen akkor, ha a munkacsoport-kiszolgálók újonnan lettek hozzáadva a Active Directoryhoz. Bár előfordulhat, hogy a fürt a Windows felügyeleti központban jelenik meg, előfordulhat, hogy még nem érhető el a kapcsolódás.

Ha a fürt feloldása egy kis idő elteltével nem sikerül, a legtöbb esetben a fürt neve helyett a kiszolgáló nevét lehet helyettesíteni.

## <a name="next-steps"></a>Következő lépések

- Regisztrálja a fürtöt az Azure-ban. Lásd: az [Azure-regisztráció kezelése](../manage/manage-azure-registration.md).
- Végezze el a fürt végső érvényesítését. Lásd: [Azure stack HCI-fürt ellenőrzése](validate.md)