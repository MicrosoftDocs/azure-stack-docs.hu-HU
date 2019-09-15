---
title: Azure Stack adatok helyreállítása a Infrastructure Backup szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan készíthet biztonsági mentést és visszaállítást Azure Stack konfigurációs és szolgáltatási adatairól a Infrastructure Backup szolgáltatás használatával.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 832d2146e79f3724c5f1b3bf9da1776388636da8
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974702"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>Azure Stack adatok helyreállítása a Infrastructure Backup szolgáltatással

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack Infrastructure Backup szolgáltatással biztonsági mentést készíthet és visszaállíthatja a konfigurációs és a szolgáltatási adataikat. Minden Azure Stack-telepítés a szolgáltatás egy példányát tartalmazza. A szolgáltatás által létrehozott biztonsági másolatokat a Azure Stack felhő újratelepítésére használhatja az identitás-, a biztonsági és a Azure Resource Manager adatok visszaállításához.

Engedélyezze a biztonsági mentést, ha készen áll a felhő éles környezetben való üzembe helyezésére. Ne engedélyezze a biztonsági mentést, ha hosszú időn keresztül szeretné elvégezni a tesztelést és az érvényesítést.

A biztonsági mentési szolgáltatás engedélyezése előtt ellenőrizze, hogy teljesülnek [-e a követelmények](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> A Infrastructure Backup szolgáltatás nem tartalmazza a felhasználói és az alkalmazási szolgáltatásokat. A IaaS VM-alapú alkalmazások védelemével kapcsolatos további információkért lásd: [Azure stack üzembe helyezett virtuális gépek elleni védelem](../user/azure-stack-manage-vm-protect.md). Az alkalmazások Azure Stackon való védelemének átfogó megismeréséhez tekintse meg az [üzletmenet folytonosságával és a vész-helyreállítási tanulmányokkal kapcsolatos Azure stack szempontokat](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás

A szolgáltatás a következő funkciókat tartalmazza:

| Funkció                                            | Leírás                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentési infrastruktúra-szolgáltatások                     | A biztonsági mentés koordinálása a Azure Stack infrastruktúra-szolgáltatások egy részhalmazán keresztül. Vészhelyzet esetén az újratelepítés részeként visszaállíthatók az adatmennyiség. |
| Az exportált biztonsági mentési adatmennyiség tömörítése és titkosítása | A biztonsági mentési információk tömörítése és titkosítása a rendszer által a rendszergazda által biztosított külső tárolóhelyre való exportálás előtt történik.                |
| Biztonsági mentési feladatok figyelése                              | A System figyelmeztetést küld, ha a biztonsági mentési feladatok meghiúsulnak, és elhárítják a problémát.                                                                                                |
| Biztonsági mentési kezelési élmény                       | A Backup RP támogatja a biztonsági mentést.                                                                                                                         |
| Felhőbeli helyreállítás                                     | Ha katasztrofális adatvesztést okoz, a biztonsági mentések segítségével visszaállíthatja a központi Azure Stacki adatokat az üzembe helyezés részeként.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>A Infrastructure Backup szolgáltatás követelményeinek ellenőrzése

- **Tárolási hely**  
  Szükség van egy olyan fájlmegosztás elérésére Azure Stack, amely hét biztonsági mentést tartalmazhat. Az egyes biztonsági másolatok körülbelül 10 GB méretűek. A megosztásnak képesnek kell lennie 140 GB biztonsági mentés tárolására. További információ a Infrastructure Backup szolgáltatás tárolási helyének kiválasztásáról: a [biztonsági mentési vezérlőre vonatkozó követelmények](azure-stack-backup-reference.md#backup-controller-requirements).
- **Hitelesítő adatok**  
  Szüksége lesz egy tartományi felhasználói fiókra és a hitelesítő adatokra. Használhatja például a Azure Stack rendszergazdai hitelesítő adatait.
- **Titkosítási tanúsítvány**  
  A biztonságimásolat-fájlok titkosítása a tanúsítvány nyilvános kulcsával történik. Ügyeljen arra, hogy a tanúsítványt biztonságos helyen tárolja. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [engedélyezheti Azure stack biztonsági mentését a felügyeleti portálról](azure-stack-backup-enable-backup-console.md).

Megtudhatja, hogyan [engedélyezheti a Azure stack biztonsági mentését a PowerShell használatával](azure-stack-backup-enable-backup-powershell.md).

További információ a [Azure stack biztonsági mentéséről](azure-stack-backup-back-up-azure-stack.md).

Megtudhatja, hogyan [állíthatja helyre a katasztrofális adatvesztést](azure-stack-backup-recover-data.md).
