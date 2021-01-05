---
title: EMC PowerEdge taktikai mutatók és kódok
description: Tudnivalók az EMC PowerEdge Tactical-mutatókról és-kódokról
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b439bfdfcae82c6e80b29d678c18f434945684cc
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867944"
---
# <a name="dell-emc-poweredge-tactical-indicators-and-codes"></a>Dell EMC PowerEdge – taktikai mutatók és kódok

Az ebben a szakaszban ismertetett funkciók megegyeznek a hardver életciklus-gazdagép és a skálázási egység csomópont-kiszolgálók esetében.

## <a name="system-information-label"></a>Rendszerinformáció címkéje

Az alábbi ábra a rendszerinformációk címkéjét mutatja be, amely az előlapon található.


10. ábra. LED-viselkedés

![](media/image-75.png)

## <a name="idrac-direct-led-indicator-codes"></a>iDRAC Direct LED-kijelzők kódjai

A iDRAC közvetlen LED-jelzőfény jelzi, hogy a port csatlakoztatva van, és a rendszer a iDRAC alrendszer részeként használja.

A iDRAC a közvetlen USB-ről a Micro-USB (Type AB) kábellel konfigurálhatja, amelyhez csatlakozhat a laptophoz vagy a táblaszámítógéphez. Az alábbi táblázat a közvetlen iDRAC tevékenységet mutatja be, amikor a iDRAC Direct port aktív, és a iDRAC Direct LED-jelző kódokat ismerteti.

4. táblázat. LED-kijelzők kódjai

| **Kijelző kódok**                                         | **Feltétel**                                                |
|-------------------------------------------------------------|--------------------------------------------------------------|
| Tömör zöld két másodpercig                                 | Azt jelzi, hogy a laptop vagy a táblaszámítógép csatlakoztatva van.            |
| Villogó zöld (két másodpercig, két másodpercig kikapcsolva) | Azt jelzi, hogy a csatlakoztatott laptop vagy táblaszámítógép fel van ismerve. |
| Nincs kivilágítva                                             | Azt jelzi, hogy a laptop vagy a táblaszámítógép le van húzva.            |

## <a name="left-control-panel"></a>Bal oldali Vezérlőpult

Az alábbi ábrán a bal oldali Vezérlőpult állapota és állapotjelzői láthatók.



11. ábra. Bal oldali Vezérlőpult

![](media/image-76.png)

A következő táblázat a bal oldali panel funkcióit ismerteti.

5. táblázat PowerEdge bal oldali Vezérlőpult állapota és állapotjelzői

|    <br>Elem       |    <br>Kijelző vagy gomb                       |    <br>Ikon                                |    <br>Leírás                                                                                                  |
|-------------------|--------------------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
|    <br>1          |    <br>Állapotjelző LED-mutatók                     |    <br>N.A.                                 |    <br>A szolgáltatás állapotát jelzi. További információ: PowerEdge status LED-mutatók a 61-es oldalon.    |
|    <br>2 és 3    |    <br>Rendszerállapot-és rendszerazonosítók mutatói    | ![](media/image-77.png) |    <br>A rendszerállapotot jelzi.                                                                                 |

A következő táblázat a PowerEdge állapotának LED-mutatóit sorolja fel.

> [!NOTE]
> Az állapotjelző LED-mutatók mindig ki vannak kapcsolva, és csak folytonos borostyánra változnak, ha bármilyen hiba történik.

6. táblázat PowerEdge állapotú LED-mutatók

