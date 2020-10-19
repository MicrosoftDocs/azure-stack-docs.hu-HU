---
title: Azure Stack HCI-fürt létrehozása a Windows felügyeleti központtal
description: Megtudhatja, hogyan hozhat létre kiszolgálófürt Azure Stack HCI-hez a Windows felügyeleti központtal
author: v-dasis
ms.topic: how-to
ms.date: 10/17/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 927a57097eff9890dc8c546be9914e70dad5ec3c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179528"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Azure Stack HCI-fürt létrehozása a Windows felügyeleti központtal

> A következőre vonatkozik Azure Stack HCI, Version v20H2

Ebből a cikkből megtudhatja, hogyan használhatja a Windows felügyeleti központot olyan Azure Stack HCI hiperkonvergens-fürt létrehozásához, amely Közvetlen tárolóhelyekt használ. A Windows felügyeleti központban a fürt létrehozása varázsló a nagy mennyiségű felemelést végzi el Önnek. Ha inkább a PowerShell-lel szeretné elvégezni, tekintse meg [az Azure stack HCI-fürt létrehozása a PowerShell használatával](create-cluster-powershell.md)című témakört. A PowerShell-cikk emellett jó információforrás a varázsló csuklyáján és hibaelhárítási célokra szolgáló információkkal.

Két fürt típusának létrehozása közül választhat:

- Standard fürt legalább két, egyetlen helyen található kiszolgáló-csomóponttal.
- A kiterjesztett fürt legalább négy olyan kiszolgáló-csomóponttal rendelkezik, amely két helyen található, és legalább két csomóponttal rendelkezik.

További információ a kifeszített fürtökről: a [kiterjesztett fürtök áttekintése](../concepts/stretched-clusters.md).

Ha érdekli a Azure Stack HCI kipróbálása, de korlátozott vagy nem rendelkezik tartalék hardverrel, tekintse meg a [Azure stack HCI-értékelési útmutatót](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), ahol áttekintheti a Azure stack HCI-t a beágyazott virtualizálás használatával, akár az Azure-ban, akár a helyszínen, akár egyetlen fizikai rendszeren.

## <a name="before-you-run-the-wizard"></a>A varázsló futtatása előtt

A fürt létrehozása varázsló futtatása előtt győződjön meg róla, hogy:

- A [Azure stack HCI üzembe helyezése előtt](before-you-start.md)olvassa el a hardver-és egyéb követelményeket.
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

OK, kezdjük:

1. A Windows felügyeleti központban a **minden kapcsolat**területen kattintson a **Hozzáadás**elemre.
1. Az **erőforrások hozzáadása** panelen, a **Windows Server-fürt**területen válassza az **új létrehozása**lehetőséget.
1. A **fürt típusának kiválasztása**területen válassza a **Azure stack HCI**lehetőséget.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Fürt létrehozása varázsló – HCI lehetőség" lightbox="media/cluster/create-cluster-type.png":::

1. A **kiszolgáló helyeinek kiválasztása**területen válasszon egyet a következők közül:

    - **Az egyik helyen lévő összes kiszolgáló**
    - **Kiszolgálók két helyen** (a kifeszített fürthöz)

1. Ha végzett, kattintson a **Létrehozás** gombra. Ekkor megjelenik a fürt létrehozása varázsló, az alábbi ábrán látható módon.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Fürt létrehozása varázsló – HCI lehetőség" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>1. lépés: első lépések

A varázsló 1. lépése végigvezeti az összes előfeltétel teljesítésének biztosításán, a kiszolgálói csomópontok hozzáadásán, a szükséges funkciók telepítésén, majd az egyes kiszolgálók újraindításának lépésein, ha szükséges.

