---
title: A mennyiségi titkosítás, a deduplikálás és a tömörítés – Azure Stack HCI engedélyezése
description: Ez a témakör azt ismerteti, hogyan használható a mennyiségi titkosítás, a deduplikálás és a tömörítés a Azure Stack HCI-ben a Windows felügyeleti központon keresztül.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: 9599a4d24d93e545c964de91ad10b905c79b42a1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573496"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>A mennyiségi titkosítás, a deduplikálás és a tömörítés engedélyezése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör bemutatja, hogyan engedélyezheti a BitLocker-titkosítást a Azure Stack HCI-ben lévő köteteken a Windows felügyeleti központ használatával. Emellett azt is ismerteti, hogyan engedélyezhető a kötetek deduplikálása és tömörítése. A kötetek létrehozásáról további információt a [kötetek létrehozása](create-volumes.md)című témakörben talál.

## <a name="turn-on-bitlocker-to-protect-volumes"></a>A BitLocker bekapcsolása a kötetek védelemmel való ellátásához
A BitLocker bekapcsolása a Windows felügyeleti központban:

1. Kapcsolódjon Közvetlen tárolóhelyek fürthöz, majd az **eszközök** ablaktáblán válassza a **kötetek**lehetőséget.
1. A **kötetek** lapon válassza a **leltár** fület, majd a **választható funkciók**területen kapcsolja be a **titkosítás (BitLocker)** kapcsolót.

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="Váltás a BitLocker engedélyezéséhez":::

1. A **titkosítás (BitLocker)** előugró ablakában kattintson a **Start**gombra, majd a **titkosítás bekapcsolása** lapon adja meg hitelesítő adatait a munkafolyamat befejezéséhez.

>[!NOTE]
   > Ha a **BitLocker szolgáltatás telepítése első** előugró ablak jelenik meg, kövesse az utasításokat a szolgáltatás telepítéséhez a fürt minden kiszolgálóján, majd indítsa újra a kiszolgálókat.

## <a name="turn-on-deduplication-and-compression"></a>A deduplikálás és a tömörítés bekapcsolása
A rendszer köteten kezeli a deduplikálás és a tömörítést. A deduplikálás és a tömörítés egy post-Processing modellt használ, ami azt jelenti, hogy a megtakarítás addig nem fog megjelenni, amíg nem fut. Ha igen, akkor az összes fájlon, még az előtte lévő fájlokon is működni fog.

A deduplikálás és a tömörítés bekapcsolása egy köteten a Windows felügyeleti központban:

1. Kapcsolódjon Közvetlen tárolóhelyek fürthöz, majd az **eszközök** ablaktáblán válassza a **kötetek**lehetőséget.
1. A **kötetek** lapon válassza a **leltár** lapot.
1. A kötetek listájában válassza ki a kezelni kívánt kötet nevét.
1. A kötet részletei lapon váltson a **deduplikálás és a tömörítési** kapcsolóra.
1. A **deduplikálás engedélyezése** panelen válassza a deduplikálás módot.

    A bonyolult beállítások helyett a Windows felügyeleti központ lehetővé teszi, hogy a kész profilok közül válassza a különböző munkaterhelésekhez. Ha nem biztos a dolgában, használja az alapértelmezett beállítást.

1. Válassza a **deduplikálás engedélyezése**lehetőséget.

Tekintse meg a deduplikálás és a tömörítés bekapcsolásának gyors videóját. A videó nem jeleníti meg a titkosítást.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

A kötetek titkosításának engedélyezése csekély hatással van a kötetek teljesítményére – általában 10%-kal, de a hatás a hardvertől és a munkaterheléstől függően változhat. Az adatok deduplikálása és tömörítése is hatással van a teljesítményre – további részletekért tekintse meg az [adatok deduplikálása céljából jelölt munkaterhelések meghatározása](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads)című témakört.

<!---Add info on greyed out ReFS option? --->

Ennyi az egész! A kötetek adatainak védelme érdekében ismételje meg a szükséges műveletet.

## <a name="next-steps"></a>Következő lépések
A kapcsolódó témakörökhöz és az egyéb tárolási felügyeleti feladatokhoz lásd még:

- [Közvetlen tárolóhelyek áttekintése](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Kötetek megtervezése](../concepts/plan-volumes.md)
- [Kötetek kiterjesztése](extend-volumes.md)
- [Kötetek törlése](delete-volumes.md)