---
title: CommVault hozzáadása Azure Stack hub Marketplace-hez
description: Ismerje meg, hogyan adhat hozzá CommVault a Azure Stack hub Marketplace-hez.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 6f61e1d34cd8c8fcd22d4ebb87ece01376dbb5dd
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375133"
---
# <a name="add-commvault-to-azure-stack-hub-marketplace"></a>CommVault hozzáadása Azure Stack hub Marketplace-hez

Ez a cikk bemutatja, hogyan CommVault élő szinkronizálást egy különálló Azure Stack hub méretezési egységen található helyreállítási virtuális gép (VM) frissítéséhez. A felhasználók számára biztonsági mentési és replikációs megoldásként letöltheti és CommVault is kínálhat.

## <a name="notes-for-commvault"></a>Megjegyzések a CommVault

- A felhasználónak telepítenie kell a biztonsági mentési és replikációs szoftvert egy virtuális gépre a forrás Azure Stack hub-előfizetésében. A Azure Site Recovery és a Azure Backup a biztonsági másolatok és a helyreállítási lemezképek tárolására szolgáló, off-stack helyet is biztosít. Mindkettőhöz szükség van egy Recovery Services-tároló létrehozására az Azure-ban, mielőtt letölti az Azure Stack hubhoz telepítendő rendszerképeket. A szoftver lemezképeit a következő címről töltheti le: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) és [Azure site Recovery](https://aka.ms/unifiedinstaller_eus).  

- Előfordulhat, hogy licencekre van szüksége a harmadik féltől származó szoftverekhez (ha van ilyen).
- A felhasználóknak segítségre lehet szükségük a forrás és a cél csatlakoztatásához egy VPN-átjárón vagy egy nyilvános IP-címen keresztül a biztonsági mentési és a replikációs gazdagépen.
- Cél Azure-beli felhőalapú előfizetés vagy egy helyreállítási cél Azure Stack hub előfizetése.
- Cél erőforráscsoport és blob Storage-fiók egy helyreállítási cél Azure Stack központban.
- Néhány megoldáshoz olyan virtuális gépeket kell létrehoznia a cél-előfizetésben, amelyeknek nonstop kell futtatnia ahhoz, hogy a forráskiszolgáló változásait fogadhasson. A [virtuális gép biztonsági mentése a CommVault-vel Azure stack központban](../user/azure-stack-network-howto-backup-commvault.md)a CommVault Live Sync a kezdeti konfiguráció során létrehozza a cél helyreállítási virtuális gépeket, és az inaktív állapotba helyezi azokat (nem számlázható), amíg a módosításokat a replikálási ciklusok során nem kell alkalmazni.

## <a name="get-commvault-for-your-marketplace"></a>CommVault beszerzése a piactéren

1. Nyissa meg az Azure Stack hub felügyeleti portált.
2. Válassza **Marketplace management**  >  **a piactér-kezelés Hozzáadás az Azure-ból**lehetőséget.

    ![Azure Stack hub CommVault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Írja be a `commvault` (igen) kifejezést.
4. Válassza ki a **CommVault próbaverzióját**. Majd válassza a **Letöltés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [A virtuális gép biztonsági mentése Azure Stack hub-on a CommVault](../user/azure-stack-network-howto-backup-commvault.md)
- [Az Azure Stack hub szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
