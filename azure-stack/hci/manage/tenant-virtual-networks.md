---
title: Bérlői virtuális hálózatok kezelése
description: Ez a témakör részletesen ismerteti, hogyan használhatja a Windows felügyeleti központot a Hyper-V hálózati virtualizálási (HNV) virtuális hálózatok létrehozására, frissítésére és törlésére, miután telepítette a szoftveres hálózatkezelést (SDN).
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: fb90a8f9df28ed8fd67330c42ae1e3d2f3aa445c
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562924"
---
# <a name="manage-tenant-virtual-networks"></a>Bérlői virtuális hálózatok kezelése

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server 2016

Ez a témakör részletesen ismerteti, hogyan használhatja a Windows felügyeleti központot a Hyper-V hálózati virtualizálási (HNV) virtuális hálózatok létrehozására, frissítésére és törlésére, miután telepítette a szoftveres hálózatkezelést (SDN).

A HNV segít elkülöníteni a bérlői hálózatokat, hogy minden bérlői hálózat különálló entitás legyen. Minden entitás nem rendelkezik kapcsolati lehetőséggel, kivéve, ha a nyilvános hozzáférési munkaterheléseket vagy a virtuális hálózatok közötti társítást konfigurálja.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Virtuális hálózat létrehozásához használja a következő lépéseket a Windows felügyeleti központban.

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Képernyőkép a Windows felügyeleti központ kezdőlapja panelről, amelyben a virtuális hálózat nevét és a hozzá tartozó előtagot kell létrehoznia." lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyen létre szeretné hozni a virtuális hálózatot.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. A **virtuális hálózatok** területen válassza a **leltár** fület, majd válassza az **új** lehetőséget.
1. A **Virtual Networks (virtuális hálózatok** ) ablaktáblán adja meg a virtuális hálózat nevét.
1. A **címek előtagjai** területen válassza a **Hozzáadás** lehetőséget, majd írjon be egy előtagot az osztály nélküli TARTOMÁNYOK közötti útválasztás (CIDR) jelöléssel. Opcionálisan hozzáadhat további címek előtagjait is.
1. Az **alhálózatok** területen válassza a **Hozzáadás** elemet, adja meg az alhálózat nevét, majd adja meg a CIDR jelölését.

   >[!NOTE]
   > Az alhálózati címek előtagjának a virtuális hálózat **előtagjaiban** megadott címtartomány-előtagon belül kell lennie.

1. Válassza a **Küldés** lehetőséget, vagy további alhálózatokat, majd kattintson a **Submit (Küldés**) lehetőségre.
1. A **Virtual Networks (virtuális hálózatok** ) listában ellenőrizze, hogy a virtuális hálózat állapota **kifogástalan**-e.

## <a name="get-a-list-of-virtual-networks"></a>Virtuális hálózatok listájának beolvasása
Egyszerűen megtekintheti a fürtben található összes virtuális hálózatot.

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a virtuális hálózatok listáját jeleníti meg." lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyen meg szeretné tekinteni a virtuális hálózatokat.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. A **leltár** lap felsorolja a fürtön elérhető összes virtuális hálózatot, valamint parancsokat biztosít az egyes virtuális hálózatok kezeléséhez. A következőket teheti:
    - Megtekintheti a virtuális hálózatok listáját.
    - Megtekintheti a virtuális hálózati beállításokat, az egyes virtuális hálózatok állapotát, valamint az egyes virtuális hálózatokhoz csatlakoztatott virtuális gépek számát.
    - Virtuális hálózat beállításainak módosítása
    - Virtuális hálózat törlése.

## <a name="view-virtual-network-details"></a>Virtuális hálózat adatainak megtekintése
Egy adott virtuális hálózat részletes információit a dedikált oldaláról tekintheti meg.

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely egy virtuális hálózat részletes nézetét jeleníti meg." lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. Válassza a **leltár** fület, majd válassza ki azt a virtuális hálózatot, amelyen meg szeretné tekinteni a részleteket. A következő oldalon a következőket teheti:
    - A virtuális hálózat **kiépítési állapotának** megtekintése (sikeres, sikertelen).
    - A virtuális hálózat **konfigurációs állapotának** megtekintése (kifogástalan, hiba, figyelmeztetés, ismeretlen).
    - Tekintse meg a virtuális hálózat mögöttes **logikai hálózatát** .
    - Megtekintheti a virtuális hálózat **címterület-területét** .
    - Vegyen fel új alhálózatokat, törölje a meglévő alhálózatokat, és módosítsa a virtuális hálózati alhálózat beállításait.
    - Virtuális **gépek kapcsolatainak** megtekintése a virtuális hálózaton.

## <a name="change-virtual-network-settings"></a>Virtuális hálózati beállítások módosítása
Frissítheti a virtuális hálózati címek előtagjait, felügyelheti a virtuális hálózatokat, és konfigurálhat egy Border Gateway Protocol (BGP) útválasztót és társat a virtuális hálózathoz.

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="A virtuális hálózat beállítások nézetét megjelenítő Windows felügyeleti központ képernyőképe." lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. Válassza a **leltár** lapot, válasszon ki egy virtuális hálózatot, majd válassza a **Beállítások** lehetőséget.
1. Az **általános** lapon a következőket teheti:
    - Távolítsa el a meglévő címek előtagjait, vagy vegyen fel újakat.
    - Konfigurálja a társítást egy másik virtuális hálózattal.
    - Adjon hozzá egy BGP-útválasztót a virtuális hálózathoz. Ehhez meg kell adnia a BGP-útválasztó nevét és az autonóm System number (ASN) számot.
    - Adjon hozzá egy vagy több BGP-társat a BGP-útválasztóhoz. Ehhez meg kell adnia az egyes BGP-társak nevét, valamint az egyes BGP-társak ASN-számát.

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése
Ha már nincs szüksége rá, törölheti a virtuális hálózatot.

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a virtuális hálózat törlése megerősítési kérést jeleníti meg." lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. Válassza a **leltár** lapot, válasszon ki egy virtuális hálózatot, majd válassza a **Törlés** lehetőséget.
1. A **virtuális hálózat törlése** megerősítő kérdésnél válassza a **Törlés** lehetőséget.
1. A virtuális hálózatok keresési mező mellett válassza a **frissítés** lehetőséget a virtuális hálózat törlésének biztosításához.

## <a name="next-steps"></a>Következő lépések
További információért lásd még:
- [Szoftveresen definiált hálózatkezelés (SDN) Azure Stack HCI-ben](../concepts/software-defined-networking.md)
