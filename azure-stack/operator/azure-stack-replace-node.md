---
title: Cserélje le a skálázási egység csomópont az Azure Stackkel integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, hogyan cserélje le az Azure Stackkel integrált rendszerek fizikai skálázási egység csomópontjait.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 78fe71768b83082e152685f7fe4c7cc606ae5b3c
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469204"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Cserélje le az Azure Stackkel integrált rendszerek a skálázási egység csomópont

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti az általános folyamat helyett egy fizikai számítógépen (más néven a skálázási egység csomópont) az Azure Stackkel integrált rendszereknél. Tényleges skálázási egység csomópont helyettesítő lépései eltérőek lesznek a számítógépgyártó (OEM) hardver szállítójával alapján. Konkrétan a rendszer a részletes lépéseket a gyártója által biztosított mező telepen cserélhető egység (FRU) dokumentációjában talál.

Az alábbi folyamatábrája bemutatja cserélje le a teljes méretezési egység csomópont FRU általános folyamata.

![Cserélje le a csomópont folyamatot folyamatábra](media/azure-stack-replace-node/replacenodeflow.png)

* Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

> [!Note]  
> A leállítási művelet végrehajtása nem sikerül, ha a kiürítési művelet követ a leállítási művelet használata ajánlott. További információ az elérhető csomópont műveletek:  

## <a name="review-alert-information"></a>Tekintse át a riasztás adatai

Ha egy méretezési egység csomópont nem működik, kapni fog a következő kritikus riasztások:

- Nem csatlakozik a hálózati vezérlő csomópont
- A csomópont nem érhető el a virtuálisgép-elhelyezés
- Skálázási egység csomópont offline állapotban.

![A skálázási egység le riasztások listája](media/azure-stack-replace-node/nodedownalerts.png)

Ha megnyitja a **skálázási egység csomópont offline állapotban** riasztást, a riasztás leírásában a skálázási egység csomópontot tartalmaz, amely nem érhető el. Az OEM-specifikus figyelési megoldás, amely a hardver életciklus gazdagépen fut is kaphat további riasztásokat.

![A csomópont offline riasztás részletei](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Skálázási egység csomópont cseréjét.

A következő lépések szolgálnak, a skálázási egység csomópont cseréjét magas szintű áttekintését. A részletes lépéseket, amelyek az adott rendszerhez a OEM hardver szállítójával FRU dokumentációjában talál. Ne kövesse ezeket a lépéseket az OEM által biztosított dokumentációt hivatkozó nélkül.

1. Használja a **leállítási** szabályosan leállítja a méretezési egység csomópont művelet. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján. 

2. Az a valószínűtlen eset a leállítási művelet meghiúsul, használja a [kiürítési](azure-stack-node-actions.md#drain) műveletet a skálázási egység csomópont karbantartási módba. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

   > [!NOTE]  
   > Minden esetben csak egy csomópont is le van tiltva és ki van kapcsolva egy időben az S2D megszakítása nélkül (a közvetlen tárolóhelyek).

3. Miután a skálázási egység csomópont karbantartási módban van, a [leállítása](azure-stack-node-actions.md#stop) művelet. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

   > [!NOTE]  
   > A valószínűtlen eset, hogy a művelet kikapcsolás nem működik használja helyette a alaplapi felügyeleti vezérlőnek (BMC) webes felületén.

4. Cserélje le a fizikai számítógép. Általában ez történik a számítógép-Gyártói hardver gyártója által.
5. Használja a [javítási](azure-stack-node-actions.md#repair) műveleteket kíván hozzáadni az új fizikai számítógépet a skálázási egység.
6. Használja a kiemelt végponthoz [Virtuálislemez-javítási állapotának ellenőrzéséhez](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Az új adatok meghajtókat egy teljes helyreállítási feladat rendszerterheléstől függően több órát is igénybe vehet, és a felhasznált lemezterület.
7. A javítási művelet befejezése után, ellenőrizze, hogy az összes aktív riasztás automatikusan lezárták.

## <a name="next-steps"></a>További lépések

- További információ a fizikai lemez cseréje, amíg a rendszer ki van-e kapcsolva: [olyan lemezt cserél ki](azure-stack-replace-disk.md). 
- További információ a rendszer kikapcsolásának igénylő hardverkomponensek cseréje: [hardverkomponensek cseréje](azure-stack-replace-component.md).
