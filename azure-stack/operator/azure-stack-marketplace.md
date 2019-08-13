---
title: Egyéni Piactéri elemek közzététele Azure Stack (Cloud Operator) | Microsoft Docs
description: Azure Stack operátorként megtudhatja, hogyan tehet közzé egyéni Piactéri elemeket a Azure Stackban.
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: be62ddc3c3e5ea7180164cac95edd125030cbc6a
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959497"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace – áttekintés

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack piactér a Azure Stack számára testre szabott szolgáltatások, alkalmazások és erőforrások gyűjteménye. Az erőforrások közé tartoznak a hálózatok, a virtuális gépek, a tárolók és egyebek. A piactér használatával új erőforrásokat hozhat létre, és új alkalmazásokat telepíthet; Tallózással keresse meg és válassza ki a használni kívánt elemeket. Marketplace-elem használatához a felhasználóknak elő kell fizetniük egy olyan ajánlatra, amely hozzáférést biztosít számukra az elemhez.

Azure Stack operátorként eldöntheti, hogy mely elemeket kívánja hozzáadni (közzétenni) a piactéren. Olyan elemeket tehet közzé, mint például az adatbázisok, App Services és sok más. A közzététel minden felhasználó számára láthatóvá teszi az elemeket. Közzéteheti az Ön által létrehozott egyéni elemeket, de az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)egyre bővülő listájából is közzétehet elemeket. Amikor közzétesz egy tételt a piactéren, a felhasználók öt percen belül láthatják.

> [!CAUTION]  
> A gyűjtemény összes összetevője, beleértve a képeket és a JSON-fájlokat, hitelesítés nélkül elérhető, miután elérhetővé tenné őket a Azure Stack piactéren. Az egyéni Piactéri elemek közzétételekor további szempontokat a [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című témakörben talál.

A piactér megnyitásához a felügyeleti portálon válassza az **+ erőforrás létrehozása**lehetőséget.

![Marketplace](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-elemek

A Azure Stack Marketplace-elem olyan szolgáltatás, alkalmazás vagy erőforrás, amelyet a felhasználók letölthetnek és használhatnak. Minden Azure Stack Marketplace-elem látható az összes felhasználó számára, beleértve az olyan felügyeleti elemeket is, mint a csomagok és ajánlatok. Ezekhez a felügyeleti elemekhez nem szükséges előfizetést megtekinteni, de nem működőképesek a felhasználók számára.

Minden Piactéri tétel:

* Az erőforrás-kiépítés Azure Resource Manager sablonja.
* Metaadatok, például karakterláncok, ikonok és egyéb marketing-biztosítékok.
* Az elemek a portálon való megjelenítéséhez szükséges formázási információk.

A piactéren közzétett minden elem az Azure Gallery-csomag (. azpkg) formátumát használja. Az üzembe helyezési vagy futásidejű erőforrások (code,. zip fájlok szoftverrel vagy virtuálisgép-lemezképekkel) Azure Stack külön, nem a piactér elem részeként.

A 1803-es és újabb verziókban a Azure Stack a képeket a ritka fájlokra konvertálja, amikor letölti az Azure-ból, vagy egyéni rendszerképeket tölt fel. Ez a folyamat időt vesz igénybe egy kép hozzáadásakor, de helyet takarít meg, és felgyorsítja a rendszerképek üzembe helyezését. Az átalakítás csak az új lemezképekre vonatkozik. A meglévő lemezképek nem változnak.

## <a name="next-steps"></a>További lépések

* [Marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)  
* [Marketplace-termék létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
