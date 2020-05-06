---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: d84b6a7d7f4ec1777b8e58e8cafc9ba64726393f
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847876"
---
Ha már telepített egy erőforrás-szolgáltatót, valószínűleg végrehajtotta a következő előfeltételeket, és kihagyhatja ezt a szakaszt. Ellenkező esetben a folytatás előtt végezze el a következőket: 

1. Ha még nem tette [meg, regisztrálja Azure stack hub-példányát az Azure](../operator/azure-stack-registration.md)-ban. Erre a lépésre azért van szükség, mert az Azure-ból az elemek a piactérről való csatlakoztatásához és a piactérhez való letöltéséhez szükséges.

2. Ha nem ismeri az Azure Stack hub felügyeleti portál **piactér-felügyeleti** szolgáltatását, tekintse át a [Marketplace-elemek letöltése az Azure-ból és a közzététel az Azure stack hub](../operator/azure-stack-download-azure-marketplace-item.md)-ban című cikket. A cikk végigvezeti az elemek Azure-ból az Azure Stack hub Marketplace-re való letöltésének folyamatán. A csatlakoztatott és a leválasztott forgatókönyvekre egyaránt vonatkozik. Ha a Azure Stack hub-példánya le van választva vagy részben csatlakoztatva van, a telepítés előkészítése során további előfeltételek is befejeződik.

3. Frissítse Azure Active Directory (Azure AD) kezdőkönyvtárat. A Build 1910-es verziótól kezdődően az új üzembe helyezési erőforrás-szolgáltató (DRP) alkalmazást kell használni a kezdőkönyvtár-bérlő regisztrálásához. Ez az alkalmazás lehetővé teszi, hogy a DRP sikeresen létrehozza és regisztrálja az erőforrás-szolgáltatókat. Ha ez a lépés nem fejeződött be, az erőforrás-szolgáltató telepítése sikertelen lesz. 

   - Miután sikeresen frissítette a Azure Stack hub-példányt a 1910-es vagy újabb verziójára, kövesse az [Azure stack hub Tools adattár klónozására/letöltésére vonatkozó utasításokat](../operator/azure-stack-powershell-download.md). 
   - Ezután kövesse az [Azure stack hub Azure ad-kezdőkönyvtár frissítésére vonatkozó utasításokat (a frissítések vagy az új erőforrás-szolgáltatók telepítése után)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 