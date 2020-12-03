---
title: Alkalmazás üzembe helyezése Azure Stack hubhoz
description: Alkalmazás üzembe helyezése Azure Stack hubhoz.
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 08728a6e0de2bc1ce0e646de124fb58dac83c8fe
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525370"
---
# <a name="common-deployments-for-azure-stack-hub"></a>Azure Stack hub általános telepítései

A Azure Stack hub a szolgáltatásként szolgáló infrastruktúra (IaaS) és a szolgáltatásként szolgáló platform (Péter) használatát is támogatja. Ez a cikk a virtuális gép (VM) beállításához és a Azure Stack hubhoz való üzembe helyezéséhez szükséges erőforrásokat sorolja fel.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené az alkalmazás központi telepítését Azure Stack hubhoz, állítsa be a fejlesztői gépet. Az Azure Stack hub fejlesztőként való működésének előkészítésével kapcsolatos útmutatásért lásd: [fejlesztési környezet beállítása Azure stack hub-ban](azure-stack-dev-start.md).

## <a name="deploy-an-app-to-a-vm"></a>Alkalmazás üzembe helyezése egy virtuális gépen

A következő útmutató cikkeiben virtuális gépet (VM) állít be Azure Stack hub-ban, majd üzembe helyezheti az alkalmazást a fejlesztői munkaállomásról Azure Stack hubhoz.

- [.NET Core (C#)](azure-stack-dev-start-howto-vm-dotnet.md)
- [Java](azure-stack-dev-start-howto-vm-java.md)
- [Ugrás](azure-stack-dev-start-howto-vm-go.md)
- [Ruby](azure-stack-dev-start-howto-vm-ruby.md)
- [Python](azure-stack-dev-start-howto-vm-python.md)
- [NodeJS](azure-stack-dev-start-howto-vm-nodejs.md)

## <a name="next-steps"></a>További lépések

- [Fejlesztési környezet beállítása Azure stack központban](azure-stack-dev-start.md).
- [Folyamatos integrációs/folyamatos szállítási (CI/CD) folyamat beállítása Azure stack hub-ban](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/hybrid-devops).
