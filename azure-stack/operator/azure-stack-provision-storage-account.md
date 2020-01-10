---
title: Storage-fiókok létrehozása Azure Stack központban | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre Storage-fiókokat Azure Stack központban.
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
ms.openlocfilehash: 0319ab52f27eb6dd269db0eb161727a623841961
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809882"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Storage-fiókok létrehozása Azure Stack központban

Azure Stack hub Storage-fiókjai közé tartozik a blob és a Table Services, valamint a Storage-adatobjektumok egyedi névtere. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. Az Azure Stack hub POC számítógépen jelentkezzen be a [`https://adminportal.local.azurestack.external` rendszergazdaként, majd](../asdk/asdk-connect.md)kattintson az **+ erőforrás létrehozása** ** > adattároló > ** **Storage-fiók**elemre.

   ![A Storage-fiók létrehozása Azure Stack hub felügyeleti portálon](media/azure-stack-provision-storage-account/image01.png)

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, majd kattintson a **Létrehozás** gombra a Storage-fiók létrehozásához.

   ![A Storage-fiók áttekintése Azure Stack hub felügyeleti portálján](media/azure-stack-provision-storage-account/image02.png)

3. Az új Storage-fiók megjelenítéséhez kattintson a **minden erőforrás**lehetőségre, majd keresse meg a Storage-fiókot, és kattintson a nevére.

    ![A Storage-fiók neve a Azure Stack hub felügyeleti portálján](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Következő lépések

- [Használjon Azure Resource Manager-sablonokat](../user/azure-stack-arm-templates.md)
- [További tudnivalók az Azure Storage-fiókokról](/azure/storage/common/storage-create-storage-account)
- [Az Azure Stack hub Azure-konzisztens tárolási ellenőrzési útmutatójának letöltése](https://aka.ms/azurestacktp1doc)