| <br>Ikon | <br>Leírás           | <br>Feltétel                                                                                                                                                                       | <br>Javító művelet                                                                                                                                                                                                                                                                                                    |
|----------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | <br>Meghajtó jelzője       | <br>Ha hiba történik, a kijelzőn a stabil sárga szín jelenik meg.                                                                                                                      | 1. Ellenőrizze a rendszer eseménynaplóját, és állapítsa meg, hogy a meghajtó hibával rendelkezik-e.<br>2. Futtassa a megfelelő online diagnosztikai tesztet.<br>3. Indítsa újra a rendszert, és futtassa a beágyazott diagnosztikát (ePSA).<br>4. Ha a meghajtók RAID-tömbben vannak konfigurálva, indítsa újra a rendszert, és adja meg a gazdagép adapterének konfigurációs segédprogramját. |
|          | <br>Hőmérséklet kijelző | <br>Ha a rendszer termikus hibát tapasztal (például a környezeti hőmérséklet a tartományon kívül esik, vagy ventilátor meghibásodása van), a kijelző stabil sárga színűre vált.                 | Győződjön meg arról, hogy a következő feltételek egyike sem létezik:<br>* A hűtőventilátor el lett távolítva vagy meghiúsult. <br>* A rendszerszintű, a légi lepel, a memóriamodul üres vagy a hátsó kitöltő zárójel el lesz távolítva.<br>* A környezeti hőmérséklet túl magas.<br>* A külső légáram akadályozva van.                                                 |
|          | <br>Elektromos kijelző  | <br>A kijelző stabil sárga színűre vált, ha a rendszer elektromos hibát észlel (például a tartományon kívüli feszültséget vagy egy meghibásodott tápegységet) vagy feszültség szabályzót. | 1. Keresse meg a rendszer eseménynaplóját vagy a rendszerüzeneteket az adott hibához.<br>2. Ha a napló a PSU hibáját jelzi, ellenőrizze a LED-t a PSU-on.<br>3. Helyezze át a PSU-t.                                                                                                                                              |
|          | <br>Memória kijelzője      | <br>Ha memóriahiba történik, a kijelzőn a stabil sárga szín jelenik meg.                                                                                                                       | 1. Keresse meg a rendszer eseménynaplóját vagy a rendszerüzeneteket a hibás memória helyének megadásához.<br>2. Helyezze át a memóriamodult.                                                                                                                                                                                                  |
|          | <br>PCIe-kijelző      | <br>Ha a PCIe-kártya hibát észlel, a kijelzőn a stabil sárga színűre vált.                                                                                                          | 1. Indítsa újra a rendszert.<br>2. frissítse a PCIe kártyához szükséges illesztőprogramokat.<br>3. Telepítse újra a kártyát.                                                                                                                                                                                                                    |

## <a name="power-supply-unit-indicator-codes"></a>Tápegység-egység jelölő kódjai

A VÁLTÓÁRAMÚ tápegységek (PSUs) egy világító átlátszó fogantyúval rendelkeznek, amely kijelzőként szolgál. A DC PSUs olyan LED-t tartalmaz, amely kijelzőként szolgál. A kijelzőn látható, hogy a tápellátás van-e vagy áramkimaradás történt-e.

Az alábbi ábrán az AC PSU látható.

12. ábra. AC PSU

![](media/image-83.png)

A következő táblázat a jelölő kódokat határozza meg.

7. táblázat. AC PSU-kijelzők kódjai

| Energiagazdálkodási mutatók kódjai         | Feltétel                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zöld                         | Egy érvényes áramforrás csatlakozik a PSU-hez, és a PSU működik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Villogó sárga                | A PSU hibáját jelzi.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Nincs kivilágítva               | A Power nincs csatlakoztatva a PSU-hoz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Villogó zöld                | Ha a PSU belső vezérlőprogram frissítése folyamatban van, a PSU-fogantyú zöld színnel villog. <br>Ne válassza le a tápkábelt, vagy húzza ki a TÁPEGYSÉGet a belső vezérlőprogram frissítésekor. Ha a belső vezérlőprogram frissítése megszakad, a tápegységek nem működnek.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Zöld villogás és kikapcsolás  | A PSU gyors csatlakoztatásakor a PSU öt alkalommal, 4 Hz-es sebességgel villog, és kikapcsol. Ez azt jelzi, hogy a PSU nem egyezik a hatékonysággal, a funkciók készletével, az állapottal vagy a támogatott feszültséggel kapcsolatban. <br><br>Ha két PSUs van telepítve, a PSUs-nek ugyanolyan típusú címkével kell rendelkeznie; például: kiterjesztett teljesítmény (EPP) felirat. A PowerEdge-kiszolgálók előző generációi által használt PSUs-keverők nem támogatottak, még akkor is, ha a PSUs-nek azonos az energiafogyasztásuk. Ez egy PSU-eltérési feltételt eredményez, vagy nem sikerül a rendszer bekapcsolása. <br>A PSU-eltérések kijavításakor csak a TÁPEGYSÉGet cserélje le a villogó kijelzőre. Ha a PSU-t felcseréli egy egyeztetett pár kiváltására, akkor a hiba feltétele és <br>váratlan rendszerleállítás történt. Ha magas kimeneti konfigurációról alacsony kimeneti konfigurációra szeretne váltani, vagy fordítva, ki kell kapcsolni a rendszerét. <br>Az AC PSUs a 240 V és a 120 V bemeneti feszültséget is támogatja a titán PSUs kivételével, amely csak a 240 V-t támogatja. Ha két azonos PSUs különböző bemeneti feszültséget kap, különböző teljesítmények tudnak kiadni, és eltérést válthatnak ki. <br>Ha két PSUs van használatban, ugyanolyan típusúnak kell lennie, és a maximális kimeneti teljesítménnyel kell rendelkeznie. <br>Az AC és a DC PSUs együttes használata nem támogatott, és eltérést vált ki. |
