---
title: Frissítések és frissítések
description: A frissítések és a frissítések alkalmazásának áttekintése Azure Stack HCI-re.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867525"
---
# <a name="updates-and-upgrades"></a>Frissítések és frissítések

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Azure Stack HCI-megoldások kiszámítható frissítési és frissítési tapasztalattal rendelkeznek.

## <a name="support-period"></a>Támogatási időszak

A Microsoft Hardware Solution partnertől legalább öt évig számíthat a hardveres szolgáltatásra, a támogatásra és a biztonsági frissítésekre. Az Azure Stack HCI operációs rendszert két évig támogatja a Microsoft, éves szolgáltatásokkal, amelyekkel az ügyfél akár egy évig is üzembe helyezhető.

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Azure Stack HCI – éves kiadási ütemterv" border="false":::

## <a name="updating-azure-stack-hci"></a>Azure Stack HCI frissítése

A Windows 10 rendszerhez hasonlóan a Azure Stack HCI mindig naprakész marad a Microsoft havi minőségi és biztonsági frissítéseivel. A frissítéseket a fürtöket támogató frissítéssel kezdeményezheti, mint a Windows Serveren.

A hardveres partnerek és a megoldás-építők a Windows felügyeleti központba csatlakoztathatók, és a bővítmények fejlesztésével megtarthatják a kiszolgálók belső vezérlőprogram, illesztőprogramjai és BIOS-ának naprakész és konzisztens csomópontjait. Azok az ügyfelek, akik a Azure Stack HCI-vel előre telepített, integrált rendszert vásárolnak, a bővítmények segítségével telepíthetik a megoldás frissítéseit; azok az ügyfelek, akik egyszerűen megvásárolták az érvényesített hardvereszközöket, esetleg külön kell végrehajtaniuk a frissítéseket a hardver gyártójától kapott javaslatok alapján.

A virtuális gépeket is rendszeresen frissíteni kell. Windows Update és Windows Server Update Services mellett az Azure Update Management használatával is frissítheti a virtuális gépeket.

## <a name="upgrading-to-azure-stack-hci"></a>Frissítés Azure Stack HCI-re

A Windows Server 2019-ről Azure Stack HCI-re frissíthet, de zökkenőmentesen nem. Jelenleg nincs helyben történő frissítés. A frissítéshez karbantartási időszakra és tiszta telepítésre van szükség az egyes kiszolgálókon, és az egyes csomópontokat egyenként kell újraépíteni a működés közbeni fürt frissítésének részeként.

## <a name="cluster-aware-updating"></a>Fürtöket támogató frissítés

A fürt frissítéséhez szükség van egy előkészítésre, mert egyes frissítések esetében szükség van az operációs rendszer újraindítására, és a kiszolgálókat újra kell indítani a fürt rendelkezésre állásának biztosítása érdekében. A fürtöket támogató frissítés (CAU) a Microsoft alapértelmezett Orchestrator, amely lehetővé teszi a frissítések végrehajtását a Windows felügyeleti központban lévő integrált frissítési felület vagy a PowerShell-parancsok használatával manuálisan. A fürtöket támogató frissítés bővíthető a belső vezérlőprogram és az illesztőprogram frissítéseinek beolvasására szolgáló partneri beépülő modulok támogatásához.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Azure Stack HCI-fürtök frissítése](../manage/update-cluster.md)
