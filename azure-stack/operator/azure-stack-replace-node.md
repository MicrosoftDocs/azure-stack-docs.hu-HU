---
title: Méretezési egység csomópontjának cseréje egy Azure Stack integrált rendszeren | Microsoft Docs
description: Megtudhatja, hogyan helyettesíthet egy fizikai méretezési egység csomópontot egy Azure Stack integrált rendszeren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 35f350628a5c13e26bf6f3d1c931aa78a05bfb53
ms.sourcegitcommit: 159da88a52701679571bbedde1c36b72bbfe32dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380495"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Méretezési egység csomópontjának cseréje Azure Stack integrált rendszeren

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk azt ismerteti, hogyan lehet lecserélni egy fizikai számítógépet (más néven a méretezési egység csomópontját) egy Azure Stack integrált rendszeren. A tényleges méretezési egység csomópontjának helyettesítő lépései az eredeti berendezésgyártó (OEM) hardvergyártó alapján változnak. Az Ön rendszerére vonatkozó részletes lépésekért tekintse meg a forgalmazó helyszínen cserélhető egységekkel (FRU-k) kapcsolatos dokumentációját.

> [!CAUTION]  
> A belső vezérlőprogram-simítás kritikus fontosságú a cikkben ismertetett művelet sikeressége szempontjából. Ez a lépés hiányzik a rendszer instabillá, a teljesítmény csökkenése, a biztonsági szálak vagy a Azure Stack Automation az operációs rendszer központi telepítésének megakadályozása érdekében. Mindig tekintse meg a hardveres partner dokumentációját, amikor lecseréli a hardvert, hogy az alkalmazott belső vezérlőprogram megfelel a [Azure stack felügyeleti portálon](azure-stack-updates.md)megjelenő OEM-verziónak.<br>
További információt és a partneri dokumentációra mutató hivatkozásokat a [hardver-összetevők cseréje](azure-stack-replace-component.md)című témakörben talál.

A következő folyamatábra a teljes méretezési egység csomópontjának cseréjére szolgáló általános cserélhető folyamatot mutatja be.

![Folyamatábra a csomópontok cseréjéhez](media/azure-stack-replace-node/replacenodeflow.png)

\* Ez a művelet nem szükséges a hardver fizikai állapota alapján.

> [!Note]  
> Ha a leállítási művelet meghiúsul, javasolt a leállítási művelet, majd a Leállítás művelet használata. További részletek: elérhető csomópont-műveletek  

## <a name="review-alert-information"></a>Riasztási információk áttekintése

Ha a skálázási egység csomópontja nem érhető el, a következő kritikus riasztások jelennek meg:

- A csomópont nincs hálózati vezérlőhöz csatlakoztatva
- A csomópont nem érhető el a virtuális gép elhelyezéséhez
- A skálázásiegység-csomópont offline állapotban van

![A skálázási egységre vonatkozó riasztások listája](media/azure-stack-replace-node/nodedownalerts.png)

Ha megnyitja a **méretezési egység csomópontot offline** riasztás, a riasztás leírása a nem elérhető méretezési egység csomópontot tartalmazza. További riasztásokat is kaphat a hardveres életciklus-gazdagépen futó OEM-specifikus figyelési megoldásban.

![Csomópont kapcsolat nélküli riasztásának részletei](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Méretezési egység csomópontjának helyettesítési folyamata

Az alábbi lépések a méretezési egység csomópont-helyettesítési folyamatának magas szintű áttekintését nyújtanak. A rendszerre vonatkozó részletes lépésekért tekintse meg az OEM hardver gyártójának cserélhető szoftveres dokumentációját. Ne kövesse ezeket a lépéseket a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációra való hivatkozás nélkül.

1. A leállítási művelettel könnyedén leállíthatja a skálázási egység csomópontját. Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges. 

2. Ha nem valószínű, hogy a leállítási művelet meghiúsul, a [kiürítési](azure-stack-node-actions.md#drain) művelettel állítsa be a méretezési egység csomópontját karbantartási módba. Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

   > [!NOTE]  
   > Mindenesetre csak egy csomópontot lehet letiltani és kikapcsolni a S2D (Közvetlen tárolóhelyek) megszakítása nélkül.

3. Miután a méretezési egység csomópontja karbantartási módban van, használja a [Leállítás](azure-stack-node-actions.md#stop) műveletet. Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

   > [!NOTE]  
   > Ha nem valószínű, hogy a kikapcsolás művelet nem működik, használja helyette a alaplapi felügyeleti vezérlő (BMC) webes felületét.

4. Cserélje le a fizikai számítógépet. Ezt általában az OEM-hardver gyártója végzi.
5. Az új fizikai számítógép a méretezési egységhez való hozzáadásához használja a [javítási](azure-stack-node-actions.md#repair) műveletet.
6. A rendszerjogosultságú végponton [ellenőrizhető a virtuális lemez javításának állapota](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Az új adatmeghajtók esetében a teljes tárterület-javítási feladat több órát is igénybe vehet a rendszerterheléstől és a felhasznált területtől függően.
7. A javítási művelet befejeződése után ellenőrizze, hogy az összes aktív riasztás automatikusan le van-e zárva.

## <a name="next-steps"></a>További lépések

- A fizikai lemezek a rendszer bekapcsolásakor történő cseréjével kapcsolatos információkért lásd: [lemez cseréje](azure-stack-replace-disk.md). 
- További információ a rendszer kikapcsolását igénylő hardver-összetevők lecseréléséről: [hardver-összetevő cseréje](azure-stack-replace-component.md).
