---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 97f578d81458e371640574d8db287e261b37daab
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936232"
---
1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

    Ha Ön a platform lemezét létrehozó Felhőbeli operátor, kövesse a [platform lemezkép hozzáadása](../operator/azure-stack-add-vm-image.md#add-a-platform-image) a virtuális merevlemez hozzáadása a felügyeleti portálon vagy a rendszergazdai végpontokkal című témakör utasításait.

2. A felhasználói portálon válassza a **minden szolgáltatás**  >  **lemez**  >  **Hozzáadás**lehetőséget.

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
    9. Kattintson a **Létrehozás** gombra.

4. A lemez létrehozása után a lemez használatával létrehozhat egy új virtuális gépet.