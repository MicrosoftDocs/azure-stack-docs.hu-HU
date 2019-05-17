---
title: Tárfiókok az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Stack tárfiókok.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 77d4963d2e3e468cb2de1e41a5c483e0339a3449
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782457"
---
# <a name="storage-accounts-in-azure-stack"></a>Tárfiókok az Azure Stack szolgáltatásban

Az Azure Stack tárfiókok tartalmazzák a Blob és Table szolgáltatások, és az egyedi névteret a tároló adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. Az Azure Stack POC számítógépen jelentkezzen be `https://adminportal.local.azurestack.external` , [rendszergazda](../asdk/asdk-connect.md), és kattintson a **+ erőforrás létrehozása** > **adatok + tárolás**  >  **Tárfiók**.

   ![A storage-fiók létrehozása](media/azure-stack-provision-storage-account/image01.png)
2. Az a **storage-fiók létrehozása** panelen írja be a tárfiók nevét. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévőt, majd kattintson a **létrehozás** a tárfiók létrehozásához.

   ![Tekintse át a storage-fiók](media/azure-stack-provision-storage-account/image02.png)
3. Az új tárfiók megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a storage-fiókot, és kattintson a nevére.

    ![A tárfiók nevét](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>További lépések
[Használjon Azure Resource Manager-sablonokat](../user/azure-stack-arm-templates.md)

[További tudnivalók az Azure storage-fiókok](/azure/storage/common/storage-create-storage-account)

[Töltse le az Azure Stack az Azure-konzisztens tároló-ellenőrzési útmutatóját](https://aka.ms/azurestacktp1doc)
