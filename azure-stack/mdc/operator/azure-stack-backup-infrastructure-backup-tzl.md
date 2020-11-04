---
title: Azure Stack adatok helyreállítása a Infrastructure Backup szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan készíthet biztonsági mentést és visszaállítást Azure Stack konfigurációs és szolgáltatási adatairól a Infrastructure Backup szolgáltatás használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: c32065beeeb446d7d8e494634bc68318e280a693
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329534"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>Azure Stack adatok helyreállítása a Infrastructure Backup szolgáltatással

*A következőkre vonatkozik: moduláris adatközpont és Azure Stack hub robusztus*

Az infrastruktúra-szolgáltatási metaadatok biztonsági mentését az Azure Stack Infrastructure Backup szolgáltatás használatával végezheti el. Ezek a biztonsági másolatok a csökkentett teljesítményű infrastruktúra-szolgáltatások szervizelésére szolgálnak. A biztonsági mentés csak a rendszeren belüli infrastruktúra-szolgáltatásokból származó adatokra vonatkozik. A biztonsági mentési adatkészletek nem tartalmazzák a felhasználói és az alkalmazásadatok. A felhasználói és az alkalmazásadatok védelmét külön kell védeni.

Alapértelmezés szerint az infrastruktúra biztonsági mentése az üzembe helyezési időpontban engedélyezett. Ezek a biztonsági másolatok a rendszeren belül tárolódnak, és csak a speciális támogatási esetekben érhetők el. Ha a rendszernek van hozzáférése egy külső tárolási helyhez, az infrastruktúra-biztonsági mentési szolgáltatás arra utasítja, hogy másodlagos másolatként exportálja a biztonsági másolatot az adott tárolási helyre.

A biztonsági mentési szolgáltatás engedélyezése előtt ellenőrizze, hogy teljesülnek [-e a követelmények](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

> [!NOTE]
> A Infrastructure Backup szolgáltatás nem tartalmazza a felhasználói és az alkalmazási szolgáltatásokat. A IaaS VM-alapú alkalmazások védelemmel kapcsolatos további információkért tekintse meg a következő cikkeket:
>
> - [Az Azure Stacken üzembe helyezett virtuális gépek védelme](../../user/azure-stack-manage-vm-protect.md)
> - Event Hubs idősoros adatainak védelme Azure Stack
> - Blobok adatainak védelme Azure Stack

## <a name="the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás

A szolgáltatás a következő funkciókat tartalmazza:

| Funkció                                            | Leírás                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentési infrastruktúra-szolgáltatások                     | A biztonsági mentés koordinálása a Azure Stack infrastruktúra-szolgáltatások egy részhalmazán keresztül. |
| A biztonsági másolatok tömörítése és titkosítása | A rendszer tömöríti és titkosítja a biztonsági mentési adatforrásokat, mielőtt a \' belső tárolóban tárolja vagy exportálja azokat az operátor által biztosított külső tárolóhelyre.                |
| Biztonsági mentési feladatok figyelése                              | A System figyelmeztetést küld, ha a biztonsági mentési feladatok meghiúsulnak, és elhárítják a problémát.                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás követelményeinek ellenőrzése

- **Tárolási hely** Ha megbízható hozzáférés van egy külső tárolási helyhez, a Azure Stack-infrastruktúrából elérhető fájlmegosztás szükséges, amely több biztonsági mentést képes tárolni. További információ a Infrastructure Backup szolgáltatás tárolási helyének kiválasztásáról: a [biztonsági mentési vezérlőre vonatkozó követelmények](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

- **Hitelesítő adatok** A tárolási helyhez hozzáférő felhasználói fiók hitelesítő adatai szükségesek.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [engedélyezheti Azure stack biztonsági mentését a felügyeleti portálról](../../operator/azure-stack-backup-enable-backup-console.md).

- Megtudhatja, hogyan [engedélyezheti a Azure stack biztonsági mentését a PowerShell használatával](../../operator/azure-stack-backup-enable-backup-powershell.md).
