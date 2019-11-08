---
title: Storage-fiókok létrehozása a Azure Stackban | Microsoft Docs
titleSuffix: Azure Stack
description: Megtudhatja, hogyan hozhat létre Storage-fiókokat a Azure Stackban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802334"
---
# <a name="create-storage-accounts-in-azure-stack"></a>Storage-fiókok létrehozása a Azure Stackban

Az Azure Stack-tárfiókok tartalmazzák a Blob és a Table szolgáltatásokat és az egyedi névteret a tároló adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. A Azure Stack POC számítógépen jelentkezzen be [rendszergazdaként](../asdk/asdk-connect.md)a `https://adminportal.local.azurestack.external`ra, majd kattintson az **+ erőforrás létrehozása** ** > adattároló > ** Storage- **fiók**elemre.

   ![A Storage-fiók létrehozása a Azure Stack felügyeleti portálon](media/azure-stack-provision-storage-account/image01.png)

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, majd kattintson a **Létrehozás** gombra a Storage-fiók létrehozásához.

   ![A Storage-fiók áttekintése Azure Stack felügyeleti portálon](media/azure-stack-provision-storage-account/image02.png)

3. Az új Storage-fiók megjelenítéséhez kattintson a **minden erőforrás**lehetőségre, majd keresse meg a Storage-fiókot, és kattintson a nevére.

    ![A Storage-fiók neve a Azure Stack felügyeleti portálon](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Következő lépések

- [Használjon Azure Resource Manager-sablonokat](../user/azure-stack-arm-templates.md)
- [További tudnivalók az Azure Storage-fiókokról](/azure/storage/common/storage-create-storage-account)
- [Töltse le a Azure Stack Azure-konzisztens tárolási ellenőrzési útmutatóját](https://aka.ms/azurestacktp1doc)
