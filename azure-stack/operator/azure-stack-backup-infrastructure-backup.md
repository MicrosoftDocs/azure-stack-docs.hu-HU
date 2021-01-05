---
title: Azure Stack hub-adatok helyreállítása a Infrastructure Backup szolgáltatással
description: Megtudhatja, hogyan készíthet biztonsági mentést és visszaállítást Azure Stack hub konfigurációs és szolgáltatási adatairól a Infrastructure Backup szolgáltatás használatával.
author: PatAltimore
ms.topic: article
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 9d2105138501bfabe1c1244576208d485ff2d875
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871667"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>Azure Stack hub adatainak helyreállítása a Infrastructure Backup szolgáltatással

A konfigurációs és a szolgáltatási adatai biztonsági mentését és visszaállítását az Azure Stack hub Infrastructure Backup szolgáltatás használatával végezheti el. Minden Azure Stack hub-telepítés a szolgáltatás egy példányát tartalmazza. A szolgáltatás által létrehozott biztonsági másolatokat a Azure Stack hub-felhő újratelepítésére használhatja az identitás-, biztonsági és Azure Resource Manager adatok visszaállításához.

Engedélyezze a biztonsági mentést, ha készen áll a felhő éles környezetben való üzembe helyezésére. Ne engedélyezze a biztonsági mentést, ha hosszú időn keresztül szeretné elvégezni a tesztelést és az érvényesítést.

A biztonsági mentési szolgáltatás engedélyezése előtt ellenőrizze, hogy teljesülnek [-e a követelmények](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> A Infrastructure Backup szolgáltatás nem tartalmazza a felhasználói és az alkalmazási szolgáltatásokat. A IaaS VM-alapú alkalmazások védelemével kapcsolatos további információkért lásd: [Azure stack hub-ban üzembe helyezett virtuális gépek elleni védelem](../user/azure-stack-manage-vm-protect.md). Az alkalmazások Azure Stack hub-on való védelemének átfogó megismeréséhez tekintse meg az [üzletmenet-folytonossággal és a vész-helyreállítási tanulmányokkal foglalkozó Azure stack hub-megfontolásokat](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás

A szolgáltatás a következő funkciókat tartalmazza:

| Szolgáltatás                                            | Leírás                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentési infrastruktúra-szolgáltatások                     | A biztonsági mentés koordinálása a Azure Stack hub infrastruktúra-szolgáltatásainak egy részhalmazán keresztül. Vészhelyzet esetén az újratelepítés részeként visszaállíthatók az adatmennyiség. |
| Az exportált biztonsági mentési adatmennyiség tömörítése és titkosítása | A biztonsági mentési információk tömörítése és titkosítása a rendszer által a rendszergazda által biztosított külső tárolóhelyre való exportálás előtt történik.                |
| Biztonsági mentési feladatok figyelése                              | A System figyelmeztetést küld, ha a biztonsági mentési feladatok meghiúsulnak, és elhárítják a problémát.                                                                                                |
| Biztonsági mentési kezelési élmény                       | A Backup RP támogatja a biztonsági mentést.                                                                                                                         |
| Felhőbeli helyreállítás                                     | Ha katasztrofális adatvesztést okoz, a biztonsági mentések segítségével visszaállíthatja a központi Azure Stack hub-adatokat az üzembe helyezés részeként.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás követelményeinek ellenőrzése

- **Tárolási hely**  
  Egy olyan fájlmegosztás szükséges, amely Azure Stack hub-ból érhető el, amely 14 biztonsági mentést tartalmaz. Az egyes biztonsági másolatok körülbelül 10 GB méretűek. A fájlmegosztás 140 GB-nyi biztonsági mentést képes tárolni. További információ a Infrastructure Backup szolgáltatás tárolási helyének kiválasztásáról: a [biztonsági mentési vezérlőre vonatkozó követelmények](azure-stack-backup-reference.md#backup-controller-requirements).
- **Hitelesítő adatok**  
  Szüksége lesz egy tartományi felhasználói fiókra és a hitelesítő adatokra. Használhatja például az Azure Stack hub rendszergazdai hitelesítő adatait.
- **Titkosítási tanúsítvány**  
  A biztonságimásolat-fájlok titkosítása a tanúsítvány nyilvános kulcsával történik. Ügyeljen arra, hogy a tanúsítványt biztonságos helyen tárolja. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [engedélyezheti az Azure stack hub biztonsági mentését a felügyeleti portálról](azure-stack-backup-enable-backup-console.md).

Megtudhatja, hogyan [engedélyezheti az Azure stack hub biztonsági mentését a PowerShell](azure-stack-backup-enable-backup-powershell.md)-lel.

Megtudhatja, hogyan [készíthet biztonsági másolatot Azure stack hub](azure-stack-backup-back-up-azure-stack.md)-ról.

Megtudhatja, hogyan [állíthatja helyre a katasztrofális adatvesztést](azure-stack-backup-recover-data.md).
