---
title: Storage-fiókok létrehozása Azure Stack központban
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre Storage-fiókokat Azure Stack központban.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 2d4085605fc37e95c3f68db074d4a517f5296a7a
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367913"
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
