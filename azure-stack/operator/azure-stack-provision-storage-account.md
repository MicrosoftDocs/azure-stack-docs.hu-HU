---
title: Storage-fiókok a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Stack Storage-fiókot.
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
ms.openlocfilehash: 60336477f1dec9618fd6cc439e9d2f5d098b3399
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829385"
---
# <a name="storage-accounts-in-azure-stack"></a>Tárfiókok az Azure Stack szolgáltatásban

Az Azure Stack-tárfiókok tartalmazzák a Blob és a Table szolgáltatásokat és az egyedi névteret a tároló adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. A Azure stack POC számítógépén jelentkezzen be `https://adminportal.local.azurestack.external` rendszergazdaként [](../asdk/asdk-connect.md), majd kattintson az **+ erőforrás** > létrehozása **+ tároló** > **Storage-fiók**elemre.

   ![A Storage-fiók létrehozása](media/azure-stack-provision-storage-account/image01.png)
2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, majd kattintson a **Létrehozás** gombra a Storage-fiók létrehozásához.

   ![A Storage-fiók áttekintése](media/azure-stack-provision-storage-account/image02.png)
3. Az új Storage-fiók megjelenítéséhez kattintson a **minden erőforrás**lehetőségre, majd keresse meg a Storage-fiókot, és kattintson a nevére.

    ![A Storage-fiók neve](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>További lépések
[Használjon Azure Resource Manager-sablonokat](../user/azure-stack-arm-templates.md)

[További tudnivalók az Azure Storage-fiókokról](/azure/storage/common/storage-create-storage-account)

[Töltse le a Azure Stack Azure-konzisztens tárolási ellenőrzési útmutatóját](https://aka.ms/azurestacktp1doc)