1. Tekintse át a varázslóban felsorolt előfeltételeket, és győződjön meg arról, hogy az egyes kiszolgálói csomópontok fürtre készek. Amikor végzett, kattintson a **Tovább**gombra.
1. A **kiszolgálók hozzáadása a fürthöz** lapon adja meg a fiókja felhasználónevét és jelszavát, majd kattintson a **tovább**gombra. Ennek a fióknak a helyi Rendszergazdák csoport tagjának kell lennie az egyes kiszolgálókon.
1. Adja meg a hozzáadni kívánt első kiszolgáló nevét, majd kattintson a **Hozzáadás**gombra.
1. Ismételje meg a 3. lépést minden olyan kiszolgáló esetében, amely a fürt részét képezi majd. Amikor végzett, kattintson a **Tovább**gombra.
1. Ha szükséges, a **Csatlakozás a kiszolgálókhoz tartományhoz** lapon válassza ki a tartományt és egy fiókot a kiszolgálók tartományhoz való csatlakoztatásához. Ezután tetszés szerint nevezze át a kiszolgálókat a további felhasználóbarát nevekre, és kattintson a **tovább**gombra.
1. Kattintson a **szolgáltatások telepítése**elemre. Amikor végzett, kattintson a **Tovább**gombra.

    A varázsló a következő szükséges szolgáltatásokat telepíti:

    - BitLocker
    - Adatközpont-áthidalás (RoCEv2 hálózati adapterek esetében)
    - Feladatátvételi fürtszolgáltatás
    - Fájlkiszolgáló
    - FS – adatmásolási modul
    - Hyper-V
    - RSAT-AD-PowerShell modul
    - Storage-replika (csak a kifeszített fürtök esetében van telepítve)

1. Ha szükséges, **telepítse a frissítéseket**, majd kattintson a **frissítések telepítése**elemre. Ha kész van, kattintson a **Next** (Tovább) gombra.
1. Ha szükség van a **megoldás frissítéseire**, kattintson a **bővítmény telepítése**elemre. Ha kész van, kattintson a **Next** (Tovább) gombra.
1. Ha szükséges, kattintson a **kiszolgálók újraindítása**elemre. Ellenőrizze, hogy minden kiszolgáló sikeresen elindult-e.

## <a name="step-2-networking"></a>2. lépés: hálózatkezelés

A varázsló 2. lépése végigvezeti a virtuális kapcsolók és a fürt egyéb hálózati elemeinek konfigurálásán.

> [!NOTE]
> Ha a hálózat vagy a virtuális kapcsoló lépései során észlelt hibákat látja, próbálja meg újból az **alkalmaz és a teszt** elemre kattintva.

1. Válassza a **Tovább: Hálózatkezelés** lehetőséget.
1. **A hálózati adapterek ellenőrzése**alatt várjon, amíg a zöld jelölőnégyzetek megjelennek az egyes adapterek mellett, majd válassza a **tovább**lehetőséget.

1. A **felügyeleti adapterek kiválasztása**lapon válasszon ki egy vagy két, az egyes kiszolgálókhoz használandó felügyeleti adaptert. Legalább az egyik adaptert ki kell választani a felügyeleti célokra, mivel a varázsló legalább egy dedikált fizikai hálózati adaptert igényel a fürtözés kezeléséhez.  Miután kijelölt egy adaptert a felügyelethez, a rendszer kizárja a varázsló többi munkafolyamatát.

    A felügyeleti adapterek két konfigurációs lehetőséggel rendelkeznek:

    - **Egy fizikai hálózati adapter felügyelethez**. Ehhez a beállításhoz a DHCP vagy a statikus IP-cím hozzárendelés is támogatott.

    - **Két fizikai hálózati adapter a felügyelethez**. Az összevont adapterek összevonásakor a rendszer csak a statikus IP-címek hozzárendelését támogatja. Ha a kiválasztott adapterek DHCP-címzést használnak (akár egyet, akár mindkettőt), a rendszer a DHCP IP-címet statikus IP-címekre konvertálja a virtuális kapcsoló létrehozása előtt.

    Az összevont adapterek használatával egyetlen kapcsolatban áll több kapcsolóval, de csak egyetlen IP-címet használ. A terheléselosztás elérhetővé válik, és a hibatűrés azonnali, a DNS-rekordok frissítésére való várakozás helyett.

    Most végezze el a következőket az egyes kiszolgálókon:

    - Jelölje be a **Leírás** jelölőnégyzetet. Vegye figyelembe, hogy az összes adapter ki van választva, és a varázsló javaslatot tehet Önnek.
    - Törölje a jelölőnégyzeteket azokhoz az adapterekhez, amelyeket nem kíván használni a fürtszolgáltatáshoz.

    > [!NOTE]
    > 1 GB-os adaptereket felügyeleti adapterként használhat, de javasoljuk, hogy 10 GB-nyi vagy gyorsabb adaptert használjon a tárolási és számítási feladatok (VM) forgalmának tárolásához.

