---
title: Helyreállítás az Azure Stack katasztrofális adatvesztéssel kapcsolatban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja helyre és állíthatja vissza az infrastruktúra-adatokat Azure Stack a katasztrofális adatvesztés után.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fd7c1ede611f9505ed48c8efc3caa5311c285cd3
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342862"
---
# <a name="recover-from-catastrophic-data-loss"></a>Végzetes adatvesztés utáni helyreállítás

*Vonatkozik: Azure Stack integrált rendszerek.*

A Azure Stack az Azure-szolgáltatásokat futtatja az adatközpontban, és a környezetekben olyan kicsi, mint négy, egyetlen állványra telepített csomópont. Ezzel szemben az Azure több mint 40 régióban fut több adatközpontban, és az egyes régiókban több zóna is működik. A felhasználói erőforrások több kiszolgálóra, állványra, adatközpontra és régióra is kiterjedhetnek. A Azure Stack használatával jelenleg csak egyetlen állványon helyezhető üzembe a teljes felhő. Ez a korlátozás teszi lehetővé a felhő számára az adatközpontban fellépő katasztrofális események kockázatát, vagy a súlyos termék hibái miatti hibákat. Katasztrófa esetén a Azure Stack-példány offline állapotba kerül. Az összes adatok valószínűleg nem állíthatók helyre.

Az adatvesztés gyökerétől függően előfordulhat, hogy egy infrastruktúra-szolgáltatást kell kijavítania, vagy a teljes Azure Stack példányt kell visszaállítani. Előfordulhat, hogy a másik hardverre is vissza kell állítania ugyanazon a helyen, vagy egy másik helyen.

Ez a forgatókönyv a teljes telepítés helyreállítására vonatkozik, ha hiba történt a privát felhő újratelepítése közben.

| Forgatókönyv                                                           | Adatvesztés                            | Megfontolandó szempontok                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Katasztrófa vagy termék meghibásodása miatti katasztrofális adatvesztéssel. | Minden infrastruktúra-és felhasználói és alkalmazásadatok. | A felhasználói alkalmazások és adatok védelme külön történik az infrastruktúra-adatoktól. |

## <a name="workflows"></a>Workflows

Az Azure Start védelmének megkezdése az infrastruktúra és az alkalmazás-és bérlői adatok külön biztonsági mentésével kezdődik. Ez a dokumentum az infrastruktúra elleni védelemre vonatkozik. 

![Adathelyreállítási munkafolyamat Azure Stack – üzembe helyezés](media/azure-stack-backup/azure-stack-backup-workflow1.png)

A legrosszabb esetben, amikor az összes adatvesztés történik, a helyreállítás Azure Stack az a folyamat, amelynek során a rendszer a Azure Stack és az összes felhasználói adatok számára egyedi infrastruktúra-adatok visszaállítását hajtja végre. 

![Adathelyreállítási munkafolyamat Azure Stack – újratelepítés](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Visszaállítás

Ha katasztrofális adatvesztés történik, de a hardver továbbra is használható, Azure Stack újratelepítése szükséges. Az újratelepítés során megadhatja a biztonsági mentések eléréséhez szükséges tárolási helyet és hitelesítő adatokat. Ebben a módban nem kell megadnia azokat a szolgáltatásokat, amelyeket vissza kell állítani. Infrastructure Backup vezérlő a telepítési munkafolyamat részeként beadja a vezérlési sík állapotát.

Ha van olyan katasztrófa, amely használhatatlanná teszi a hardvert, az újbóli üzembe helyezés csak az új hardvereken lehetséges. Az újraüzembe helyezés több hétig is eltarthat, amíg a rendszer kicseréli a hardvert, és megérkezik az adatközpontba. A vezérlési sík-adatkészletek visszaállítása bármikor lehetséges. A visszaállítás azonban nem támogatott, ha az újratelepített példány verziója több, mint az utolsó biztonsági mentésben használt verziónál nagyobb verzió.

| Üzembe helyezési mód | Kezdőpont | Végpont                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tiszta telepítés   | Alapterv létrehozása | Az OEM telepíti a Azure Stack és a frissítéseket a legújabb támogatott verzióra.                                                                                                                                          |
| Helyreállítási mód   | Alapterv létrehozása | A SZÁMÍTÓGÉPGYÁRTÓ a Azure Stack helyreállítási módban telepíti, és a rendelkezésre álló legújabb biztonsági mentés alapján kezeli a verziószámmal egyező követelményeket. A SZÁMÍTÓGÉPGYÁRTÓ a legújabb támogatott verzióra való frissítéssel befejezi a telepítést. |

## <a name="data-in-backups"></a>Biztonsági másolatok adatai

Azure Stack támogatja a Cloud Recovery Mode nevű üzembe helyezési típust. Ezt a módot csak akkor használja a rendszer, ha úgy dönt, hogy helyreállítja a Azure Stack egy katasztrófa vagy termék meghibásodása után, hogy a megoldás helyreállíthatatlanul lett kiképezve. Ez a telepítési mód nem állítja helyre a megoldásban tárolt felhasználói adatokat. Ennek a telepítési módnak a hatóköre csak a következő adatmennyiségek visszaállítására korlátozódik:

 - Üzembe helyezési bemenetek
 - Belső identitás-szolgáltatási adatok (ADFS-telepítések).
 - Összevont azonosítási konfiguráció (ADFS-telepítések).
 - A belső hitelesítésszolgáltató által használt főtanúsítványok.
 - Azure Resource Manager a konfigurációs felhasználói adatok, például az előfizetések, a csomagok, az ajánlatok, a tárolási kvóták, a hálózati kvóták és a számítási erőforrások.
 - Key Vault a titkokat és a tárolókat.
 - RBAC szabályzat-hozzárendelések és szerepkör-hozzárendelések.

Az üzembe helyezés során a rendszer a felhasználói infrastruktúra (IaaS) vagy a platform as Service (szolgáltatásként szolgáló) erőforrásait sem állítja vissza. Ezek a veszteségek közé tartoznak a IaaS virtuális gépek, a Storage-fiókok, a Blobok, a táblák, a hálózati konfiguráció stb. A Felhőbeli helyreállítás célja annak biztosítása, hogy az operátorok és a felhasználók a telepítés befejezése után vissza tudják jelentkezni a portálra. A-ban bejelentkezett felhasználók nem látják az erőforrásaikat. A felhasználók előfizetései visszaállíthatók, és a rendszergazda által meghatározott eredeti csomagok, ajánlatok és szabályzatok. Azok a felhasználók, akik visszajelentkeznek a rendszerben, a katasztrófa előtt az eredeti megoldás által megszabott korlátozásokkal működnek. A Felhőbeli helyreállítás befejezése után az operátor manuálisan állíthatja vissza a Value-Add és a harmadik féltől származó RPs és kapcsolódó adatmennyiséget.

## <a name="next-steps"></a>További lépések

Ismerje meg az [Infrastructure Backup szolgáltatás használatának](azure-stack-backup-best-practices.md)ajánlott eljárásait.
