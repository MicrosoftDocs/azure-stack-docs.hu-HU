---
title: EMC PowerEdge Tactical R640 Server – áttekintés
description: Tudnivalók az EMC PowerEdge Tactical R640 Serverről
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d1f0e141c4ca4966aab9735064287121718dee87
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391196"
---
# <a name="dell-emc-poweredge-tactical-r640-server-overview"></a>A Dell EMC PowerEdge Tactical R640 Server áttekintése

Ez a szakasz áttekintést nyújt a Dell EMC PowerEdge Tactical R640 Server-összetevőiről.

## <a name="chassis-front-view"></a>Váz elülső nézete

Az alábbi ábra a PowerEdge Tactical R640-kiszolgálót mutatja be, amely egy 2U-kiszolgáló.

3. ábra PowerEdge Tactical R640 HLH és SU elülső váz nézet

![](media/image-60.png)

A következő táblázat ismerteti a PowerEdge Tactical R640 \' s előlapjának funkcióit.

1. táblázat A PowerEdge Tactical R640 előlapjának funkciói

| Portok, panelek és tárolóhelyek  | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bal oldali Vezérlőpult        | A rendszerállapot és a rendszerazonosító, az állapot LED és a iDRAC gyors szinkronizálás 2 (vezeték nélküli) kijelzőjét tartalmazza. <br>**Megjegyzés:** A iDRAC gyors szinkronizálási 2 jelzője csak bizonyos konfigurációkon érhető el. <br>Állapot LED: lehetővé teszi a hibás hardver-összetevők azonosítását. Legfeljebb öt állapotjelző LED és egy általános rendszerállapot LED (váz állapota és rendszerazonosító) sáv található. További információ: PowerEdge status LED-mutatók a 61-es oldalon. <br>Gyors szinkronizálás 2 (vezeték nélküli): egy gyors szinkronizálásra alkalmas rendszer használatát jelzi. A gyors szinkronizálás funkció nem kötelező. Ez a funkció lehetővé teszi a rendszer mobileszközök használatával történő felügyeletét. Ez a szolgáltatás összefoglalja a hardver vagy a belső vezérlőprogram leltározását, valamint a rendszer hibaelhárításához használható különböző rendszerszintű diagnosztikai és hibajelentési adatokat.  |
| VGA-port                  | Lehetővé teszi egy megjelenítõ eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Jobb oldali Vezérlőpult       | Tartalmazza a főkapcsoló gombot, az USB-portot, a iDRAC Direct Micro portot és a közvetlen iDRAC-állapotot.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Váz hátsó nézete

Az alábbi ábra a PowerEdge taktikai R640 háttér-összetevőit mutatja be.

4. ábra PowerEdge Tactical R640 HLH háttérbeli nézet

![](media/image-61.png)

A következő táblázat a PowerEdge taktikai R640 hátlapjának funkcióit ismerteti.

2. táblázat A PowerEdge taktikai R640 hátlapjának funkciói

| Portok, panelek és tárolóhelyek       | Ikon                      | Leírás                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Merevlemezek                   | N.A.                       | Az operációs rendszer és az adattárolás a HLH és a SU-kiszolgáló csomópontjaihoz.                                                                                                                                                                                                                        |
| BOSS-modul                   | N.A.                       | Rendszerindításra optimalizált tárolási megoldás (BOSS), amely az operációs rendszer vagy a hypervisor telepítéséhez használatos.                                                                                                                                                                                 |
| Tápegységek (2)        | N.A.                       | Ezek biztosítják a kiszolgáló számára a teljesítményt, és a redundancia érdekében párhuzamosan vannak megadva.                                                                                                                                                                                                 |
| USB 3,0-portok                 |  ![](media/image-62.png)   | Az USB-portok 9 PIN-es és 3,0-kompatibilisek. Ezek a portok lehetővé teszik USB-eszközök csatlakoztatását a rendszerhez.                                                                                                                                                                     |
| VGA-port                      |   ![](media/image-63.png)  | Lehetővé teszi egy megjelenítõ eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                      |
| Soros port                   |   ![](media/image-64.png)  | Lehetővé teszi egy soros eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                       |
| iDRAC9 Enterprise port        |   ![](media/image-65.png)  | Lehetővé teszi a iDRAC távoli elérését.                                                                                                                                                                                                                                       |
| CMA energiagazdálkodási port                | N.A.                       | A Cable Management ARM (CMA) energiaellátási port lehetővé teszi az állapotjelző kábel csatlakoztatását a CMA.                                                                                                                                                                     |
| Rendszerazonosító gomb  |   ![](media/image-66.png) | A rendszer-azonosító (azonosító) gomb a kiszolgáló elöl és hátulján érhető el. A gomb megnyomásakor a rendszerhealth\System-azonosító jelzője villogni kezd. A rendszerazonosító gomb segítségével alaphelyzetbe állíthatja a iDRAC, és elérheti a BIOS-t a lépésenkénti mód használatával.  |

## <a name="right-control-panel"></a>Jobb oldali Vezérlőpult

Az alábbi ábra a PowerEdge taktikai R640 jobb oldali Vezérlőpultját mutatja be.

5. ábra PowerEdge taktikai R640 jobb oldali Vezérlőpult

![](media/image-67.png)

A következő táblázat a jobb oldali panelen található vezérlőket és kijelzőket ismerteti.

3. táblázat PowerEdge taktikai R640 jobb oldali Vezérlőpult-vezérlők és-mutatók

| Elem  | Kijelző vagy gomb  | Ikon                      | Leírás                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Főkapcsoló gomb         |   ![](media/image-68.png)  | Azt jelzi, hogy a rendszer be van-e kapcsolva. Nyomja meg a főkapcsolót a rendszer manuális be-vagy kikapcsolásához.  **Megjegyzés:** A főkapcsoló gomb megnyomásával szabályosan állíthatja le az ACPI-kompatibilis operációs rendszert.  |
| 2     | USB-port             | ![](media/image-70.png)    | Az USB-port 4 PIN-kód, 2,0-kompatibilis. Ez a port lehetővé teszi USB-eszközök csatlakoztatását a rendszerhez.                                                                                                         |
| 3     | iDRAC Direct LED     | N.A.                       | A iDRAC közvetlen LED-jelzőfény jelzi, hogy a iDRAC Direct port aktívan csatlakozik egy eszközhöz.                                                                                        |
| 4     | Közvetlen iDRAC port    |   ![](media/image-65.png) | A iDRAC Direct port a Micro USB 2,0-kompatibilis. Ez a port lehetővé teszi a közvetlen iDRAC funkciók elérését.                                                                                              |

## <a name="inside-the-poweredge-tactical-r640"></a>A PowerEdge taktikai R640 belül

Az alábbi ábrán a belső PowerEdge taktikai R640 összetevők láthatók.

6. ábra A PowerEdge taktikai R640 HLH

![](media/image-71.png)

7. ábra A PowerEdge taktikai R640 SU

![](media/image-72.png)

## <a name="motherboard"></a>Alaplap

Az alábbi ábrán az alaplapok elrendezése látható.

8. ábra PowerEdge taktikai R640 alaplap-elrendezés

![](media/image-73.png)

## <a name="memory"></a>Memória

Az alábbi ábrán a PowerEdge taktikai R640 memória információi láthatók.

9. ábra. PowerEdge taktikai R640 memória adatai

![](media/image-74.png)
