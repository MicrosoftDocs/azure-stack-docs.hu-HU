---
title: Azure Stack Marketplace – áttekintés | Microsoft Docs
description: Azure Stack Marketplace-és piactér-elemek áttekintése.
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
ms.openlocfilehash: da8fdf20446e5ed60271e236f44939d439118b4b
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534091"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace – áttekintés

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a piactér a Azure Stack számára testre szabott szolgáltatások, alkalmazások és erőforrások gyűjteménye. Az erőforrások közé tartoznak a hálózatok, a virtuális gépek (VM-EK), a tárolás és egyebek. Azure Stack piactéren új erőforrásokat hozhat létre, és új alkalmazásokat telepíthet, vagy tallózással és kiválaszthatja a használni kívánt elemeket. Marketplace-elem használatához a felhasználóknak elő kell fizetniük egy olyan ajánlatra, amely hozzáférést biztosít számukra az elemhez.

Azure Stack kezelőként el kell döntenie, hogy mely elemeket kívánja hozzáadni (közzétenni) a piactéren Azure Stack. Többek között az adatbázisok, az App Services és más elemek is közzétehető. A közzététel minden felhasználó számára láthatóvá teszi az elemeket. Közzéteheti az Ön által létrehozott egyéni elemeket, de az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)egyre bővülő listájából is közzétehet elemeket. Amikor közzétesz egy elemeket Azure Stack piactéren, a felhasználók öt percen belül láthatják.

> [!CAUTION]  
> A gyűjtemény összes összetevője, beleértve a lemezképeket és a JSON-fájlokat, hitelesítés nélkül, a Azure Stack piactéren elérhetővé tételével elérhető. Az egyéni Piactéri elemek közzétételekor további szempontokat a [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című témakörben talál.

A piactér megnyitásához a felügyeleti portálon válassza az **+ erőforrás létrehozása**lehetőséget.

![Erőforrás létrehozása Azure Stack felügyeleti portálon](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-elemek

A Azure Stack Marketplace-elem olyan szolgáltatás, alkalmazás vagy erőforrás, amelyet a felhasználók letölthetnek és használhatnak. Minden Azure Stack Marketplace-elem látható az összes felhasználó számára, beleértve az olyan felügyeleti elemeket is, mint a csomagok és ajánlatok. Ezekhez a felügyeleti elemekhez nem szükséges előfizetést megtekinteni, de nem működőképesek a felhasználók számára.

Minden Piactéri tétel:

* Az erőforrás-kiépítés Azure Resource Manager sablonja.
* Metaadatok, például karakterláncok, ikonok és egyéb marketing-biztosítékok.
* Az elemek a portálon való megjelenítéséhez szükséges formázási információk.

Azure Stack piactéren közzétett minden elem az Azure Gallery-csomag (. azpkg) formátumát használja. Az üzembe helyezési vagy futásidejű erőforrások (code,. zip fájlok szoftverrel vagy virtuálisgép-lemezképekkel) Azure Stack külön, nem a piactér elem részeként.

A 1803-es és újabb verziókban a Azure Stack a képeket a ritka fájlokra konvertálja, amikor letölti az Azure-ból, vagy egyéni rendszerképeket tölt fel. Ez a folyamat időt vesz igénybe egy kép hozzáadásakor, de helyet takarít meg, és felgyorsítja a rendszerképek üzembe helyezését. Az átalakítás csak az új lemezképekre vonatkozik. A meglévő lemezképek nem változnak.

## <a name="next-steps"></a>Következő lépések

* [Meglévő Piactéri elemek letöltése az Azure-ból és közzététel a Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [Egyéni Azure Stack Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
