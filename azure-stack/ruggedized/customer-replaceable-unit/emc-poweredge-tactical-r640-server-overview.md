---
title: Kiszolgáló áttekintése
description: További információ a Azure Stack hub-kiszolgáló robusztus
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: a922c99121157c03705784dd99b8abf97fbec507
ms.sourcegitcommit: dc11aabd3b97c505c5b3cecd3bdb2d5c8e8496aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98981274"
---
# <a name="server-overview"></a>Kiszolgáló áttekintése

Ez a szakasz a kiszolgáló-összetevők áttekintését tartalmazza.

## <a name="chassis-front-view"></a>Váz elülső nézete

Az alábbi ábrán a 2U-kiszolgáló látható.


![A kiszolgáló elülső alvázát ábrázoló diagram.](media/image-60.png)

A következő táblázat ismerteti az előlapon lévő funkciókat.

| Portok, panelek és tárolóhelyek  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bal oldali Vezérlőpult        | A rendszerállapot és a rendszerazonosító, az állapot LED és a iDRAC gyors szinkronizálás 2 (vezeték nélküli) kijelzőjét tartalmazza. <br>**Megjegyzés:** A iDRAC gyors szinkronizálási 2 jelzője csak bizonyos konfigurációkon érhető el. <br>Állapot LED: lehetővé teszi a hibás hardver-összetevők azonosítását. Legfeljebb öt állapotjelző LED és egy általános rendszerállapot LED (váz állapota és rendszerazonosító) sáv található. <br>Gyors szinkronizálás 2 (vezeték nélküli): egy gyors szinkronizálásra alkalmas rendszer használatát jelzi. A gyors szinkronizálás funkció nem kötelező. Ez a funkció lehetővé teszi a rendszer mobileszközök használatával történő felügyeletét. Ez a szolgáltatás összefoglalja a hardver vagy a belső vezérlőprogram leltározását, valamint a rendszer hibaelhárításához használható különböző rendszerszintű diagnosztikai és hibajelentési adatokat.  |
| VGA-port                  | Lehetővé teszi egy megjelenítõ eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Jobb oldali Vezérlőpult       | Tartalmazza a főkapcsoló gombot, az USB-portot, a iDRAC Direct Micro portot és a közvetlen iDRAC-állapotot.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Váz hátsó nézete

Az alábbi ábra a háttérbeli összetevők összetevőit mutatja be.


![A kiszolgáló hátsó alvázát ábrázoló diagram.](media/image-61.png)

A következő táblázat a hátlapon található szolgáltatásokat ismerteti.


| Portok, panelek és tárolóhelyek       | Ikon                      | Description                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Merevlemezek                   | N/A                       | Az operációs rendszer és az adattárolás a HLH és a SU-kiszolgáló csomópontjaihoz.                                                                                                                                                                                                                        |
| BOSS-modul                   | N/A                       | Rendszerindításra optimalizált tárolási megoldás (BOSS), amely az operációs rendszer vagy a hypervisor telepítéséhez használatos.                                                                                                                                                                                 |
| Tápegységek (2)        | N/A                       | Ezek biztosítják a kiszolgáló számára a teljesítményt, és a redundancia érdekében párhuzamosan vannak megadva.                                                                                                                                                                                                 |
| USB 3,0-portok                 |  :::image type="icon" source="media/image-62.png"::: | Az USB-portok 9 PIN-es és 3,0-kompatibilisek. Ezek a portok lehetővé teszik USB-eszközök csatlakoztatását a rendszerhez.                                                                                                                                                                     |
| VGA-port                      |   :::image type="icon" source="media/image-63.png":::  | Lehetővé teszi egy megjelenítõ eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                      |
| Soros port                   |   :::image type="icon" source="media/image-64.png":::  | Lehetővé teszi egy soros eszköz csatlakoztatását a rendszerhez.                                                                                                                                                                                                                       |
| iDRAC9 Enterprise port        |   :::image type="icon" source="media/image-65.png":::  | Lehetővé teszi a iDRAC távoli elérését.                                                                                                                                                                                                                                       |
| CMA energiagazdálkodási port                | N/A                       | A Cable Management ARM (CMA) energiaellátási port lehetővé teszi az állapotjelző kábel csatlakoztatását a CMA.                                                                                                                                                                     |
| Rendszerazonosító gomb  |   :::image type="icon" source="media/image-66.png"::: | A rendszer-azonosító (azonosító) gomb a kiszolgáló elöl és hátulján érhető el. A gomb megnyomásakor a rendszerhealth\System-azonosító jelzője villogni kezd. A rendszerazonosító gomb segítségével alaphelyzetbe állíthatja a iDRAC, és elérheti a BIOS-t a lépésenkénti mód használatával.  |

## <a name="right-control-panel"></a>Jobb oldali Vezérlőpult

Az alábbi ábra a jobb oldali Vezérlőpultot mutatja be.

![A Vezérlőpulton a főkapcsoló és az USB-portokat megjelenítő diagram.](media/image-67.png)

A következő táblázat a jobb oldali panelen található vezérlőket és kijelzőket ismerteti.


| Elem  | Kijelző vagy gomb  | Ikon                      | Leírás                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Főkapcsoló gomb         |   :::image type="icon" source="media/image-68.png":::  | Azt jelzi, hogy a rendszer be van-e kapcsolva. Nyomja meg a főkapcsolót a rendszer manuális be-vagy kikapcsolásához.  **Megjegyzés:** A főkapcsoló gomb megnyomásával szabályosan állíthatja le az ACPI-kompatibilis operációs rendszert.  |
| 2     | USB-port             | :::image type="icon" source="media/image-70.png":::    | Az USB-port 4 PIN-kód, 2,0-kompatibilis. Ez a port lehetővé teszi USB-eszközök csatlakoztatását a rendszerhez.                                                                                                         |
| 3     | iDRAC Direct LED     | N/A                       | A iDRAC közvetlen LED-jelzőfény jelzi, hogy a iDRAC Direct port aktívan csatlakozik egy eszközhöz.                                                                                        |
| 4     | Közvetlen iDRAC port    |   :::image type="icon" source="media/image-65.png"::: | A iDRAC Direct port a Micro USB 2,0-kompatibilis. Ez a port lehetővé teszi a közvetlen iDRAC funkciók elérését.                                                                                              |

## <a name="inside-the-server"></a>A kiszolgálón belül

Az alábbi ábrán a belső kiszolgáló összetevői láthatók.

![Diagram, amely a R640 HLH belső kiszolgálói összetevőinek felső oldali nézetét jeleníti meg.](media/image-71.png)


![Diagram, amely az R640 SU belső kiszolgáló összetevőinek legfelső szintű nézetét jeleníti meg.](media/image-72.png)

## <a name="motherboard"></a>Alaplap

Az alábbi ábrán az alaplapok elrendezése látható.


![A R640 szolgáltatás információit és az alaplap elrendezését bemutató diagram.](media/image-73.png)

## <a name="memory"></a>Memória

Az alábbi ábrán a memória adatai láthatók.


![A R640 memóriájának adatait bemutató diagram.](media/image-74.png)
