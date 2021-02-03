---
title: Bérlői logikai hálózatok kezelése
description: Ez a témakör részletesen ismerteti, hogyan használhatja a Windows felügyeleti központot a hálózati vezérlő üzembe helyezése után a logikai hálózatok létrehozásához, frissítéséhez és törléséhez.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: e45ca61a09bd989645998a2e8993f29176d1b9cf
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500968"
---
# <a name="manage-tenant-logical-networks"></a>Bérlői logikai hálózatok kezelése

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

Ez a témakör részletesen ismerteti, hogyan használhatja a Windows felügyeleti központot a hálózati vezérlő üzembe helyezése után a logikai hálózatok létrehozásához, frissítéséhez és törléséhez. A szoftveresen definiált hálózatkezelés (SDN) logikai hálózat egy hagyományos VLAN-alapú hálózat.

A VLAN-alapú hálózat SDN logikai hálózatként való modellezésével hálózati házirendeket alkalmazhat a hálózatokhoz csatolt munkaterhelésekre. Alkalmazhat például biztonsági hozzáférés-vezérlési listákat (ACL-eket) az SDN logikai hálózatokhoz csatolt munkaterhelésekhez. Az ACL-ek alkalmazása védi a VLAN-alapú számítási feladatokat külső és belső támadásokkal szemben.

## <a name="create-a-logical-network"></a>Logikai hálózat létrehozása
Logikai hálózat létrehozásához használja a következő lépéseket a Windows felügyeleti központban.

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="Képernyőfelvétel a Windows felügyeleti központ kezdőlapján a logikai hálózatok neve mezővel." lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyen létre szeretné hozni a logikai hálózatot.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **logikai hálózatok** elemet.
1. A **logikai hálózatok** területen válassza a **leltár** fület, majd válassza az **új** lehetőséget.
1. A **logikai hálózatok** ablaktáblán írja be a logikai hálózat nevét.
1. A **logikai alhálózat** területen válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="Képernyőfelvétel a Windows felügyeleti központ kezdőlapján a logikai alhálózat ablaktáblán." lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. A **logikai alhálózat** ablaktáblán adja meg az alhálózat nevét, majd adja meg a következő adatokat:
    1. A hálózat **VLAN-azonosítója** .
    1. Az osztály nélküli tartomány-útválasztási (CIDR) jelölésben szereplő **címek előtagja** .
    1. A hálózat **alapértelmezett átjárója** .
    1. A **DNS-** kiszolgáló címe, ha szükséges.
    1. Jelölje be a **nyilvános logikai hálózat** jelölőnégyzetet, ha a logikai hálózat a külső ügyfelek számára biztosít kapcsolatot.
1. A **logikai alhálózat IP-készletek** területén válassza a **Hozzáadás** lehetőséget, majd adja meg a következő adatokat:
    1. Logikai **IP-címkészlet neve**.
    1. A **kezdő IP-cím**.
    1. Egy **záró IP-cím**. A kezdő és a záró IP-címnek az alhálózathoz megadott cím előtagon belül kell lennie.
    1. Válassza a **Hozzáadás** elemet.
1. A **logikai alhálózat** lapon válassza a **Hozzáadás** lehetőséget.
1. A **logikai hálózatok** lapon válassza a **Küldés** lehetőséget.
1. A **logikai hálózatok** listában ellenőrizze, hogy a logikai hálózat állapota **kifogástalan**-e.

## <a name="get-a-list-of-logical-networks"></a>Logikai hálózatok listájának beolvasása
A fürt összes logikai hálózatát könnyedén megtekintheti.

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="Képernyőfelvétel a Windows felügyeleti központ kezdőlapján a logikai hálózatok leltár ablaktábláján." lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyen a logikai hálózatokat meg szeretné tekinteni.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **logikai hálózatok** elemet.
1. A **leltár** lap felsorolja a fürtön elérhető összes logikai hálózatot, és parancsokat biztosít az egyes logikai hálózatok kezeléséhez. A következőket teheti:
    - A logikai hálózatok listájának megtekintése.
    - Megtekintheti a logikai hálózat beállításait, az egyes logikai hálózatok állapotát, valamint azt, hogy minden logikai hálózat esetében engedélyezve van-e a hálózati virtualizálás. Ha a hálózati virtualizálás engedélyezve van, megtekintheti az egyes logikai hálózatokhoz társított virtuális hálózatok számát is.
    - Logikai hálózat beállításainak módosítása
    - Logikai hálózat törlése.

## <a name="view-logical-network-details"></a>Logikai hálózat adatainak megtekintése
Egy adott logikai hálózat részletes információit a dedikált oldaláról tekintheti meg.

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely egy logikai hálózat részletes nézetét jeleníti meg." lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **logikai hálózatok** elemet.
1. Válassza a **leltár** fület, majd válassza ki azt a logikai hálózatot, amelyen meg szeretné tekinteni a részleteket. A következő oldalon a következőket teheti:
    - A logikai hálózat kiépítési állapotának megtekintése (sikeres, sikertelen).
    - Annak megtekintése, hogy engedélyezve van-e a hálózati virtualizálási szolgáltatás a logikai hálózathoz.
    - Tekintse meg a logikai hálózat alhálózatait.
    - Vegyen fel új alhálózatokat, törölje a meglévő alhálózatokat, és módosítsa a logikai hálózati alhálózat beállításait.
    - Válassza ki az egyes alhálózatokat, hogy megnyissa az **alhálózat** lapját, ahol hozzáadhat, eltávolíthat és módosíthat logikai ALHÁLÓZATI IP-készleteket.
    - A logikai hálózathoz társított virtuális hálózatok és kapcsolatok megtekintése.

## <a name="change-a-logical-networks-virtualization-setting"></a>Logikai hálózat virtualizációs beállításainak módosítása
A logikai hálózatokhoz tartozó hálózati virtualizálási beállítások módosíthatók.

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a hálózati virtualizálási engedélyezése jelölőnégyzetet mutatja logikai hálózat esetén." lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **logikai hálózatok** elemet.
1. Válassza a **leltár** lapot, válassza ki a logikai hálózatot, majd válassza a **Beállítások** lehetőséget.
1. Ha a logikai hálózat felett szeretné telepíteni a virtuális hálózatokat, a logikai hálózat neve területen jelölje be a **hálózati virtualizáció engedélyezése** jelölőnégyzetet, majd válassza a **Küldés** lehetőséget.

## <a name="delete-a-logical-network"></a>Logikai hálózat törlése
Ha már nincs szüksége rá, törölheti a logikai hálózatot.

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a logikai hálózat törlésére vonatkozó megerősítési kérést jeleníti meg." lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **logikai hálózatok** elemet.
1. Válassza a **leltár** lapot, válassza ki a logikai hálózatot, majd válassza a **Törlés** lehetőséget.
1. A **Törlés** megerősítésének kérése lapon válassza az **Igen** lehetőséget.
1. A **logikai hálózatok** keresése mező mellett válassza a **frissítés** lehetőséget a logikai hálózat törlésének biztosításához.

## <a name="next-steps"></a>Következő lépések
További információért lásd még:
- [Bérlői virtuális hálózatok kezelése](tenant-virtual-networks.md)
- [Szoftveresen definiált hálózatkezelés (SDN) Azure Stack HCI-ben](../concepts/software-defined-networking.md)
