---
title: A kiterjesztett fürtök áttekintése
description: További információ a kifeszített fürtökről
author: v-dasis
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 2a09d459a44171a864729fd4163197de35995f2e
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737929"
---
# <a name="stretched-clusters-overview"></a>A kiterjesztett fürtök áttekintése

> A következőre vonatkozik Azure Stack HCI, Version v20H2

Egy Azure Stack HCI-alapú kifeszített fürt megoldás a vész-helyreállításhoz automatikus feladatátvételt biztosít a éles környezet helyreállításához, és nincs szükség manuális beavatkozásra. A Storage-replika biztosítja a kötetek replikálását a helyek között a vész-helyreállítás érdekében, és minden kiszolgáló szinkronizálva marad.

A Storage-replika a szinkron és aszinkron replikációt is támogatja:

- A szinkron replikáció az alacsony késésű hálózatban lévő helyek között az összeomlás-konzisztens kötetekkel biztosítja az adatvesztést, így biztosítva, hogy a hiba során a fájlrendszer szintjén nulla adatvesztés történjen.
- Az aszinkrón replikáció nagyvárosi tartományokon kívüli telephelyek között végez adattürközést magasabb késleltetésű hálózati kapcsolatokon, de nem garantálja, hogy meghibásodás esetén minkét telephely az adatok azonos példányával rendelkezik.

>[!NOTE]
> Az aszinkron replikációhoz a feladatátvétel után manuálisan kell a másik helyhez adni a célként megadott köteteket. További információ: [aszinkron replikáció](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview#asynchronous-replication).

Kétféle kiterjesztett fürt, aktív-passzív és aktív – aktív. Beállíthatja az aktív-passzív helyek replikálását, ahol a replikáció elsődleges helye és iránya. Aktív-aktív replikáció: a replikáció kétirányú lehet mindkét helyről. Ez a cikk csak az aktív/passzív konfigurációt ismerteti.

Egyszerű feltételek esetén az *aktív* hely az, amely erőforrásokkal rendelkezik, és olyan szerepköröket és munkaterheléseket biztosít, amelyekkel az ügyfelek csatlakozhatnak. A *passzív* hely olyan, amely nem biztosít semmilyen szerepkört vagy munkaterhelést az ügyfelek számára, és az aktív helyről a vész-helyreállításra vár feladatátvételre.

A helyek lehetnek két különböző állapotban, különböző városokban, különböző szintekben vagy különböző helyiségekben. A két helyet használó többhelyes fürtök vész-helyreállítást és üzletmenet-folytonosságot biztosítanak, ha egy hely áramszünet vagy meghibásodás miatt csökkenhet.

Szánjon néhány percet a videó megtekintésére a kifelé irányuló fürtözéshez Azure Stack HCI használatával:
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>Aktív – passzív kifeszített fürt

A következő ábrán az 1. hely látható, amely az aktív hely a 2. helyre történő replikálással, egyirányú replikációval.

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="Aktív/passzív kifeszített fürt forgatókönyve"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>Aktív-aktív, kifeszített fürt

A következő ábrán az 1. és a 2. hely aktív helyként jelenik meg, kétirányú replikálással a másik helyre.

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="Aktív/aktív kiterjesztett fürt forgatókönyve" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>További lépések

- További információ a Storage-replikáról. Lásd: a [Storage-replika áttekintése](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview).
- További információ a Storage-replika használatáról. Lásd: [Hyper-V feladatátvevő fürt vagy fájlkiszolgáló konfigurálása általános célú fürthöz](https://docs.microsoft.com/windows-server/storage/storage-replica/stretch-cluster-replication-using-shared-storage#configure-a-hyper-v-failover-cluster-or-a-file-server-for-a-general-use-cluster).
- További információ a kifeszített fürtökhöz kapcsolódó hardverekről és egyéb követelményekről. Lásd: [rendszerkövetelmények](system-requirements.md).
- Megtudhatja, hogyan helyezhet üzembe egy kiterjesztett fürtöt a Windows felügyeleti központban. Lásd: [fürtök létrehozása a Windows felügyeleti központtal](../deploy/create-cluster.md).
- Megtudhatja, hogyan helyezhet üzembe egy kiterjesztett fürtöt a PowerShell használatával. Lásd: [fürt létrehozása a PowerShell használatával](../deploy/create-cluster-powershell.md).
- Megtudhatja, hogyan hozhat létre köteteket, és hogyan állíthatja be a replikálást a kiterjesztett fürtök számára. Lásd: [kötetek létrehozása és replikáció beállítása a kifeszített fürtökhöz](../manage/create-stretched-volumes.md).