1. A módosítások elvégzése után kattintson az **alkalmaz és a tesztelés**elemre.
1. A **hálózatok meghatározása**területen győződjön meg arról, hogy minden egyes kiszolgáló hálózati adaptere egyedi statikus IP-címmel, alhálózati maszkkal és VLAN-azonosítóval rendelkezik. Vigye az egérmutatót az egyes táblázatos elemek fölé, és szükség szerint adja meg vagy módosítsa az értékeket. Ha elkészült, kattintson **az alkalmaz és tesztelés**elemre.

    > [!NOTE]
    > A fürt VLAN-azonosító konfigurációjának támogatásához az összes kiszolgálón lévő összes hálózati kártyának támogatnia kell a VLANID tulajdonságot.

1. Várjon, amíg az **állapot** oszlop **megjelenik az** egyes kiszolgálókon, majd kattintson a **tovább**gombra. Ez a lépés ellenőrzi az összes adapter közötti hálózati kapcsolatot ugyanazzal az alhálózattal és VLAN-AZONOSÍTÓval. A megadott IP-címeket a rendszer a fizikai adapterről a virtuális adapterekre helyezi át, ha a következő lépésben a virtuális kapcsolók jönnek létre. A konfigurált adapterek számától függően több percet is igénybe vehet.

1. A **virtuális kapcsoló**alatt válasszon a következő lehetőségek közül a megfelelő módon. Attól függően, hogy hány adapter van jelen, nem minden beállítás jelenik meg:

    - **Virtuális kapcsoló létrehozásának kihagyása**
    - **Hozzon létre egy virtuális kapcsolót a számításhoz és a tároláshoz**
    - **Csak számítási feladatokhoz hozzon létre egy virtuális kapcsolót**
    - **Két virtuális kapcsoló létrehozása**

    > [!NOTE]
    > Ha az SDN hálózati vezérlőjét szeretné telepíteni (a varázsló **5. lépés: Sdn** ), szüksége lesz egy virtuális kapcsolóra. Tehát ha nem szeretne létrehozni egy virtuális kapcsolót, és nem hoz létre egyet a varázslón kívül, a varázsló nem helyezi üzembe a hálózati vezérlőt.

    A következő táblázat bemutatja, hogy mely virtuális kapcsolók támogatottak és engedélyezettek a különböző hálózati adapterek konfigurációjában:

    | Beállítás | 1-2 adapter | 3 + adapter | összevont adapterek |
    | :------------- | :--------- |:--------| :---------|
    | egyetlen kapcsoló (számítás + tárolás) | engedélyezve | engedélyezve  | nem támogatott |
    | egyetlen kapcsoló (csak számítás) | nem támogatott| engedélyezve | engedélyezve |
    | két kapcsoló | nem támogatott | engedélyezve | engedélyezve |

1. Szükség szerint módosítsa a kapcsoló nevét és az egyéb konfigurációs beállításokat, majd kattintson az **alkalmaz és a tesztelés**gombra. A virtuális kapcsolók létrehozása után az **állapot** oszlopnak az összes kiszolgáló számára **átadottnak** kell lennie.

## <a name="step-3-clustering"></a>3. lépés: fürtözés

A varázsló 3. lépése gondoskodik arról, hogy minden eddig helyesen legyen beállítva, automatikusan két helyet állít be a kihelyezett fürtök üzembe helyezése esetén, majd ténylegesen létrehozza a fürtöt. A helyeket előre is beállíthatja Active Directoryban.

1. Válassza a **Tovább: fürtözés**lehetőséget.
1. **A fürt ellenőrzése**területen válassza az **Érvényesítés**lehetőséget. Az érvényesítés több percet is igénybe vehet.

    Ha megjelenik a **hitelesítő adatok biztonsági szolgáltatójának (CredSSP)** előugró ablaka, válassza az **Igen** lehetőséget a CredSSP ideiglenes engedélyezéséhez a varázsló folytatásához. Miután létrehozta a fürtöt, és a varázsló befejeződött, a biztonság fokozása érdekében letiltja a CredSSP. Ha a CredSSP kapcsolatos problémákat tapasztal, további információért tekintse meg a [CredSSP hibaelhárításával](../manage/troubleshoot-credssp.md) foglalkozó témakört.

