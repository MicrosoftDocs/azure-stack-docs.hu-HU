---
title: Egyéni Piactéri elem közzététele az Azure Stackben (felhőüzemeltető) |} A Microsoft Docs
description: Az Azure Stack operátorait szerint megtudhatja, hogyan egyéni Piactéri elem közzététele az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 70a1e1223accda5dfb273372da486588fb030afc
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172759"
---
# <a name="azure-stack-marketplace-overview"></a>Az Azure Stack piactéren – áttekintés

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack piactéren olyan szolgáltatások, alkalmazások és testre szabott Azure stack-erőforrások gyűjteménye. Erőforrások közé tartoznak a hálózatok, virtuális gépek, tárolási és egyéb. Új erőforrásokat hozhat létre és helyezzen üzembe új alkalmazásokat; a piactéren használatával vagy keresse meg és válassza ki a használni kívánt elemeket. Piactéri elem használandó felhasználók kell előfizetés egy ajánlatra, amely hozzáférést biztosít számukra az elemet.

Az Azure Stack operátorait szerint dönt, hogy a hozzáadni kívánt elemek (közzététel) a piactéren. Elemek, például adatbázisok, alkalmazásszolgáltatások, közzététele, és így tovább. Közzététel őket a felhasználók számára láthatóvá teszi. Teheti közzé egyéni elemeket, vagy közzéteheti az egyre növekvő elemek [Azure Marketplace-elemek listáját](azure-stack-marketplace-azure-items.md). A piactéren tesz közzé egy elemet, ha a felhasználók láthatják őket öt percen belül.

> [!CAUTION]  
> Minden katalógus elem összetevők, beleértve a képeket és JSON-fájlokat, miután elérhetővé teszi azokat az Azure Stack piactéren hitelesítés nélkül érhetők el. Egyéni Piactéri elemek közzétételekor további szempontokért lásd: [létrehozása és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md).

Nyissa meg a Marketplace-en, a felügyeleti portálon válassza az **+ erőforrás létrehozása**.

![Marketplace](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-elemek

Az Azure Stack piactéren elem egy szolgáltatás, alkalmazás vagy erőforrás, amely a felhasználók letöltheti és használhatja. Az összes Azure Stack piactéren elemek láthatók minden felhasználó számára, beleértve a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nincs szükség-előfizetés megtekintése, de azok nem működik a felhasználók számára.

Minden Marketplace-en elemnek:

* Az Azure Resource Manager-sablon erőforrás kiépítéséhez.
* A metaadatok, például a karakterláncokat, az ikonok és az egyéb marketinganyag.
* Formázási információk megjelenítéséhez a cikk a portálon.

A piactéren közzétett minden eleme az Azure Gallery-csomag (.azpkg) formátumot használja. Üzembe helyezés vagy a futásidejű erőforrások hozzáadása (kód, a szoftver vagy virtuálisgép-rendszerképek a .zip kiterjesztésű fájlok) az Azure Stackhez külön-külön, a Piactéri elem nem részeként.

Az 1803 és az újabb Azure Stack alakítja lemezképek ritka fájlokat, amikor letöltik az Azure-ban, vagy ha egyéni lemezképeket tölthetnek fel. Ez a folyamat hozzáadja az idő kép hozzáadásakor viszont helyet takarít, és lerövidíti az üzembe helyezés ilyen rendszerképpel. Új képek csak érvényes átalakítás. Meglévő lemezképek nem változnak.

## <a name="next-steps"></a>További lépések

* [Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
* [Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)
