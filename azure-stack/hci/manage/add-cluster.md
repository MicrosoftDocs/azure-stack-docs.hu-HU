---
title: Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el kiszolgálói csomópontokat egy fürtből Azure Stack HCI-ben
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 4d8e122c8a0b68740ecf34f8140322ee627d03e7
ms.sourcegitcommit: 91f4baa7a770b7a82f1ddccec4dbac14be530d06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83768986"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz

> A következőkre vonatkozik: Windows Server 2019

Azure Stack HCI-ben könnyedén hozzáadhat vagy eltávolíthat kiszolgálókat egy fürtből. Ne feledje, hogy minden egyes új fizikai kiszolgálónak szorosan meg kell egyeznie a fürtben lévő többi kiszolgálóval, amikor a processzor típusa, a memória, a meghajtók száma, valamint a meghajtók típusa és mérete is megfelel.

Amikor kiszolgálót ad hozzá vagy távolít el, a fürt érvényesítését is végre kell hajtania, hogy a fürt megfelelően működjön.

## <a name="obtain-server-hardware-from-your-oem"></a>Kiszolgálói hardver beszerzése az OEM-ből

Az első lépés az új HCI-hardver beszerzése az eredeti OEM-ből. Mindig tekintse meg a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációt, amikor új kiszolgálói hardvert ad hozzá a fürtben való használathoz.

1. Helyezze az új fizikai kiszolgálót az állványba, és csatlakoztassa megfelelően.
1. Engedélyezheti a fizikai kapcsoló portjait, és módosíthatja a hozzáférés-vezérlési listákat (ACL) és a VLAN-azonosítókat.
1. Konfigurálja a megfelelő IP-címet a alaplapi felügyeleti vezérlőben (BMC), és az összes BIOS-beállítást alkalmazza OEM-utasítások alapján.
1. Alkalmazza a jelenlegi belső vezérlőprogram-alapkonfigurációt az összes összetevőre az OEM által biztosított eszközök használatával.
1. Futtassa az OEM-ellenőrző teszteket a meglévő fürtcsomópontok egységességének biztosításához.

## <a name="add-the-server-to-the-cluster"></a>Kiszolgáló hozzáadása a fürthöz

Ha a kiszolgáló megfelelően megpördült, a Windows felügyeleti központban csatlakoztassa a kiszolgálót a fürthöz.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Kiszolgáló hozzáadása képernyő":::

1. A **Windows felügyeleti központban**válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai**területen válassza ki a fürtöt.
1. Az **eszközök**területen válassza a **kiszolgálók**elemet.
1. A **kiszolgálók**területen válassza a **leltár** lapot.
1. A **leltár** lapon válassza a **Hozzáadás**lehetőséget.
1. A **kiszolgáló neve**mezőben adja meg a hozzáadni kívánt kiszolgáló teljes tartománynevét, kattintson a **Hozzáadás**gombra, majd a lap alján kattintson a **Hozzáadás** gombra.
1. Győződjön meg arról, hogy a kiszolgáló sikeresen hozzá lett adva a fürthöz.

## <a name="remove-a-server-from-the-cluster"></a>Kiszolgáló eltávolítása a fürtből

A kiszolgálók fürtből való eltávolításának lépései hasonlóak a kiszolgálók fürthöz való hozzáadásához.

Ne feledje, hogy amikor eltávolít egy kiszolgálót, a kiszolgálóhoz társított összes virtuális gépet, meghajtót és munkaterhelést is el fogja távolítani.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Kiszolgáló eltávolítása párbeszédpanel":::

1. A **Windows felügyeleti központban**válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai**területen válassza ki a fürtöt.
1. Az **eszközök**területen válassza a **kiszolgálók**elemet.
1. A **kiszolgálók**területen válassza a **leltár** lapot.
1. A **leltár** lapon válassza ki az eltávolítani kívánt kiszolgálót, majd kattintson az **Eltávolítás**gombra.
1. Ha az összes kiszolgálói meghajtót is el szeretné távolítani a tárolóból, engedélyezze ezt a jelölőnégyzetet.
1. Ellenőrizze, hogy a kiszolgáló sikeresen el lett-e távolítva a fürtből.

## <a name="validate-the-cluster"></a>A fürt ellenőrzése

Amikor fürtöt ad hozzá vagy távolít el, ellenőriznie kell a fürtöt. A fürt érvényesítésének a Microsoft által támogatott hibák nélkül kell megfelelnie.

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="Fürt ellenőrzése párbeszédpanel":::

1. A **Windows felügyeleti központban**válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök**területen válassza a **kiszolgálók**elemet.
1. A **kiszolgálók**területen válassza a **leltár** fület, válassza a **továbbiak**, majd a **fürt ellenőrzése**lehetőséget.
1. Ellenőrizze, hogy az összes ellenőrzési lépés sikeres volt-e.

## <a name="next-steps"></a>További lépések

- A fürt ellenőrzésével kapcsolatos további tudnivalókért tekintse meg [a feladatátvevő fürt hardverének ellenőrzése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11))című témakört.