---
title: CommVault hozzáadása a Azure Stack Marketplace-hez | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá CommVault a Azure Stack Marketplace-hez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 787453550e9a8ed69aa9dfda0a03ea5e57c49d7a
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802326"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>CommVault hozzáadása a Azure Stack Marketplace-hez

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk bemutatja, hogyan CommVault élő szinkronizálást egy különálló Azure Stack skálázási egységben található helyreállítási virtuális gép frissítéséhez. A felhasználók számára biztonsági mentési és replikációs megoldásként letöltheti és CommVault is kínálhat. 

## <a name="notes-for-commvault"></a>Megjegyzések a CommVault

- A felhasználónak telepítenie kell a biztonsági mentési és replikálási szoftvert egy virtuális gépre a forrás Azure Stack előfizetésében. A Azure Site Recovery és a Azure Backup a biztonsági másolatok és a helyreállítási lemezképek tárolására szolgáló, off-stack helyet is biztosít. Mindkettőhöz szükség van egy Recovery Services-tároló létrehozására az Azure-ban, mielőtt a következő helyekről letölti a Azure Stack telepítendő rendszerképeket: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) és [Azure site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Előfordulhat, hogy licencekre van szüksége a harmadik féltől származó szoftverekhez (ha van ilyen).
- Előfordulhat, hogy a felhasználóknak asszisztensre van szükségük a forrás és a cél csatlakoztatásához egy Virtual Network átjárón (VPN) vagy nyilvános IP-címen keresztül a biztonsági mentési és a replikációs gazdagépen.
- Célzott Azure-beli felhőalapú előfizetés vagy előfizetés helyreállítási célra Azure Stack.
- A cél erőforráscsoport és a Blob Storage fiók egy helyreállítási cél Azure Stack.
- Néhány megoldáshoz olyan virtuális gépeket kell létrehoznia a cél-előfizetésben, amelyeknek nonstop kell futtatnia ahhoz, hogy a forráskiszolgáló változásait fogadhasson. A [virtuális gép biztonsági mentése Azure stack a CommVault-mel](../user/azure-stack-network-howto-backup-commvault.md)a CommVault Live Sync a kezdeti konfiguráció során létrehozza a cél helyreállítási virtuális gépeket, és az inaktív állapotba helyezi azokat (nem számlázható), amíg a módosításokat a replikálási ciklusok során nem kell alkalmazni.


## <a name="get-commvault-for-your-marketplace"></a>CommVault beszerzése a piactéren

1. Nyissa meg a Azure Stack felügyeleti portált.
2. Válassza a **piactér-kezelés** > **Az Azure-ból való hozzáadás**lehetőséget.

    ![Azure Stack CommVault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Írja be a `commvault` (igen) kifejezést.
4. Válassza ki a **CommVault próbaverzióját**. Majd válassza a **Letöltés**lehetőséget.


## <a name="next-steps"></a>Következő lépések

[A virtuális gép biztonsági mentése Azure Stack a CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[A Azure Stack szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
