---
title: Alkalmazás üzembe helyezése Azure Stack hub-ban | Microsoft Docs
description: Alkalmazás üzembe helyezése Azure Stack hubhoz.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 5f29fd7a0d2ab8df9d713927b7e9bada38d0e249
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820678"
---
# <a name="common-deployments-for-azure-stack-hub"></a>Azure Stack hub általános telepítései

A Azure Stack hub a szolgáltatásként szolgáló infrastruktúra (IaaS) és a szolgáltatásként szolgáló platform (Péter) használatát is támogatja. Ez a cikk a virtuális gép (VM) beállításához és a Azure Stack hubhoz való üzembe helyezéséhez szükséges erőforrásokat sorolja fel.

## <a name="before-you-begin"></a>Előzetes teendők

Mielőtt elkezdené az alkalmazás központi telepítését Azure Stack hubhoz, állítsa be a fejlesztői gépet. Az Azure Stack hub fejlesztőként való működésének előkészítésével kapcsolatos útmutatásért lásd: [fejlesztési környezet beállítása Azure stack hub-ban](azure-stack-dev-start.md).

## <a name="deploy-an-app-to-a-vm"></a>Alkalmazás üzembe helyezése egy virtuális gépen

A következő útmutató cikkeiben virtuális gépet (VM) állít be Azure Stack hub-ban, majd üzembe helyezheti az alkalmazást a fejlesztői munkaállomásról Azure Stack hubhoz.

- [.NET Core (C#)](azure-stack-dev-start-howto-vm-dotnet.md)
- [Java](azure-stack-dev-start-howto-vm-java.md)
- [Go](azure-stack-dev-start-howto-vm-go.md)
- [Ruby](azure-stack-dev-start-howto-vm-ruby.md)
- [Python](azure-stack-dev-start-howto-vm-python.md)
- [NodeJS](azure-stack-dev-start-howto-vm-nodejs.md)

## <a name="next-steps"></a>Következő lépések

- [Fejlesztési környezet beállítása Azure stack központban](azure-stack-dev-start.md).
- [Folyamatos integrációs/folyamatos szállítási (CI/CD) folyamat beállítása Azure stack hub-ban](azure-stack-solution-pipeline.md).
