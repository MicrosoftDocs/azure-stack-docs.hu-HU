---
title: Azure Stack hub Marketplace – áttekintés
description: Az Azure Stack hub piactér és a piactér elemeinek áttekintése.
author: sethmanheim
ms.topic: article
ms.date: 07/27/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: b9f01cb8237a3a6764b6ce450e279700a17eea01
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250809"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Azure Stack hub Marketplace – áttekintés

Azure Stack hub Marketplace az Azure Stack hub-hoz testreszabott szolgáltatások, alkalmazások és erőforrások gyűjteménye. Az erőforrások közé tartoznak a hálózatok, a virtuális gépek (VM-EK), a tárolás és egyebek. Az Azure Stack hub Marketplace használatával új erőforrásokat hozhat létre, és új alkalmazásokat telepíthet, vagy tallózással és kiválaszthatja a használni kívánt elemeket. Marketplace-elem használatához a felhasználóknak elő kell fizetniük egy olyan ajánlatra, amely hozzáférést biztosít számukra az adott elemhez.

Azure Stack hub-kezelőként eldöntheti, hogy mely elemeket kívánja hozzáadni (közzétenni) a Azure Stack hub piactéren. Többek között az adatbázisok, az App Services és más elemek is közzétehető. A közzététel minden felhasználó számára láthatóvá teszi az elemeket. Közzéteheti az Ön által létrehozott egyéni elemeket, de az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)egyre bővülő listájából is közzétehet elemeket. Amikor közzétesz egy elemeket Azure Stack hub piactéren, a felhasználók öt percen belül láthatják.

> [!CAUTION]  
> A gyűjtemény összes összetevője, beleértve a képeket és a JSON-fájlokat, hitelesítés nélkül elérhető, miután elérhetővé tenné őket az Azure Stack hub piactéren. Az egyéni Piactéri elemek közzétételekor további szempontokat a [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című témakörben talál.

A piactér megnyitásához a felügyeleti portálon válassza az **+ erőforrás létrehozása**lehetőséget.

![Erőforrás létrehozása Azure Stack hub felügyeleti portálján](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-elemek

Az Azure Stack hub Marketplace-elem olyan szolgáltatás, alkalmazás vagy erőforrás, amelyet a felhasználók letölthetnek és használhatnak. Minden Azure Stack hub Marketplace-elem látható az összes felhasználó számára, beleértve az olyan felügyeleti elemeket is, mint a csomagok és ajánlatok. Ezekhez a felügyeleti elemekhez nem szükséges előfizetést megtekinteni, de nem működőképesek a felhasználók számára.

Minden Piactéri tétel:

* Az erőforrás-kiépítés Azure Resource Manager sablonja.
* Metaadatok, például karakterláncok, ikonok és egyéb marketing-biztosítékok.
* Az elemek a portálon való megjelenítéséhez szükséges formázási információk.

Az Azure Stack hub piactéren közzétett minden elem az Azure Gallery-csomag (. azpkg) formátumát használja. Az üzembe helyezési vagy futtatókörnyezeti erőforrások (code,. zip fájlok szoftverrel vagy virtuálisgép-lemezképekkel) külön Azure Stack hub-ra, nem a Piactéri elem részeként.

A 1803-es és újabb verziókban a Azure Stack hub a képeket az Azure-ból való letöltéskor vagy Egyéni rendszerképek feltöltésekor a ritka fájlokra konvertálja. Ez a folyamat időt vesz igénybe egy kép hozzáadásakor, de helyet takarít meg, és felgyorsítja a rendszerképek üzembe helyezését. Az átalakítás csak az új lemezképekre vonatkozik. A meglévő lemezképek nem változnak.

## <a name="next-steps"></a>Következő lépések

* [Meglévő Piactéri elemek letöltése az Azure-ból és közzététel Azure Stack hubhoz](azure-stack-download-azure-marketplace-item.md)  
* [Egyéni Azure Stack hub Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
