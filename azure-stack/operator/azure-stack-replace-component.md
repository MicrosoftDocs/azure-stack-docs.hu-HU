---
title: Hardver-összetevő cseréje Azure Stack hub skálázási egység csomópontján
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan cserélhet le egy hardver-összetevőt egy Azure Stack hub integrált rendszeren.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 2755daf33f1bd2bd4bc02858821ae0727df71042
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572596"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-hub-scale-unit-node"></a>Hardveralkatrész cseréje Azure Stack Hub skálázásiegység-csomóponton

Ez a cikk az általános eljárást ismerteti a nem becserélhető hardver-összetevők cseréjére. A tényleges helyettesítési lépések az eredeti berendezésgyártó (OEM) hardvergyártó alapján változnak. A Azure Stack hub integrált rendszerével kapcsolatos részletes lépésekért tekintse meg a gyártó által kihelyezhető, cserélhető egységre vonatkozó dokumentációt.

> [!CAUTION]  
> A belső vezérlőprogram-simítás kritikus fontosságú a cikkben ismertetett művelet sikeressége szempontjából. Ez a lépés hiányzik a rendszer instabillá, a teljesítmény csökkenése, a biztonsági szálak vagy a Azure Stack hub Automation operációs rendszer központi telepítésének megakadályozása érdekében. A hardver cseréjekor mindig tekintse meg a hardveres partner dokumentációját, és győződjön meg arról, hogy az alkalmazott belső vezérlőprogram megfelel az [Azure stack hub felügyeleti portálján](azure-stack-updates.md)megjelenő OEM-verziónak.

| Hardveres partner | Region | URL-cím |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Mind | [Cisco integrált rendszer Microsoft Azure Stack hub üzemeltetési útmutatóhoz](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Kibocsátási megjegyzések a Microsoft Azure Stack hub-hoz készült Cisco integrált rendszerhez](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Mind | [Felhő Microsoft Azure Stack hub-14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Felhő Microsoft Azure Stack hub-13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japán | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (fiók és bejelentkezés szükséges)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Mind | [Microsoft Azure Stack hub HPE-ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Mind | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/belső vezérlőprogram csomagja](https://aka.ms/AA6z600)<br>[a Terra Azure Stack hub dokumentációja (beleértve a cserélhető adatközpontot)](https://aka.ms/aa6zktc)

A nem lecserélhető összetevők az alábbi elemeket tartalmazzák:

- CPU
- Memória
- Alaplap/alaplapi felügyeleti vezérlő (BMC)/video kártya
- Lemezvezérlő/gazdagép Bus-adapter (HBA)/Backplane
- Hálózati adapter (NIC)
- Operációs rendszer lemeze *
- Adatmeghajtók (olyan meghajtók, amelyek nem támogatják a gyors cserét, például a PCI-e bővítmények kártyái) *

* Ezek az összetevők támogatják a gyors cserét, de a szállítói implementációtól függően változhatnak. A részletes lépésekért tekintse meg az OEM gyártója a cserélhető szoftverrel kapcsolatos dokumentációját.

A következő folyamatábra a nem lecserélhető hardver-összetevők cseréjére szolgáló általános cserélhető folyamatokat mutatja be.

![Összetevő-helyettesítési folyamatot bemutató folyamatábra](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

* * Az, hogy az OEM hardveres gyártója rendelkezik-e az összetevő cseréjével és a belső vezérlőprogram frissítésével, a támogatási szerződéstől függően változhat.

## <a name="review-alert-information"></a>Riasztási információk áttekintése

Az Azure Stack hub állapot-és monitorozási rendszere nyomon követi a Közvetlen tárolóhelyek által vezérelt hálózati adapterek és adatmeghajtók állapotát. Nem követ más hardver-összetevőket. Minden más hardver-összetevő esetén a rendszer riasztásokat jelenít meg a hardveres életciklus-gazdagépen futó, szállítóspecifikus hardver-figyelési megoldásban.  

## <a name="component-replacement-process"></a>Összetevő-helyettesítési folyamat

Az alábbi lépések áttekintést nyújtanak az összetevő-helyettesítési folyamatról. Ne hajtsa végre ezeket a lépéseket anélkül, hogy a SZÁMÍTÓGÉPGYÁRTÓ által megadott cserélhető eszközre hivatkozik.

1. A leállítási művelettel szabályosan állíthatja le a méretezési egység csomópontját. Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

2. Ha nem valószínű, hogy a leállítási művelet sikertelen, a [kiürítési](azure-stack-node-actions.md#drain) művelettel állítsa be a méretezési egység csomópontját karbantartási módba. Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

   > [!NOTE]  
   > Mindenesetre csak egy csomópontot lehet letiltani és kikapcsolni a S2D (Közvetlen tárolóhelyek) megszakítása nélkül.

3. Miután a skálázási egység csomópontja karbantartási módban van, használja a [kikapcsolás műveletet.](azure-stack-node-actions.md#scale-unit-node-actions) Előfordulhat, hogy ez a művelet a hardver fizikai állapota alapján nem szükséges.

   > [!NOTE]  
   > Ha nem valószínű, hogy a kikapcsolás művelet nem működik, használja helyette a alaplapi felügyeleti vezérlő (BMC) webes felületét.

4. Cserélje le a sérült hardver összetevőt. Azt határozza meg, hogy az OEM hardver gyártója rendelkezik-e a támogatási szerződéstől függően.  
5. Frissítse a belső vezérlőprogramot. A hardveres életciklus-gazdagép használatával hajtsa végre a gyártó által megadott belső vezérlőprogram-frissítési folyamatot, hogy ellenőrizze, hogy a lecserélt hardver-összetevő rendelkezik-e a jóváhagyott belső vezérlőprogram- Azt határozza meg, hogy ez a lépés a támogatási szerződéstől függően változhat-e.  
6. A [javítási](azure-stack-node-actions.md#scale-unit-node-actions) művelettel helyezze vissza a méretezési egység csomópontját a méretezési egységbe.
7. A rendszerjogosultságú végponton [ellenőrizhető a virtuális lemez javításának állapota](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Az új adatmeghajtók esetében a teljes tárterület-javítási feladat több órát is igénybe vehet a rendszerterheléstől és a felhasznált területtől függően.
8. A javítási művelet befejeződése után ellenőrizze, hogy az összes aktív riasztás automatikusan le van-e zárva.

## <a name="next-steps"></a>Következő lépések

- A lecserélhető fizikai lemezek cseréjével kapcsolatos információkért lásd: [lemez cseréje](azure-stack-replace-disk.md).
- A fizikai csomópontok cseréjével kapcsolatos információkért lásd: [méretezési egység csomópontjának cseréje](azure-stack-replace-node.md).