1. Tekintse át az összes érvényesítési állapotot, töltse le a jelentést, hogy részletes információkat kapjon a hibákról, végezze el a módosításokat, majd kattintson **újra az érvényesítés** igény szerint lehetőségre. Szükség esetén ismételje meg a műveletet, amíg az összes érvényesítési ellenőrzés be nem fejeződik.
1. **A fürt létrehozása**területen adja meg a fürt nevét.
1. A **hálózatok**területen válassza ki az előnyben részesített konfigurációt.
1. Az **IP-címek**területen válassza ki a használni kívánt dinamikus vagy statikus IP-címeket.
1. Ha elkészült, kattintson a **fürt létrehozása**elemre.

1. A kiosztott fürtök esetében a **kiszolgálók társítása helyekhez**területen nevezze el a két használni kívánt helyet.

1. Ezután rendeljen hozzá minden kiszolgálót egy helyhez. A helyek közötti replikációt később fogja beállítani. Ha elkészült, kattintson az **alkalmaz**gombra.

## <a name="step-4-storage"></a>4. lépés: tároló

A varázsló 4. lépése végigvezeti a fürt Közvetlen tárolóhelyekának beállításán.

1. Válassza a **Next (tovább): Storage**lehetőséget.
1. A **meghajtók ellenőrzése**területen kattintson az **>** egyes kiszolgálók melletti ikonra annak ellenőrzéséhez, hogy a lemezek működnek és csatlakoztatva vannak-e, majd kattintson a **tovább**gombra.
1. A meghajtók **tisztítása**területen kattintson a **tiszta meghajtók** elemre az adatmeghajtók ürítéséhez. Ha elkészült, kattintson a **tovább**gombra.
1. A **tároló ellenőrzése**területen kattintson a **tovább**gombra.
1. Tekintse át az érvényesítési eredményeket. Ha minden jó, kattintson a **tovább**gombra.
1. A **közvetlen tárolóhelyek engedélyezése**területen kattintson az **Engedélyezés**elemre.
1. Töltse le a jelentést, és tekintse át. Ha minden rendben van, kattintson a **Befejezés**gombra.

Gratulálunk, most már rendelkezik egy fürttel.

A fürt létrehozása után időbe telik a fürt nevének replikálása a tartományon belül, különösen akkor, ha a munkacsoport-kiszolgálók újonnan lettek hozzáadva a Active Directoryhoz. Bár előfordulhat, hogy a fürt a Windows felügyeleti központban jelenik meg, előfordulhat, hogy még nem érhető el a kapcsolódás.

Ha a fürt feloldása egy kis idő elteltével nem sikerül, a legtöbb esetben a fürt neve helyett a fürt nevét is helyettesítheti a fürtben.

## <a name="step-5-sdn-optional"></a>5. lépés: SDN (nem kötelező)

Ez a választható lépés végigvezeti a [szoftveresen definiált hálózatkezelés (Sdn)](../concepts/software-defined-networking.md)hálózati vezérlő összetevőjének beállításán. A hálózati vezérlő beállítása után az SDN más összetevőit is konfigurálhatja, például a szoftver Load Balancer és a RAS-átjárót.

> [!NOTE]
> Az SDN nem támogatott vagy nem érhető el a kifeszített fürtökhöz.

:::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Fürt létrehozása varázsló – HCI lehetőség" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Válassza a **Tovább: Sdn**lehetőséget.
1. A **gazdagép**területen adja meg a hálózati vezérlő nevét.
1. A Azure Stack HCI VHD-fájl elérési útjának megadása. Gyorsabb keresés a **tallózással** .
1. A hálózati vezérlő számára dedikált virtuális gépek számának megadása. A magas rendelkezésre állás érdekében három – öt virtuális gép ajánlott.
1. A **hálózat**alatt adja meg a VLAN-azonosítót.
1. A **VM-hálózat címzése**beállításnál válassza a **DHCP** vagy a **statikus**lehetőséget.
1. Ha a **DHCP**lehetőséget választotta, adja meg a hálózati vezérlő virtuális gépek nevét és IP-címét.
1. Ha a **statikus**lehetőséget választotta, tegye a következőket:
    1. Alhálózati előtagot kell megadni.
    1. Határozza meg az alapértelmezett átjárót.
    1. Egy vagy több DNS-kiszolgálót kell megadnia. További DNS-kiszolgálók hozzáadásához kattintson a **Hozzáadás** gombra.
