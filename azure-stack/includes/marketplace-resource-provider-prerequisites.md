---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e1e2c3c6c3d1149b2cd034c2f5e4abfdf26e712f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423981"
---
Ha már telepített egy erőforrás-szolgáltatót Azure Stack hub piactérről, valószínűleg végrehajtotta az általános előfeltételeket, és kihagyhatja ezt a szakaszt. Ellenkező esetben először végezze el a következő előfeltételeket: 

1. Ha még nem tette [meg, regisztrálja Azure stack hub-példányát az Azure](../operator/azure-stack-registration.md)-ban. Erre a lépésre azért van szükség, mert az Azure-ból az elemek a piactérről való csatlakoztatásához és a piactérhez való letöltéséhez szükséges.

2. Ha nem ismeri az Azure Stack hub felügyeleti portál **piactér-felügyeleti** szolgáltatását, tekintse át a [Marketplace-elemek letöltése az Azure-ból és a közzététel az Azure stack hub](../operator/azure-stack-download-azure-marketplace-item.md)-ban című cikket. A cikk végigvezeti az elemek Azure-ból az Azure Stack hub Marketplace-re való letöltésének folyamatán. A csatlakoztatott és a leválasztott forgatókönyvekre egyaránt vonatkozik. Ha a Azure Stack hub-példánya le van választva vagy részben csatlakoztatva van, a telepítés előkészítése során további előfeltételek is befejeződik.

3. Frissítse Azure Active Directory (Azure AD) kezdőkönyvtárat. A Build 1910-es verziótól kezdődően az új üzembe helyezési erőforrás-szolgáltató (DRP) alkalmazást kell használni a kezdőkönyvtár-bérlő regisztrálásához. Ez az alkalmazás lehetővé teszi, hogy a DRP sikeresen létrehozza és regisztrálja az erőforrás-szolgáltatókat. Ha ez a lépés nem fejeződött be, az erőforrás-szolgáltató telepítése sikertelen lesz. 

   - Miután sikeresen frissítette a Azure Stack hub-példányt a 1910-es vagy újabb verziójára, kövesse az [Azure stack hub Tools adattár klónozására/letöltésére vonatkozó utasításokat](../operator/azure-stack-powershell-download.md). 
   - Ezután kövesse az [Azure stack hub Azure ad-kezdőkönyvtár frissítésére vonatkozó utasításokat (a frissítések vagy az új erőforrás-szolgáltatók telepítése után)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 