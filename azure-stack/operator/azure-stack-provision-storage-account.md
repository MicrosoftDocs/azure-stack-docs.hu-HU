---
title: Storage-fiókok létrehozása Azure Stack központban
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre Storage-fiókokat Azure Stack központban.
author: PatAltimore
ms.topic: how-to
ms.date: 1/22/2020
ms.author: patricka
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 953ba2c8ee1c5d5950b4f42b0771d5c0976c2d89
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869542"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Storage-fiókok létrehozása Azure Stack központban

Azure Stack hub Storage-fiókjai közé tartozik a blob és a Table Services, valamint a Storage-adatobjektumok egyedi névtere. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. Az Azure stack hub POC számítógépén jelentkezzen be `https://adminportal.local.azurestack.external` [rendszergazdaként](../asdk/asdk-connect.md), majd kattintson az **+ erőforrás létrehozása** és  >  **Storage**  >  **Storage-fiók** elemre.

   ![A Storage-fiók létrehozása Azure Stack hub felügyeleti portálon](media/azure-stack-provision-storage-account/image01.png)

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, majd kattintson a **Létrehozás** gombra a Storage-fiók létrehozásához.

   ![A Storage-fiók áttekintése Azure Stack hub felügyeleti portálján](media/azure-stack-provision-storage-account/image02.png)

3. Az új Storage-fiók megjelenítéséhez kattintson a **minden erőforrás** lehetőségre, majd keresse meg a Storage-fiókot, és kattintson a nevére.

    ![A Storage-fiók neve a Azure Stack hub felügyeleti portálján](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>További lépések

- [Használjon Azure Resource Manager-sablonokat](../user/azure-stack-arm-templates.md)
- [Ismerje meg az Azure tárfiókokat](/azure/storage/common/storage-create-storage-account)
- [Az Azure Stack hub Azure-konzisztens tárolási ellenőrzési útmutatójának letöltése](https://aka.ms/azurestacktp1doc)
