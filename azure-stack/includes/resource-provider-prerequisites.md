---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a980becc3bf749987759f90a9ab84050d033a04
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297866"
---
Ha már telepített egy erőforrás-szolgáltatót, valószínűleg végrehajtotta a következő előfeltételeket, és kihagyhatja ezt a szakaszt. Ellenkező esetben a folytatás előtt végezze el a következő lépéseket: 

1. Ha még nem tette [meg, regisztrálja Azure stack hub-példányát az Azure](../operator/azure-stack-registration.md)-ban. Erre a lépésre azért van szükség, mert az Azure-ból az elemek a piactérről való csatlakoztatásához és a piactérhez való letöltéséhez szükséges.

2. Ha nem ismeri az Azure Stack hub felügyeleti portál **piactér-felügyeleti** szolgáltatását, tekintse át a [Marketplace-elemek letöltése az Azure-ból és a közzététel az Azure stack hub](../operator/azure-stack-download-azure-marketplace-item.md)-ban című cikket. A cikk végigvezeti az elemek Azure-ból az Azure Stack hub Marketplace-re való letöltésének folyamatán. A csatlakoztatott és a leválasztott forgatókönyvekre egyaránt vonatkozik. Ha a Azure Stack hub-példánya le van választva vagy részben csatlakoztatva van, a telepítés előkészítése során további előfeltételek is befejeződik.

3. Frissítse Azure Active Directory (Azure AD) kezdőkönyvtárat. A Build 1910-től kezdve új alkalmazást kell regisztrálni a saját kezdőkönyvtár-bérlőben. Ez az alkalmazás lehetővé teszi, hogy Azure Stack hub sikeresen hozzon létre és regisztráljon újabb erőforrás-szolgáltatókat (például Event Hubs, IoT Hub és másokat) az Azure AD-Bérlővel. Ez egy egyszeri művelet, amelyet a 1910-es vagy újabb verzióra való frissítés után kell elvégezni. Ha ez a lépés nem fejeződött be, a Marketplace erőforrás-szolgáltató telepítése sikertelen lesz. 

   - Miután sikeresen frissítette a Azure Stack hub-példányt a 1910-es vagy újabb verziójára, kövesse az [Azure stack hub Tools adattár klónozására/letöltésére vonatkozó utasításokat](../operator/azure-stack-powershell-download.md). 
   - Ezután kövesse az [Azure stack hub Azure ad-kezdőkönyvtár frissítésére vonatkozó utasításokat (a frissítések vagy az új erőforrás-szolgáltatók telepítése után)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 