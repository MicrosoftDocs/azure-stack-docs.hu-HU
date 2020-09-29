---
title: Azure Stack hub katasztrofális adatvesztésének helyreállítása
description: Megtudhatja, hogyan állíthatja helyre és állíthatja vissza az infrastruktúra-adatokat Azure Stack központban a katasztrofális adatvesztést követően.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7f57336cdb15be2f55fdcd3756f5f3714f51b1af
ms.sourcegitcommit: d197e8d3c3b69c20d09de4c43d8089ec0a993baf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90836505"
---
# <a name="recover-from-catastrophic-data-loss"></a>Helyreállítás végzetes adatvesztés esetén

Azure Stack hub az Azure-szolgáltatásokat futtatja az adatközpontban, és a környezetekben akár négy, egyetlen állványra telepített csomópontként is futtatható. Ezzel szemben az Azure több mint 40 régióban fut több adatközpontban, és az egyes régiókban több zóna is működik. A felhasználói erőforrások több kiszolgálóra, állványra, adatközpontra és régióra is kiterjedhetnek. Azure Stack hub esetében jelenleg csak a teljes felhő üzembe helyezésére van lehetősége egyetlen állványon. Ez a korlátozás teszi lehetővé a felhő számára az adatközpontban fellépő katasztrofális események kockázatát, vagy a súlyos termék hibái miatti hibákat. Katasztrófa esetén a Azure Stack hub-példány offline állapotba kerül. Az összes adatok valószínűleg nem állíthatók helyre.

Az adatvesztés gyökerétől függően előfordulhat, hogy egyetlen infrastruktúra-szolgáltatást kell kijavítania, vagy a teljes Azure Stack hub-példányt kell visszaállítani. Előfordulhat, hogy a másik hardverre is vissza kell állítania ugyanazon a helyen, vagy egy másik helyen.

Ez a forgatókönyv a teljes telepítés helyreállítására vonatkozik, ha hiba történt a privát felhő újratelepítése közben.

| Használati eset                                                           | Adatvesztés                            | Megfontolandó szempontok                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Katasztrófa vagy termék meghibásodása miatti katasztrofális adatvesztéssel. | Minden infrastruktúra-és felhasználói és alkalmazásadatok. | A más OEM-re is visszaállítható.<br/> A a hardver különböző generációja számára állítható vissza.<br/> A visszaállítható a méretezési egység csomópontjainak különböző darabszámára.<br/> A felhasználói alkalmazások és adatok védelme külön történik az infrastruktúra-adatoktól. |

## <a name="workflows"></a>Munkafolyamatok

Az Azure Stack hub védelme az infrastruktúra és az alkalmazás-és bérlői adatok külön biztonsági mentésével kezdődik. Ez a dokumentum az infrastruktúra elleni védelemre vonatkozik. 

![Azure Stack hub adathelyreállítási munkafolyamata – üzembe helyezés](media/azure-stack-backup/azure-stack-backup-workflow1.png)

A legrosszabb esetben, amikor az összes adatvesztés történik, az Azure Stack hub helyreállítása az a folyamat, amellyel a Azure Stack hub és az összes felhasználói adatok üzembe helyezéséhez egyedi infrastrukturális adatok visszaállnak. 

![Azure Stack hub-adathelyreállítási munkafolyamat – újratelepítés](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Visszaállítás

Ha katasztrofális adatvesztés történik, de a hardver továbbra is használható, Azure Stack hub újratelepítése szükséges. Az újratelepítés során megadhatja a biztonsági mentések eléréséhez szükséges tárolási helyet és hitelesítő adatokat. Ebben a módban nem kell megadnia azokat a szolgáltatásokat, amelyeket vissza kell állítani. Infrastructure Backup vezérlő a telepítési munkafolyamat részeként beadja a vezérlési sík állapotát.

Ha van olyan katasztrófa, amely használhatatlanná teszi a hardvert, az újbóli üzembe helyezés csak az új hardvereken lehetséges. Az újraüzembe helyezés több hétig is eltarthat, amíg a rendszer kicseréli a hardvert, és megérkezik az adatközpontba. A vezérlési sík-adatkészletek visszaállítása bármikor lehetséges. A visszaállítás azonban nem támogatott, ha az újratelepített példány verziója több, mint az utolsó biztonsági mentésben használt verziónál nagyobb verzió.

| Üzembe helyezési mód | Kezdőpont | Végpont                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tiszta telepítés   | Alapterv létrehozása | Az OEM telepíti Azure Stack hubot és frissíti a legújabb támogatott verziót.                                                                                                                                          |
| Helyreállítási mód   | Alapterv létrehozása | Az OEM a Azure Stack hubot helyreállítási módban telepíti, és a rendelkezésre álló legújabb biztonsági mentés alapján kezeli a verziószám-megfeleltetési követelményeket. A SZÁMÍTÓGÉPGYÁRTÓ a legújabb támogatott verzióra való frissítéssel befejezi a telepítést. |

## <a name="data-in-backups"></a>Biztonsági másolatok adatai

Azure Stack hub támogatja a Cloud Recovery Mode nevű üzembe helyezési típust. Ezt a módot csak akkor használja a rendszer, ha úgy dönt, hogy egy katasztrófa vagy egy termék meghibásodása után helyreállítja Azure Stack hubot. Ez a telepítési mód nem állítja helyre a megoldásban tárolt felhasználói adatokat. Ennek a telepítési módnak a hatóköre csak a következő adatmennyiségek visszaállítására korlátozódik:

 - Üzembe helyezési bemenetek
 - Belső Identity Service-adatok
 - Összevont azonosítási konfiguráció (ADFS-telepítések).
 - A belső hitelesítésszolgáltató által használt főtanúsítványok.
 - Azure Resource Manager a konfigurációs felhasználói adatok, például az előfizetések, a csomagok, az ajánlatok, az erőforráscsoportok, a címkék, a tárolási kvóták, a hálózati kvóták és a számítási erőforrások.
 - Key Vault a titkokat és a tárolókat.
 - RBAC szabályzat-hozzárendelések és szerepkör-hozzárendelések.

Az üzembe helyezés során a rendszer a felhasználói infrastruktúra (IaaS) vagy a platform as Service (szolgáltatásként szolgáló) erőforrásait sem állítja vissza. Ezek a veszteségek közé tartoznak a IaaS virtuális gépek, a Storage-fiókok, a Blobok, a táblák, a hálózati konfiguráció stb. A Felhőbeli helyreállítás célja annak biztosítása, hogy az operátorok és a felhasználók a telepítés befejezése után vissza tudják jelentkezni a portálra. A-ban bejelentkezett felhasználók nem látják az erőforrásaikat. A felhasználók előfizetései visszaállíthatók, és a rendszergazda által meghatározott eredeti csomagok, ajánlatok és szabályzatok. Azok a felhasználók, akik visszajelentkeznek a rendszerben, a katasztrófa előtt az eredeti megoldás által megszabott korlátozásokkal működnek. A Felhőbeli helyreállítás befejezése után az operátor manuálisan állíthatja vissza a Value-Add és a harmadik féltől származó RPs és kapcsolódó adatmennyiséget.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Infrastructure Backup szolgáltatás használatának](azure-stack-backup-best-practices.md)ajánlott eljárásait.
