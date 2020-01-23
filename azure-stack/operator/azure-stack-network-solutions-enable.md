---
title: Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti Azure Stack hub hálózati megoldását a Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: dde1ba144f14bd084e2e1050b1401143a06f61c1
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534651"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate

Hozzáadhat FortiGate következő generációs tűzfalat is a Azure Stack hub piactérhez. A FortiGate lehetővé teszi, hogy a felhasználók olyan hálózati megoldásokat hozzanak létre, mint a virtuális magánhálózat (VPN) Azure Stack hub és a VNET-társítás számára. Egy hálózati virtuális berendezés (NVA) szabályozza a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. 

Az Azure Marketplace FortiGate kapcsolatos további információkért lásd: [Fortinet FortiGate következő generációs tűzfal egyetlen VM-megoldás](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>A szükséges Azure Stack hub Marketplace-elemek letöltése

1.  Nyissa meg az Azure Stack hub felügyeleti portált.

2.  Válassza a **piactér-kezelés** elemet, és válassza **a Hozzáadás az Azure-ból**lehetőséget.

3. A keresőmezőbe írja be a `Forti` kifejezést, majd kattintson duplán > válassza a **Letöltés** lehetőséget a következő elemek legújabb elérhető verzióinak beszerzéséhez: 
    - Fortinet FortiGate – Azure-beli virtuális BYOL
    - FortiGate NGFW – egyetlen virtuális gép üzembe helyezése (BYOL)

    ![Azure Stack hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Várjon, amíg a Marketplace-elemek nem rendelkeznek a **letöltött**állapottal. Az elemek letöltése több percet is igénybe vehet.

    ![Azure Stack hub FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Következő lépések

[VPN beállítása Azure Stack hubhoz a FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Két virtuális hálózatok összekapcsolásának módja a peering használatával](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[VNET létrehozása VNET-kapcsolathoz a Fortinet FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
