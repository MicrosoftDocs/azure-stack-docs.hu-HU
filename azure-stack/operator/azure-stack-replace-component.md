---
title: Az Azure Stack skálázási egység csomóponton hardverkomponensek cseréje |} A Microsoft Docs
description: Útmutató az Azure Stackkel integrált rendszereknél hardverkomponensek cseréje.
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
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: ea316ebdba51ea80fcd02382023ccb46447a8cc0
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984831"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Az Azure Stack skálázási egység csomóponton hardverkomponensek cseréje

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti, amelyek nem ritkáról gyakori elérésű-cserélhető hardverösszetevők helyett általános folyamata. Tényleges cseréje szükséges lépések eltérhetnek a számítógépgyártó (OEM) hardver szállítójával alapján. Az Azure Stackkel integrált rendszereknél vonatkozó részletes lépéseket a gyártója által biztosított mező telepen cserélhető egység (FRU) dokumentációjában talál.

Nem gyakori – cserélhető összetevői a következők:

- CPU*
- Memória *
- Alaplap/alaplapi felügyeleti vezérlőnek (BMC) / video kártya
- Lemez tartományvezérlő/gazdabuszadaptert (HBA) / csatlakozópanel meghibásodása
- Hálózati adapter (NIC)
- Operációs rendszer lemez *
- Adatmeghajtók (meghajtókat, amelyek nem támogatják a gyakori elérésű felcserélés, például PCI-e hozzáadni a kártyák) *

* Ezek az összetevők előfordulhat, hogy támogatják a gyakori elérésű lapozófájl-kapacitás, de a szállító megvalósítási függően változhat. Lépésenkénti útmutató az OEM beszállítói FRU dokumentációjában talál.

Az alábbi folyamatábrája bemutatja az általános FRU folyamat nem ritkáról gyakori elérésű-cserélhető hardverkomponensek cseréje.

![Összetevő helyettesítő folyamatot bemutató folyamatábrája](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

** A támogatási szerződés alapján e OEM hardvergyártójához hajt végre, az összetevő cseréje és a frissítések a belső vezérlőprogram eltérőek lehetnek.

## <a name="review-alert-information"></a>Tekintse át a riasztás adatai

Az Azure Stack állapotának és a monitorozási rendszer nyomon követheti a hálózati adapterek és a közvetlen tárolóhelyek által vezérelt adatmeghajtók állapotát. Egyéb hardverelemek nem követi nyomon. Az összes többi hardverösszetevők riasztások aktiválódnak figyelési megoldás, amely a hardver életciklus gazdagépen fut szállító-specifikus hardver.  

## <a name="component-replacement-process"></a>Összetevő cseréjét.

Az alábbi lépéseket a összetevő cseréjét magas szintű áttekintését adja meg. Ne kövesse ezeket a lépéseket az OEM által biztosított FRU dokumentációját hivatkozó nélkül.

1. A leállítási művelet használatával szabályosan a skálázási egység csomópont. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

2. A leállítási művelet sikertelen nem valószínű esetben, használja a [kiürítési](azure-stack-node-actions.md#drain) műveletet a skálázási egység csomópont karbantartási módba. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

   > [!NOTE]  
   > Minden esetben csak egy csomópont is le van tiltva és ki van kapcsolva egy időben az S2D megszakítása nélkül (a közvetlen tárolóhelyek).

3. Miután a skálázási egység csomópont karbantartási módban van, a [kikapcsolásához](azure-stack-node-actions.md#scale-unit-node-actions) művelet. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

   > [!NOTE]  
   > A valószínűtlen eset, hogy a művelet kikapcsolás nem működik használja helyette a alaplapi felügyeleti vezérlőnek (BMC) webes felületén.

4. Cserélje le a sérült hardverösszetevő. E OEM hardvergyártójához végez összetevő váltja fel a támogatási szerződés alapján változhatnak.  
5. A belső vezérlőprogramjának frissítéséhez. Kövesse a szállító-specifikus belső vezérlőprogram frissítésének folyamatáról, a hardver életciklus gazdagép segítségével ellenőrizze, hogy a felülírt hardverösszetevő rendelkezik a jóváhagyott belső vezérlőprogram szint a alkalmazni. E OEM hardvergyártójához végrehajtja ezt a lépést a támogatási szerződés alapján változhatnak.  
6. Használja a [javítási](azure-stack-node-actions.md#scale-unit-node-actions) állapotba hozza a skálázási egység csomópont vissza a skálázási egység művelet.
7. Használja a kiemelt végponthoz [Virtuálislemez-javítási állapotának ellenőrzéséhez](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Az új adatok meghajtókat egy teljes helyreállítási feladat rendszerterheléstől függően több órát is igénybe vehet, és a felhasznált lemezterület.
8. A javítási művelet befejezése után, ellenőrizze, hogy az összes aktív riasztás automatikusan lezárták.

## <a name="next-steps"></a>További lépések

- További információ a gyakran használt adatok rétegére – cserélhető fizikai lemez cseréje: [olyan lemezt cserél ki](azure-stack-replace-disk.md).
- További információ a fizikai csomópont cseréje: [cserélje le a skálázási egység csomópont](azure-stack-replace-node.md).
