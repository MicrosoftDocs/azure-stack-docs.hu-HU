---
title: Azure Stack HCI kibocsátási megjegyzései
description: A Azure Stack HCI, Version 20H2 kibocsátási megjegyzései.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/17/2020
ms.openlocfilehash: 57aa49c075afad4f6ccb37802293424d8e0e309f
ms.sourcegitcommit: c45f92186edf62b95b38d1020e0851dd54bc6536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2020
ms.locfileid: "90765390"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>A Azure Stack HCI nyilvános előzetes kiadásának kibocsátási megjegyzései

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a cikk a Azure Stack HCI nyilvános előzetes frissítési csomagjainak tartalmát ismerteti.

## <a name="september-17-2020-preview-update-kb4577629"></a>Szeptember 17, 2020 előzetes frissítés (KB4577629)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a nem biztonsági frissítés tartalmazza a minőségi frissítéseket. A legfontosabb változások a következők:
- Probléma történt, ha a multiplexeren áthaladó szoftveres Load Balancer (SLB) forgalom átirányítható egy másik gazdagépre, amely az alkalmazás kapcsolódási hibáját okozhatja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 17-2020 biztonsági frissítés (KB4577629) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1259) a 4577629-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv) letöltése

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="september-8-2020-security-update-kb4577470"></a>2020. szeptember 8. biztonsági frissítés (KB4577470)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 8., 2020-os biztonsági frissítés (KB4577470) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1226) a 4577470-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>2020. szeptember 8. karbantartási verem frissítése (KB4577558)

Ez a frissítés a Azure Stack HCI legújabb kiadásának minőségi újdonságait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés minőségi javításokat végez a karbantartási veremben, amely a frissítéseket telepítő összetevő. A karbantartási verem frissítései (SSU) gondoskodnak arról, hogy robusztus és megbízható karbantartási veremmel rendelkezzen, hogy az eszközök megkapják és telepítsék a Microsoft-frissítéseket.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
Az [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 8., 2020-es karbantartási verem frissítése (KB4577558) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="restart-information"></a>Újraindítási információ 
A frissítés alkalmazása után nem kell újraindítani a számítógépet.

### <a name="removal-information"></a>Eltávolítási információ
A karbantartási verem frissítései (SSU) módosítják a frissítések telepítését, és nem távolíthatók el az eszközről.

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1220) a 4577558-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv)letöltésével töltheti le.

### <a name="references"></a>Referencia

A SSU kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Karbantartási verem frissítései](https://docs.microsoft.com/windows/deployment/update/servicing-stack-updates)
- [Karbantartási verem frissítései (SSU): gyakori kérdések](https://support.microsoft.com/help/4535697)

Ismerje meg, hogy a Microsoft milyen [terminológiát](https://support.microsoft.com/help/824684) használ a szoftverfrissítések leírásához.

## <a name="august-11-2020-security-update-kb4574585"></a>2020. augusztus 11. biztonsági frissítés (KB4574585)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült augusztus 11, 2020 biztonsági frissítés (KB4574585) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1162) a 4574585-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.