1. A **hitelesítő adatok**területen adja meg a hálózati vezérlő virtuális gépek fürt tartományhoz való csatlakoztatásához használt felhasználónevet és jelszót.
1. Adja meg a virtuális gépek helyi rendszergazdai jelszavát.
1. A **speciális**alatt adja meg a virtuális gépek elérési útját.
1. Adja meg az értékeket a **Mac-címkészlet kezdő** és a **Mac-címkészlet végéhez**.
1. Amikor végzett, kattintson a **Tovább**gombra.
1. Várjon, amíg a varázsló befejezi a feladatot. Maradjon ezen a lapon, amíg az összes végrehajtási feladat be nem fejeződik. Ezután kattintson a **Befejezés** gombra.

Ha a hálózati vezérlő üzembe helyezése sikertelen, a következő próbálkozás előtt tegye a következőket:

- Állítsa le és törölje a varázsló által létrehozott hálózati vezérlő virtuális gépeket.  

- Törölje a varázsló által létrehozott VHD-csatlakoztatási pontokat.  

- Győződjön meg arról, hogy a Hyper-V-gazdagépeken legalább 50 100 GB szabad terület van.  

## <a name="after-you-complete-the-wizard"></a>A varázsló befejezése után

A varázsló befejezése után még néhány fontos feladatot végre kell hajtania.

Az első feladat a hitelesítő adatok biztonsági támogató szolgáltató (CredSSP) protokolljának letiltása az egyes kiszolgálókon biztonsági okokból. Ne feledje, hogy a CredSSP szükséges, hogy engedélyezve legyen a varázsló. Ha a CredSSP kapcsolatos problémákat tapasztal, további információért tekintse meg a [CredSSP hibaelhárításával](../manage/troubleshoot-credssp.md) foglalkozó témakört.

1. A Windows felügyeleti központban az **összes kapcsolat**területen válassza ki az imént létrehozott fürtöt.
1. Az **eszközök**területen válassza a **kiszolgálók**elemet.
1. A jobb oldali ablaktáblában válassza ki az első kiszolgálót a fürtben.
1. Az **Áttekintés**területen válassza a **CredSSP letiltása**lehetőséget. Ekkor láthatja, hogy a piros **CREDSSP engedélyezte** a szalagcímet a felső részen.
1. Ismételje meg a 3. és a 4. lépést a fürt minden kiszolgálóján.

OK, most a következő műveleteket kell végrehajtania:

- Állítson be egy tanúsító fürtöt. Lásd: [tanúsító fürt beállítása](witness.md).
- Hozza létre a köteteket. Lásd: [kötetek létrehozása](../manage/create-volumes.md).
- A kiterjesztett fürtök esetében hozzon létre köteteket, és állítsa be a replikálást a Storage-replika használatával. Lásd: [kötetek létrehozása és replikáció beállítása a kifeszített fürtökhöz](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Következő lépések

- Regisztrálja a fürtöt az Azure-ban. Lásd: az [Azure-regisztráció kezelése](../manage/manage-azure-registration.md).
- Végezze el a fürt végső érvényesítését. Lásd: [Azure stack HCI-fürt ellenőrzése](validate.md)
- A virtuális gépek kiépítése. Lásd: [virtuális gépek kezelése Azure stack HCI-ben a Windows felügyeleti központ használatával](../manage/vm.md).
- Fürtöt a PowerShell használatával is üzembe helyezhet. Lásd: [Azure stack HCI-fürt létrehozása a PowerShell használatával](create-cluster-powershell.md).
- A hálózati vezérlőt a PowerShell használatával is üzembe helyezheti. Lásd: [hálózati vezérlő üzembe helyezése a PowerShell használatával](network-controller-powershell.md).
