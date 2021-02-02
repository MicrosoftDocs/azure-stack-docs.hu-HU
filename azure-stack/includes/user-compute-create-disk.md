---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 2/1/2021
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 2857a38d4e7b8195bb84e6ce84d5e4a1cb2a4c26
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245749"
---
1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

    Ha Ön a platform lemezét létrehozó Felhőbeli operátor, kövesse a [platform lemezkép hozzáadása](../operator/azure-stack-add-vm-image.md#add-a-platform-image) a virtuális merevlemez hozzáadása a felügyeleti portálon vagy a rendszergazdai végpontokkal című témakör utasításait.

2. A felhasználói portálon válassza a **minden szolgáltatás**  >  **lemez**  >  **Hozzáadás** lehetőséget.

3. A **felügyelt lemez létrehozása**:

    1. Írja be a rendszerkép **nevét** .
    2. Válassza ki az **előfizetését**.
    3. Hozza létre vagy adja hozzá a rendszerképet egy **erőforráscsoporthoz**.
    4. Válassza ki azt a **helyet**, amelyet a ASDK régiónak is neveznek.
    5. Válassza ki a **fiók típusát**.
        - A **prémium szintű lemezeket (SSD** -ket) stabil állapotú meghajtók végzik, és konzisztens, kis késleltetésű teljesítményt nyújtanak. A legjobb egyensúlyt biztosítják az árak és a teljesítmény között, és ideálisak a nagy I/O-igényű alkalmazások és a termelési feladatok számára.  
        - A **standard szintű lemezek (HDD-ket)** a mágneses meghajtókon alapulnak, és olyan alkalmazásokhoz ajánlottak, amelyekben az adathozzáférés ritkán történik. A Zone-redundáns lemezeket a zóna redundáns tárolója (ZRS) támogatja, amely több zónában replikálja az adatait, és akkor is elérhető, ha egyetlen zóna nem működik.

    6. Válassza ki a **forrás típusát** **tároló blobot** . Létrehoz egy lemezt egy blobban egy Storage-fiókban.
    7. A VHD-forráshoz válassza a következőket:
        1. Az a forrás-előfizetés, amelyben a Storage-fiók található.
        1. Válassza a **Tallózás** lehetőséget, majd keresse meg a Storage-fiókot, a tárolót és a VHD-t. Válassza a **Kiválasztás** lehetőséget
        1. Válassza ki azt az **operációsrendszer-típust** , amely megfelel a VHD-nek.
    8. Válassza ki a lemez méretét **(GIB)** , amely a virtuális merevlemez mérete vagy nagyobb.
    9. Válassza a **Létrehozás** lehetőséget.

4. A lemez létrehozása után a lemez használatával létrehozhat egy új virtuális gépet.