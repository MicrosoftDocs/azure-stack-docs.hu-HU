---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 3f8381940d7482b82ce36fd995cfac0929545f6c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867324"
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
    9. Kattintson a **Létrehozás** gombra.

4. A rendszerkép létrehozása után a rendszerkép használatával hozzon létre egy új virtuális gépet.