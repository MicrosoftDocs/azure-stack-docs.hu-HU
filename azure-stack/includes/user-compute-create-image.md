---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 2/1/2021
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 8799b1e0623023d9b9b6451ba1f695c54199dfe0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245768"
---
1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

    Ha Ön egy platformot létrehozó Felhőbeli operátor, kövesse az [Add a platform-rendszerkép hozzáadása](../operator/azure-stack-add-vm-image.md#add-a-platform-image) a virtuális merevlemez hozzáadása a felügyeleti portálon vagy a rendszergazdai végpontok segítségével című témakör utasításait.

2. A felhasználói portálon válassza a **minden szolgáltatás**  >  **lemezkép**  >  **Hozzáadás** lehetőséget.

3. A **rendszerkép létrehozása**:

    1. Írja be a rendszerkép **nevét** .
    2. Válassza ki az **előfizetését**.
    3. Hozza létre vagy adja hozzá a rendszerképet egy **erőforráscsoporthoz**.
    4. Válassza ki azt a **helyet**, amelyet a ASDK régiónak is neveznek.
    5. Válasszon ki egy **operációsrendszer-típust** , amely megfelel a rendszerképnek.
    6. Válassza a **Tallózás** lehetőséget, majd keresse meg a Storage-fiókot, a tárolót és a VHD-t. Válassza a **Kiválasztás** lehetőséget
    5. Válassza ki a **fiók típusát**.
        - A **prémium szintű lemezeket (SSD** -ket) stabil állapotú meghajtók végzik, és konzisztens, kis késleltetésű teljesítményt nyújtanak. A legjobb egyensúlyt biztosítják az árak és a teljesítmény között, és ideálisak a nagy I/O-igényű alkalmazások és a termelési feladatok számára.  
        - A **standard szintű lemezek (HDD-ket)** a mágneses meghajtókon alapulnak, és olyan alkalmazásokhoz ajánlottak, amelyekben az adathozzáférés ritkán történik. A Zone-redundáns lemezeket a zóna redundáns tárolója (ZRS) támogatja, amely több zónában replikálja az adatait, és akkor is elérhető, ha egyetlen zóna nem működik.

    8. Válassza az **olvasás/írás** lehetőséget a gazdagép elfogása elemnél.
    9. Válassza a **Létrehozás** lehetőséget.

4. A rendszerkép létrehozása után a rendszerkép használatával hozzon létre egy új virtuális gépet.