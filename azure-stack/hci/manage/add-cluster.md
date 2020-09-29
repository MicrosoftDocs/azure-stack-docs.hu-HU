---
title: Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el kiszolgálói csomópontokat egy fürtből Azure Stack HCI-ben
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: 9dfdbcab43694146c4190db8ef29905626a4d597
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866644"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI-ben könnyedén hozzáadhat vagy eltávolíthat kiszolgálókat egy fürtből. Ne feledje, hogy minden egyes új fizikai kiszolgálónak szorosan meg kell egyeznie a fürtben lévő többi kiszolgálóval, amikor a processzor típusa, a memória, a meghajtók száma, valamint a meghajtók típusa és mérete is megfelel.

Amikor kiszolgálót ad hozzá vagy távolít el, a fürt érvényesítését is végre kell hajtania, hogy a fürt megfelelően működjön.

## <a name="obtain-oem-hardware"></a>OEM-hardver beszerzése

Az első lépés az új HCI-hardver beszerzése az eredeti OEM-ből. Mindig tekintse meg a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációt, amikor új kiszolgálói hardvert ad hozzá a fürtben való használathoz.

1. Helyezze az új fizikai kiszolgálót az állványba, és csatlakoztassa megfelelően.
1. Engedélyezheti a fizikai kapcsoló portjait, és módosíthatja a hozzáférés-vezérlési listákat (ACL) és a VLAN-azonosítókat.
1. Konfigurálja a megfelelő IP-címet a alaplapi felügyeleti vezérlőben (BMC), és az összes BIOS-beállítást alkalmazza OEM-utasítások alapján.
1. Alkalmazza a jelenlegi belső vezérlőprogram-alapkonfigurációt az összes összetevőre az OEM által biztosított eszközök használatával.
1. Futtassa az OEM-ellenőrző teszteket a meglévő fürtcsomópontok egységességének biztosításához.

## <a name="add-a-server-to-the-cluster"></a>Kiszolgáló hozzáadása a fürthöz

Ha a kiszolgáló megfelelően megpördült, a Windows felügyeleti központban csatlakoztassa a kiszolgálót a fürthöz.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Kiszolgáló hozzáadása képernyő" lightbox="media/manage-cluster/add-server.png":::

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

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Kiszolgáló eltávolítása párbeszédpanel" lightbox="media/manage-cluster/remove-server.png":::

1. A **Windows felügyeleti központban**válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai**területen válassza ki a fürtöt.
1. Az **eszközök**területen válassza a **kiszolgálók**elemet.
1. A **kiszolgálók**területen válassza a **leltár** lapot.
1. A **leltár** lapon válassza ki az eltávolítani kívánt kiszolgálót, majd kattintson az **Eltávolítás**gombra.
1. Ha az összes kiszolgálói meghajtót is el szeretné távolítani a tárolóból, engedélyezze ezt a jelölőnégyzetet.
1. Ellenőrizze, hogy a kiszolgáló sikeresen el lett-e távolítva a fürtből.

A kiszolgálók csomópontjának fürtből való hozzáadásakor vagy eltávolításakor győződjön meg róla, hogy később egy fürt-ellenőrzési tesztet futtat.

## <a name="next-steps"></a>Következő lépések

- A csomópont hozzáadása vagy eltávolítása után ellenőrizze a fürtöt. További információt [a fürt ellenőrzése](../deploy/validate.md) című témakörben talál.