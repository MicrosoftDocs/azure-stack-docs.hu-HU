---
title: Red Hat-alapú virtuális gép felajánlása Azure Stack hubhoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: sethmanheim
ms.topic: article
ms.date: 3/3/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 3/3/2021
ms.openlocfilehash: 92623d76de9f3358edc1d1ffb1cace199f2f6148
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187282"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Red Hat-alapú virtuális gép felajánlása Azure Stack hubhoz

Ez a cikk azt ismerteti, hogyan készíthető elő Red Hat Enterprise Linux virtuális gép (VM) a Azure Stack hub-ban való használatra. 

## <a name="offer-a-red-hat-based-vm"></a>A Red Hat-alapú virtuális gépet ajánljuk

A Red Hat-alapú virtuális gépeket kétféleképpen kínálhatja Azure Stack központban:

- Felveheti a gépet a Azure Stack hub piactéren.
    - Ismerje meg a [Red Hat Cloud Access program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) feltételeit. Red Hat-előfizetések engedélyezése a Cloud Accesshez a [Red Hat előfizetés-kezelőben](https://access.redhat.com/management/cloud). Be kell állítania az Azure-előfizetéseket, amelyekkel a Azure Stack hub regisztrálva van a Felhőbeli hozzáféréshez való regisztráláshoz.
    - Red Hat Enterprise Linux lemezképek Azure Stack hub privát ajánlata. Ha ezt az ajánlatot elérhetővé szeretné tenni a **piactér-felügyelet** lapon, el kell [végeznie egy felmérést](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u). A felmérés elküldése után hét munkanapot vesz igénybe, hogy megjelenjen az Azure-beli **Hozzáadás az Azure** -ban lapon a piactér felügyelete alatt.
    - További információ: [Azure stack hub Marketplace – áttekintés](azure-stack-marketplace.md).
- Hozzáadhat saját egyéni Azure Stack hubhoz, majd a piactéren is felkínálhatja a rendszerképet. 
    1. A Red Hat Cloud-hozzáférésre lesz szüksége.
    2. Az Azure-hoz és Azure Stack hub-hoz készült rendszerkép előkészítésével kapcsolatos útmutatásért lásd: [Red Hat-alapú virtuális gép előkészítése az Azure](/azure/virtual-machines/linux/redhat-create-upload-vhd)-hoz.
    3. Az Egyéni rendszerképek az Azure Stack hub piactéren való felajánlásával kapcsolatos útmutatásért lásd: [Marketplace-elem létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Következő lépések

További információ a Red Hat Enterprise Linux futtatására hitelesített hypervisorokról: [Red Hat webhely](https://access.redhat.com/certified-hypervisors).