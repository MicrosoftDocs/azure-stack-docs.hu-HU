---
title: Red Hat-alapú virtuális gép felajánlása Azure Stack hubhoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: sethmanheim
ms.topic: article
ms.date: 2/18/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 2/18/2021
ms.openlocfilehash: 4dc1280b0120c5c64dff6a273ce13ae97dbe980c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840303"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Red Hat-alapú virtuális gép felajánlása Azure Stack hubhoz

Ez a cikk azt ismerteti, hogyan készíthető elő Red Hat Enterprise Linux virtuális gép (VM) a Azure Stack hub-ban való használatra. 

## <a name="offer-a-red-hat-based-vm"></a>A Red Hat-alapú virtuális gépet ajánljuk

A Red Hat-alapú virtuális gépeket kétféleképpen kínálhatja Azure Stack központban:

- Felveheti a gépet a Azure Stack hub piactéren.
    - Red Hat Enterprise Linux lemezképek Azure Stack hub privát ajánlata. Ha ezt az ajánlatot elérhetővé szeretné tenni a **piactér-felügyelet** lapon, el kell [végeznie egy felmérést](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u).
    - További információ: [Azure stack hub Marketplace – áttekintés](azure-stack-marketplace.md).
- Hozzáadhat saját egyéni Azure Stack hubhoz, majd a piactéren is felkínálhatja a rendszerképet. 
    1. A Red Hat Cloud-hozzáférésre lesz szüksége.
    2. Az Azure-hoz és Azure Stack hub-hoz készült rendszerkép előkészítésével kapcsolatos útmutatásért lásd: [Red Hat-alapú virtuális gép előkészítése az Azure](/azure/virtual-machines/linux/redhat-create-upload-vhd)-hoz.
    3. Az Egyéni rendszerképek az Azure Stack hub piactéren való felajánlásával kapcsolatos útmutatásért lásd: [Marketplace-elem létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Következő lépések

További információ a Red Hat Enterprise Linux futtatására hitelesített hypervisorokról: [Red Hat webhely](https://access.redhat.com/certified-hypervisors).