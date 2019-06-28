---
title: Az Azure Stack karbantartási szabályzat |} A Microsoft Docs
description: További információ az Azure Stack karbantartási szabályzat, és hogyan kell fenntartani egy integrált rendszer támogatott állapotban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 8965ad94d1c8576e437e85a9714997f842f7dd50
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419461"
---
# <a name="azure-stack-servicing-policy"></a>Az Azure Stack karbantartási szabályzat

Ez a cikk ismerteti a karbantartási szabályzat az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban.

## <a name="download-update-packages-for-integrated-systems"></a>Töltse le a frissítési csomagokat integrált rendszerek

A Microsoft közzéteszik egyaránt teljes havi csomagok, valamint a gyorsjavítás csomagok konkrét problémáinak megoldására.

Havi csomagok egy biztonságos Azure-végpont a üzemelnek. Letöltheti őket manuálisan a [Azure Stack frissítéseinek letöltési segédprogramja eszköz](https://aka.ms/azurestackupdatedownload). Ha a skálázási egység csatlakoztatva van, a frissítés automatikusan megjelenik a felügyeleti portálon, **frissítés érhető el**. Teljes havonta frissítési csomagokat is szerepelnek, az egyes kiadások. Minden kiadással kapcsolatos további információkért kattintson bármelyik-esről a [frissítési csomag kiadási ütem](#update-package-release-cadence) című szakaszát.

A gyorsjavítás csomagok ugyanazt az Azure biztonságos végpontját üzemelnek. Letöltheti őket a beágyazott hivatkozásokkal az egyes a megfelelő gyorsjavítás; KB-cikkek Ha például [Azure Stack gyorsjavítás 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). A teljes, a havi csomagok hasonlóan az Azure Stack-operátorok is .xml, .bin és az .exe fájlok letöltésére és importálhatja őket a leírtak [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md). Csatlakoztatott skálázási egység az Azure Stack-operátoroknak jelenik meg a gyorsjavításokat, automatikusan megjelenik a rendszergazdai portálon üzenettel **frissítés érhető el**.

Ha a skálázási egység nem kapcsolódik, és szeretne értesítést kapni az egyes gyorsjavítások kiadások, iratkozzon fel a [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) vagy [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) az egyes kiadások feljegyzett hírcsatorna.  

## <a name="update-package-types"></a>A csomagok frissítése

Integrált rendszerek frissítési csomagokat két típusa van:

- **A Microsoft szoftverfrissítések**. A Microsoft a Microsoft update szoftvercsomagok életciklusának teljes körű karbantartási feladata. Ezeket a csomagokat a Windows Server legújabb biztonsági frissítéseket, nem biztonsági frissítés és az Azure Stack funkciófrissítések tartalmazhatnak. Letöltheti a következő csomagok közvetlenül a Microsofttól.

- **OEM hardvert gyártó által biztosított frissítések**. Az Azure Stack a hardvergyártó partnerektől felelős a végpontok közötti karbantartási életciklusának (útmutatást is beleértve) a hardverrel kapcsolatos belső vezérlőprogram és illesztőprogram-csomagok frissítése. Emellett az Azure Stack a hardvergyártó partnerektől tulajdonosa, és útmutatást az összes szoftver- és a hardver életciklus gazdagép karbantartása. A számítógép-Gyártói szállítójával futtatja ezeket a csomagokat a saját letöltési hely frissítése.

## <a name="update-package-release-cadence"></a>Frissítési csomag kiadási ütem

A Microsoft vár a szoftverfrissítési csomagok havi kiadása ütemben történik felszabadítása érdekében. Viszont azt, lehetséges, hogy több, vagy nincs kiadásai az adott hónapban. OEM hardverszállítók a frissítéseket egy igény szerint történik.

Megtervezheti és kezelheti a frissítéseket, és miként állapítható meg, a jelenlegi verziót dokumentáció [kezelés frissítések – áttekintés](azure-stack-updates.md).

Egy adott frissítés, és töltheti le, beleértve kapcsolatos információkat lásd: kibocsátási megjegyzései, amely frissítése:

- [Azure Stack 1906 frissítése](azure-stack-release-notes-1906.md)
- [Azure Stack 1905 frissítése](azure-stack-release-notes-1905.md)
- [Azure Stack 1904 frissítése](azure-stack-release-notes-1904.md)
- [Azure Stack 1903 frissítése](azure-stack-update-1903.md)

## <a name="hotfixes"></a>Gyorsjavítások

Néha előfordul, a Microsoft kínál gyorsjavítások Azure Stack egy adott probléma, amely gyakran megelőző vagy időérzékeny címmel.  A megfelelő Microsoft Tudásbázis megfelelő cikkében, amely leírja a problémát, a kiváltó okkal és a megoldás, az egyes gyorsjavítások szabadul fel.

További információk és hivatkozások az adott gyorsjavításokat tekintse meg a kibocsátási megjegyzések hivatkozásokat az előző szakaszban.

Gyorsjavítások letölthető és telepíthető csakúgy, mint a rendszeres teljes frissítési csomagokat az Azure Stackhez. Azonban ellentétben a teljes frissítés, gyorsjavítások perc is telepíthető. Azt javasoljuk, hogy az Azure Stack-operátorok gyorsjavítások telepítésekor állítson be karbantartási ablakokat. Gyorsjavítások frissítése az Azure Stack-felhőben, azt könnyen megállapíthatja, hogy ha a gyorsjavítás telepítve van-e. Azure Stack támogatása továbbra is szereplő minden egyes verziója külön gyorsjavítás biztosítunk. Minden egyes javítása egy adott iteráció összegző, és ugyanazt a verziót a korábbi frissítéseit tartalmazza. Tudjon meg többet a megfelelő Tudásbázis egy adott gyorsjavítás alkalmazhatóságát kapcsolatos cikket. Tekintse meg a kibocsátási megjegyzések hivatkozásokat az előző szakaszban.  

## <a name="keep-your-system-under-support"></a>A rendszer a támogatás

A folytatáshoz szeretne támogatást kapni az Azure Stack üzemelő példányához kell tartania, aktuális. A frissítések késleltetésének házirend: támogatási megőrzési ideje az Azure Stack üzemelő frissítés nemrég kiadott verzióját futtató vagy a két előző frissítési verziók valamelyikével kell azt. Gyorsjavítások nem veszi figyelembe a nagyobb frissítési verziókat. Ha az Azure Stack-felhő mögött *több mint két frissítések*, azt a megfelelő számít, és frissítenie kell a legalább a minimális támogatott verziót szeretne támogatást kapni.

Például ha a rendelkezésre álló legfrissebb frissített verziót 1904, és az előző két frissítési csomagokat is 1903 és 1902 verziója, 1902 és 1903 is maradnak támogatása. Azonban 1901, nem támogatott. A szabályzat igaznak, ha egy hónapban, vagy két nincs kiadási. Például a jelenlegi kiadásban 1807, és nincs 1806 kiadás volt, az előző két frissítési csomagokat 1805 és 1804 továbbra is támogatott.

A szoftverfrissítési csomagokat a Microsoft nem összesítő és a korábbi frissítési csomag vagy gyorsjavítás előfeltételként szükséges. Ha úgy dönt, hogy egy vagy több frissítések késleltetése, fontolja meg a teljes runtime, ha szeretne kapni a legújabb verzióra.

## <a name="get-support"></a>Támogatás kérése

Az Azure Stack az Azure támogatási folyamatot követi. Nagyvállalati ügyfeleink is kövesse az ismertetett folyamatot [létrehozása egy Azure-támogatáskérést](/azure/azure-supportability/how-to-create-azure-support-request). Ha egy ügyfél egy Cloud Service Provider (CSP), forduljon a CSP.  További információkért lásd: a [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stackben](azure-stack-updates.md)