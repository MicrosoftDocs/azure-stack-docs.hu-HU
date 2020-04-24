---
title: A Event Hubs kapacitásának megtervezése Azure Stack hub-ban
description: Megtudhatja, hogyan tervezheti meg a Azure Stack hub Event Hubs erőforrás-szolgáltatójának kapacitását.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 4cd946bf306761533f7c8d6dcba8af75a5001400
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806721"
---
# <a name="how-to-do-capacity-planning-for-event-hubs-on-azure-stack-hub"></a>A Event Hubs kapacitásának megtervezése Azure Stack hub-ban

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Kezelőként kezelheti az Azure Stack hub kapacitását az erőforrásokra vonatkozó [kvóták](azure-stack-quota-types.md) használatával. Event Hubs erőforrás-felhasználást úgy szabályozhatja, hogy kvótákat állít be Event Hubs-fürtök által használt magok maximális száma alapján. Event Hubs-fürtöket a felhasználók hoznak létre Event Hubs-erőforrás telepítésekor. A jelen cikkben leírtak szerint az erőforrás-szolgáltatóhoz is különböző erőforrás-felhasználási követelmények vonatkoznak.

## <a name="cluster-resource-consumption"></a>Fürterőforrás-felhasználás

A Event Hubs üzemelő példányok kapacitásának megismeréséhez fontos megjegyezni, hogy a felhasználók a kapacitási egységek (ke) alapján hoznak létre Event Hubs fürtöket. Event Hubs-fürt létrehozásakor nem határoznak meg CPU-alapszámot. Azonban minden egyes CU közvetlenül egy adott számú magot használ fel. 

A felhasználóknak létre kell hozniuk Event Hubs fürtöket az üzleti követelményeiknek megfelelő ke-val. A következő táblázat a kvóta-konfigurációval kapcsolatos döntését mutatja be:
- Egy 1 CU Event Hubs-fürt által használt összes mag.
- Az egyéb erőforrások, például a virtuálisgép-tárolók, a memória-és a Storage-fiókok felhasználásához szükséges hozzávetőleges kapacitás.

| | Virtuális gép típusa | Fürtcsomópontok | Magok száma virtuális gépenként/csomópont | Magok összesen | VM-tároló | Memory (Memória) | Tárfiókok |
|-|---------|-------|-------------------|-------------|------------|--------|------------------|
| **1 CU Event Hubs fürt** | [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) | 5 | 2 | 10 | 500 GiB | 70 GiB | 4 |

Az összes Event Hubs-fürt [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) virtuálisgép-típust használ a csomópontjaihoz. A D11_V2 VM-típusok 2 maggal rendelkeznek. Így 1 CU Event Hubs-fürt 5 D11_V2 virtuális gépet használ, amely 10 magot használ. A kvótához konfigurálni kívánt magok számának meghatározásakor használja az 1 CU által használt összes mag többszörösét. Ez a számítás a Event Hubs-fürtök létrehozásakor a felhasználók által használható maximális számú CU-darabszámot tükrözi. Ha például olyan kvótát szeretne konfigurálni, amely lehetővé teszi, hogy a felhasználók 2 ke kapacitású fürtöt hozzanak létre, állítsa a kvótát 20 maggal.

> [!NOTE]
> **Csak nyilvános előzetes** verzió Azure Stack hub Event Hubs elérhető verziója csak 1 CU-fürtök létrehozását támogatja. A Event Hubs általánosan elérhető verziója a CU különböző konfigurációs beállításainak támogatását is tartalmazza.

## <a name="resource-provider-resource-consumption"></a>Erőforrás-szolgáltató erőforrásainak felhasználása  

Az erőforrás-felhasználás a Event Hubs erőforrás-szolgáltató állandó, és független a felhasználók által létrehozott fürtök számával vagy méretétől. Az alábbi táblázat a Azure Stack hub Event Hubs erőforrás-szolgáltatójának alapvető kihasználtságát, valamint az erőforrás-használat további erőforrásainak megközelítőlegi felhasználását mutatja be. A Event Hubs erőforrás-szolgáltató [D2_V2](/azure-stack/user/azure-stack-vm-sizes#dv2-series) virtuálisgép-típust használ a telepítéshez.

|                                  | Virtuális gép típusa | Fürtcsomópontok | Cores | VM-tároló | Memory (Memória) | Tárfiókok |
|----------------------------------|---------|-------|-------|------------|--------|------------------|
| **Erőforrás-szolgáltató Event Hubs** | D2_V2   | 3     | 6     | 300 GiB    | 21 GiB | 2                |

> [!IMPORTANT]
> Az erőforrás-szolgáltatói felhasználás nem olyan dolog, amelyet kvóták szabályoznak. Nem kell megadnia az erőforrás-szolgáltató által a kvóták konfigurációjában használt magok használatát. Az erőforrás-szolgáltatók rendszergazdai előfizetéssel vannak telepítve. Az előfizetés nem ró erőforrás-felhasználási korlátokat az operátorokra a szükséges erőforrás-szolgáltatók telepítésekor.

## <a name="total-resource-consumption"></a>Teljes erőforrás-felhasználás

A Event Hubs szolgáltatás által felhasznált teljes kapacitás magában foglalja az erőforrás-szolgáltató erőforrás-felhasználását, valamint a felhasználó által létrehozott fürtök általi felhasználást.

Az alábbi táblázat a különböző konfigurációk Event Hubs teljes felhasználását mutatja, függetlenül attól, hogy a kvóta kezeli-e őket. Ezek a számok az erőforrás-szolgáltatón alapulnak, és Event Hubs a fent bemutatott fürtök felhasználását. Ezeket a példákat használva könnyedén kiszámíthatja a teljes Azure Stack hub-használatot más központi telepítési méreteknél.

|                                      | Cores | VM-tároló | Memory (Memória)  | Tárfiókok | Összes tárhely |
|--------------------------------------|-------|------------|---------|------------------|---------------|
| **1 – CU-fürt + erőforrás-szolgáltató** | 16    | 800 GiB    | 91 GiB  | 6                | változó\*    |
| **2 – CU-fürt + erőforrás-szolgáltató** | 26    | 1,3 TB     | 161 GiB | 10               | változó\*    |
| **4 – CU-fürt + erőforrás-szolgáltató** | 46    | 2,3 TB     | 301 GiB | 18               | változó\*    |

\*A bejövő adatblokk (üzenet/esemény) sebessége és az üzenetek megőrzése két fontos tényező, amelyek hozzájárulnak Event Hubs-fürtök által használt tárterülethez. Ha például az üzenet megőrzésének értéke 7 nap az Event hub létrehozásakor, az üzenetek pedig 1MB/s sebességgel lesznek betöltve, a használatban lévő hozzávetőleges tárterület 604 GB (1 MB x 60 másodperc x 60 perc x 24 óra X 7 nap). Ha a rendszer 7 napos adatmegőrzési sebességgel 20 MB/s sebességű üzenetet továbbít, a megközelítőleges tárterület-felhasználás 12TB. Ügyeljen arra, hogy a tárolási kapacitás felhasználásának teljes megértése érdekében vegye figyelembe a bejövő adatforgalom és a megőrzési idő értéket.

## <a name="next-steps"></a>További lépések

A telepítési folyamat megkezdése előtt végezze el a [Event Hubs telepítésének Előfeltételeit Azure stack hub-on](event-hubs-rp-prerequisites.md).





