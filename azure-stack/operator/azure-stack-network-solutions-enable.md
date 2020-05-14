---
title: Fortinet-FortiGate hozzáadása Azure Stack hub Marketplace-hez
description: Megtudhatja, hogyan adhat hozzá Fortinet-FortiGate a Azure Stack hub piactérhez, így a felhasználók hálózati megoldásokat hozhatnak létre.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: fb9c8bbb16bcbbee5e1316522aff99752354ef7f
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375111"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate

Hozzáadhat FortiGate következő generációs tűzfalat (NGFW) a Azure Stack hub Marketplace-hez. A FortiGate lehetővé teszi, hogy a felhasználók olyan hálózati megoldásokat hozzanak létre, mint például a virtuális magánhálózat (VPN) Azure Stack hub és a VNET-társítás számára. Egy hálózati virtuális berendezés (NVA) szabályozza a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra.

Az Azure Marketplace FortiGate kapcsolatos további információkért lásd: [Fortinet FortiGate a következő generációs tűzfal egyetlen virtuálisgép-megoldás](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>A szükséges Azure Stack hub Marketplace-elemek letöltése

1. Nyissa meg az Azure Stack hub felügyeleti portált.

2. Válassza a **piactér-kezelés** elemet, és válassza **a Hozzáadás az Azure-ból**lehetőséget.

3. Írja be `Forti` a keresőmezőbe, majd kattintson duplán > válassza a **Letöltés** lehetőséget a következő elemek legújabb elérhető verzióinak beszerzéséhez:
    - Fortinet FortiGate – Azure-beli virtuális BYOL
    - FortiGate NGFW – egyetlen virtuális gép üzembe helyezése (BYOL)

    ![Azure Stack hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

4. Várjon, amíg a Marketplace-elemek nem rendelkeznek a **letöltött**állapottal. Az elemek letöltése több percet is igénybe vehet.

    ![Azure Stack hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Következő lépések

- [VPN beállítása Azure Stack hubhoz a FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-onprem.md)  
- [Két virtuális hálózatok összekapcsolásának módja a peering használatával](../user/azure-stack-network-howto-vnet-to-vnet.md)  
- [VNET létrehozása VNET-kapcsolathoz a Fortinet FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
