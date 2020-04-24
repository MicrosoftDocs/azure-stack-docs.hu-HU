---
title: CommVault hozzáadása az Azure Stack hub piactérhez
description: Ismerje meg, hogyan adhat hozzá CommVault az Azure Stack hub Marketplace-hez.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: d79137c5a00b4762cf1f7729000f8912f13ba066
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661355"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>CommVault hozzáadása az Azure Stack hub piactérhez

Ez a cikk bemutatja, hogyan CommVault élő szinkronizálást egy különálló Azure Stack hub méretezési egységen található helyreállítási virtuális gép frissítéséhez. A felhasználók számára biztonsági mentési és replikációs megoldásként letöltheti és CommVault is kínálhat. 

## <a name="notes-for-commvault"></a>Megjegyzések a CommVault

- A felhasználónak telepítenie kell a biztonsági mentési és replikációs szoftvert a forrás Azure Stack hub-előfizetéshez tartozó virtuális gépre. A Azure Site Recovery és a Azure Backup a biztonsági másolatok és a helyreállítási lemezképek tárolására szolgáló, off-stack helyet is biztosít. Mindkettőhöz szükség van egy Recovery Services-tároló létrehozására az Azure-ban, mielőtt az alábbi helyekről letölti a Azure Stack hubhoz telepítendő rendszerképeket: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) és [Azure site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Előfordulhat, hogy licencekre van szüksége a harmadik féltől származó szoftverekhez (ha van ilyen).
- Előfordulhat, hogy a felhasználóknak asszisztensre van szükségük a forrás és a cél csatlakoztatásához egy Virtual Network átjárón (VPN) vagy nyilvános IP-címen keresztül a biztonsági mentési és a replikációs gazdagépen.
- Azure-beli felhőalapú előfizetés vagy előfizetés megcélzása helyreállítási cél Azure Stack hub-ra.
- Cél erőforráscsoport és Blob Storage fiók egy helyreállítási cél Azure Stack hub-ban.
- Néhány megoldáshoz olyan virtuális gépeket kell létrehoznia a cél-előfizetésben, amelyeknek nonstop kell futtatnia ahhoz, hogy a forráskiszolgáló változásait fogadhasson. A [virtuális gép biztonsági mentése Azure stack központban a CommVault-mel](../user/azure-stack-network-howto-backup-commvault.md)a CommVault Live Sync a kezdeti konfiguráció során létrehozza a cél helyreállítási virtuális gépeket, és az inaktív állapotba helyezi azokat (nem számlázható), amíg a módosításokat a replikálási ciklusban nem kell alkalmazni.


## <a name="get-commvault-for-your-marketplace"></a>CommVault beszerzése a piactéren

1. Nyissa meg az Azure Stack hub felügyeleti portált.
2. Válassza a **piactér-kezelés** > **Hozzáadás az Azure-ból**lehetőséget.

    ![Azure Stack hub CommVault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Írja be a `commvault` (igen) kifejezést.
4. Válassza ki a **CommVault próbaverzióját**. Majd válassza a **Letöltés**lehetőséget.


## <a name="next-steps"></a>További lépések

[A virtuális gép biztonsági mentése Azure Stack hub-on a CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[Az Azure Stack hub szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
