---
title: Hálózati megoldás nyújtása Azure Stack Fortinet FortiGate | Microsoft Docs
description: Ismerje meg, hogyan engedélyezhető a hálózati megoldás a Azure Stack Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962440"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Hálózati megoldás nyújtása Azure Stack Fortinet FortiGate

Hozzáadhat FortiGate következő generációs tűzfalat is a Azure Stack Marketplace-hez. A FortiGate lehetővé teszi, hogy a felhasználók olyan hálózati megoldásokat hozzanak létre, mint például a virtuális magánhálózat (VPN) Azure Stack és a VNET. Egy hálózati virtuális berendezés (NVA) szabályozza a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. 

Az Azure Marketplace FortiGate kapcsolatos további információkért lásd: [Fortinet FortiGate következő generációs tűzfal egyetlen VM-megoldás](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-marketplace-items"></a>A szükséges Azure Stack Marketplace-elemek letöltése

1.  Nyissa meg a Azure Stack felügyeleti portált.

2.  Válassza a **piactér-kezelés** elemet, és válassza **a Hozzáadás az Azure-ból**lehetőséget.

3. A keresőmezőbe írja be a `Forti` kifejezést, majd kattintson duplán > válassza a **Letöltés** lehetőséget a következő elemek legújabb elérhető verzióinak beszerzéséhez: 
    - Fortinet FortiGate – Azure-beli virtuális BYOL
    - FortiGate NGFW – egyetlen virtuális gép üzembe helyezése (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Várjon, amíg a Marketplace-elemek nem rendelkeznek a **letöltött**állapottal. Az elemek letöltése több percet is igénybe vehet.

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>További lépések

[VPN beállítása Azure Stack FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Két virtuális hálózatok összekapcsolásának módja a peering használatával](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[VNET létrehozása VNET-kapcsolathoz a Fortinet FortiGate NVA használatával](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
