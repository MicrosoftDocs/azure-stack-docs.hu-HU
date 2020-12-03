---
title: Alapminták üzembe helyezése Azure Stack központban
description: Megtudhatja, hogyan helyezhet üzembe alapmintákat Azure Stack hub használatával.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5609d9195a909a20a54917555a7309fe0901d7fa
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525472"
---
# <a name="deploy-foundational-patterns-overview"></a>Alapvető minták üzembe helyezése – áttekintés


Mindegyik minta útmutatást, Azure Resource Manager sablonokat és oktatóanyagokat tartalmaz. Ezeket a mintákat és a harmadik féltől származó alkalmazásokat a Azure Stack által még nem támogatott ajánlatok létrehozására használhatja. Például a kezelők gyakran kezelik a virtuális magánhálózat (VPN) egyetlen Azure Stack hub-példányra való beállításával kapcsolatos bonyodalmakat, ami sokkal kevésbé hozza létre a VPN-t, amely két vagy több környezetet ölel fel. A kezelők olyan problémákkal találkozhatnak, amikor egy Azure Stack hub előtt próbálnak létrehozni terheléselosztó-t a munkaterhelések kezeléséhez. A következő útmutatással felgyorsíthatja az üzemi használatra kész számítási feladatok felszabadításának idejét.

## <a name="networking"></a>Hálózatkezelés

A hálózatkezelési minták segítségével megtalálhatja a virtuális hálózatok összevonásának Azure Stack hubhoz való létrehozásával kapcsolatos utasításokat. A virtuális hálózati társítás lehetővé teszi két virtuális hálózat összekapcsolását úgy, hogy egyetlen hálózatként jelenjenek meg. A virtuális hálózatok közötti helyek közötti kapcsolat a távoli és az útválasztási szolgáltatás (RRAS) segítségével valósítható meg. Az RRAS lehetővé teszi, hogy a Windows rendszerű virtuális gépek (VM) útválasztóként működjenek. Ezekkel a parancsfájlokkal két virtuális hálózatot helyezhet üzembe egy Azure Stack hub-erőforráscsoport, előfizetések és két Azure Stack hub-példány között. A szkripteket Azure Stack hub-on és a globális Azure-on is üzembe helyezheti. 

Minden cikk a gyakori szempontokat tárgyalja, például: 
- Méretezés
- Sávszélesség
- Biztonság
- Az üzletmenet folytonossága

|  Társviszony létesítése virtuális hálózatok között  |  VPN  |  Terheléselosztó  |
| --- | --- | --- |
| ![Virtuális hálózatok társítása virtuális gépekkel](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Virtuális hálózatok társítása virtuális gépekkel](azure-stack-network-howto-vnet-peering.md) | ![VPN beállítása helyszíni rendszerre](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[VPN beállítása helyszíni rendszerre](azure-stack-network-howto-vnet-to-onprem.md) | ![F5 Load Balancer](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[F5 Load Balancer](network-howto-f5.md) |
| ![Virtuális hálózati társítás a FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Virtuális hálózati társítás a FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Virtuális magánhálózat](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Virtuális hálózat és virtuális hálózati kapcsolatok](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![VPN-alagút létrehozása (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[VPN-alagút létrehozása (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Több helyek közötti VPN beállítása](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Több helyek közötti VPN beállítása](network-howto-vpn-tunnel.md) | |
|  | ![VPN-alagút (IPSEC) létrehozása](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[VPN-alagút (IPSEC) létrehozása](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Tárolás

A tárolási minták használatával növelheti a tárolási lehetőségeket Azure Stack hubhoz. Azure Stack hub-tároló véges. Kapcsolódjon a meglévő adatközpont erőforrásaihoz. A Windows rendszerű virtuális gépek külső iSCSI-tárolóhoz való kapcsolódásra vonatkozó utasítások a Azure Stack hub-ban történő létrehozásához. Megtudhatja, hogyan engedélyezheti a főbb funkciókat, például a többutas I/O (MPIO) szolgáltatást a virtuális gép és a külső tároló közötti teljesítmény és kapcsolat optimalizálása érdekében.

| iSCSI-tároló | Tárterület kiterjesztése |
| --- | --- |
| ![Kapcsolódás iSCSI-tárolóhoz](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Kapcsolódás iSCSI-tárolóhoz](azure-stack-network-howto-iscsi-storage.md) | ![Az adatközpont kiterjesztése](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Az adatközpont kiterjesztése](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Backup

A biztonsági mentési és vész-helyreállítási minták használatával az előfizetésben lévő összes erőforrást átmásolhatja az Azure-ba vagy egy másik Azure Stack hub-példányba. Ezek a minták a virtuális gépeken belül tárolt információk egy másik környezetbe való replikálásához a CommVault Live-Sync használatával foglalkoznak. Az adatküldéshez létrehozhat egy olyan parancsfájlt, amely létrehoz egy Storage-fiókot és egy biztonsági mentési Storage-fiókot. Az Azure-előfizetési replikátor modullal beállíthatja az erőforrás-replikációt, és testre szabhatja a processzort különböző erőforrások kezeléséhez. 



|  Biztonsági mentés  |  Másolás  |
| --- | --- |
| ![A virtuális gép biztonsági mentése Azure Stack hub-on a CommVault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[A virtuális gép biztonsági mentése Azure Stack hub-on a CommVault](azure-stack-network-howto-backup-commvault.md) | ![Előfizetés-erőforrások másolása](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Előfizetés-erőforrások másolása](azure-stack-network-howto-backup-replicator.md) |
|  ![A Storage-fiókok biztonsági mentése Azure Stack hub-on](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[A Storage-fiókok biztonsági mentése Azure Stack hub-on](azure-stack-network-howto-backup-storage.md)  | |

## <a name="github-samples"></a>GitHub-minták

A sablonokat az [Azure intelligens Edge Pattern GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) -tárházában találja.

## <a name="next-steps"></a>További lépések

[Hibrid Azure-minták és -megoldások dokumentációja](/hybrid/app-solutions)
