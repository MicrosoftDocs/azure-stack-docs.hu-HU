---
title: Ajánlat CommVault Marketplace-eleme a Azure Stack-MDC
description: CommVault üzembe helyezése Azure Stack piactérről a moduláris adatközpontok számára (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: c2f98725a93dbd471f257d8a038a989d9f892c68
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910839"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack---modular-data-center-mdc"></a>Ajánlat CommVault Marketplace-elem Azure Stack-moduláris adatközpontban (MDC)

*A következőkre vonatkozik: moduláris adatközpont, Azure Stack hub robusztus*

A CommVault támogatja a következő erőforrástípusok biztonsági mentését és visszaállítását Azure Stack hub-on:

- Virtuális gép szintjének biztonsági mentése
  - IaaS VM
  - Nem felügyelt lemezek
  - Felügyelt lemezek
  - További információ: [Virtual Machines biztonsági mentése](https://documentation.commvault.com/commvault/v11/article?p=86503.htm).

- Storage-fiók biztonsági mentése
  - Blob
  - További információ: az [Azure Blob Storage áttekintése](https://documentation.commvault.com/commvault/v11/article?p=30063.htm).

- Ügynök-alapú biztonsági mentés
  - Vendég operációs rendszer – Windows és Linux
  - Alkalmazás--SQL, MySQL
  - További információ: [Backup Agents](https://documentation.commvault.com/commvault/v11/article?p=14333.htm).

A CommVault külső gépen is üzembe helyezhetők, és a Azure Stack hub erőforrásai távolról is védhetők. Emellett lehetséges, hogy a CommVault-et virtuális készülékként telepíti Azure Stack hub-on. A CommVault-ről szóló útmutató az [Azure stack hub](https://documentation.commvault.com/commvault/v11/article?p=86486.htm)-t tartalmazó dokumentációs webhelyén érhető el. A CommVault a [Microsoft Azure képességeinek teljes listáját](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)is közzéteszi.

## <a name="deploy-from-azure-stack-hub-marketplace"></a>Üzembe helyezés Azure Stack hub piactérről

A CommVault BYOL-rendszerképet tesz közzé az Azure piactéren, és lehetővé teszi a rendszerképek begyűjtését Azure Stack hubhoz. A virtuális gépek biztonsági mentéséhez szükséges minimális verzió a Azure Stack SP16. Ha virtuális készüléket szeretne használni, frissítsen a következőre: SP16 (a legújabb [hosszú távú támogatott kiadás](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) vagy a SP17 (a legújabb mainstream kiadás).

| Felhőbeli        | Verzió | Elérhető a hírszolgáltatáshoz | Következő frissítés |
|--------------|---------|---------------------------|-------------|
| Azure – nyilvános | SP13    | Yes                       | TBD         |
| Azure gov    | SP13    | TBD                       | TBD         |

## <a name="download-from-azure-marketplace"></a>Letöltés az Azure Marketplace-ről

Azure Stack hub-operátorok az elemeket a helyi Azure Stack piactérről tölthetik le a csatlakoztatott és a leválasztott környezetekhez. Csatlakoztatott környezetben a kezelő böngészhet az Azure-ból hozzáadandó elérhető elemek listáján:

![Hozzáadás az Azure-ból](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>Manuális feltöltés és közzététel

A leválasztott környezetekben az elemet le kell tölteni az Azure-ból, majd manuálisan kell feltölteni Azure Stack hubhoz. További információkért lásd a [csatlakoztatott és a leválasztott környezetek teljes útmutatóját](../../operator/azure-stack-download-azure-marketplace-item.md).

## <a name="deployment-considerations"></a>Telepítési szempontok

- Külső üzembe helyezés Azure Stack hubhoz
- Üzembe helyezés virtuális készülékként Azure Stack hub-on
- Disk Library vs Cloud Library
- A hálózati vonal megfigyelésének megfontolása
- Előfizetés szintjének elkülönítése

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a IaaS virtuális gépek védelméről, tekintse meg a virtuális gépek védelme Azure Stack központban című témakört.
