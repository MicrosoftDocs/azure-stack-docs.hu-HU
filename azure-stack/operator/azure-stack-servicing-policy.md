---
title: Azure Stack hub karbantartási szabályzata
titleSuffix: Azure Stack Hub
description: Ismerje meg az Azure Stack hub karbantartási szabályzatát, valamint azt, hogyan tarthat egy integrált rendszer támogatott állapotú.
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: niy
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: baa622bf9d80d0896dfb3431e1363e4a8a780567
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255754"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack hub karbantartási szabályzata

Ez a cikk ismerteti az Azure Stack hub integrált rendszerek karbantartási szabályzatát, és azt, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon.

## <a name="download-update-packages-for-integrated-systems"></a>Frissítési csomagok letöltése az integrált rendszerekhez

A Microsoft a teljes frissítési csomagokat és a gyorsjavítási csomagokat is felszabadítja az adott problémák megoldásához.

A teljes frissítési csomagokat egy biztonságos Azure-végpont tárolja. Ezeket manuálisan is letöltheti az [Azure stack hub Updates Downloader eszközzel](https://aka.ms/azurestackupdatedownload). Ha a skálázási egység csatlakoztatva van, a frissítés **elérhetőként** automatikusan megjelenik a felügyeleti portálon. Az egyes kiadásokkal kapcsolatos további információkért kattintson a jelen cikk [frissítési csomag kiadási lépésszám](#update-package-release-cadence) szakaszának bármelyik kiadására.

A gyorsjavítás-frissítési csomagok ugyanazon a biztonságos Azure-végponton futnak. Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozások használatával töltheti le. például [Azure stack hub gyorsjavítási 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). A teljes, havi frissítési csomagokhoz hasonlóan Azure Stack hub-kezelők letöltheti az. XML és a. zip fájlokat, és importálhatja azokat a [frissítések alkalmazása a Azure stack hub-ban](azure-stack-apply-updates.md)című eljárással. A csatlakoztatott méretezési egységekkel rendelkező Azure Stack hub-operátorok automatikusan megjelennek a felügyeleti portálon az üzenet **frissítése lehetőséggel**.

Ha a méretezési egység nincs csatlakoztatva, és értesítést szeretne kapni az egyes gyorsjavításokról, fizessen elő az [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) -vagy [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) -hírcsatornára, amely minden kiadásban fel van tüntetve.

## <a name="update-package-types"></a>Frissítési csomagok típusai

Az integrált rendszerek két típusú frissítési csomaggal rendelkeznek:

- **Microsoft-szoftverfrissítések**. A Microsoft a Microsoft szoftverfrissítési csomagjainak teljes körű karbantartási életciklusát felelős. Ezek a csomagok magukban foglalhatják a Windows Server legújabb biztonsági frissítéseit, a nem biztonsági frissítéseket és a Azure Stack hub szolgáltatás frissítéseit. A tézisek frissítési csomagjai közvetlenül a Microsofttól tölthetők le.

- **OEM hardver gyártó által megadott frissítések**. Azure Stack hub hardveres partnerei felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusának (beleértve az útmutatást is). Emellett a Azure Stack hub Hardware-partnerek saját és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="update-package-release-cadence"></a>A csomag kiadási ritmusának frissítése

A Microsoft az év során többször is kiadja a szoftverfrissítési csomagokat.

Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket. A hardver legújabb frissítéseiről érdeklődjön a SZÁMÍTÓGÉPGYÁRTÓnál.

A frissítések tervezésével és kezelésével, valamint az aktuális verzió meghatározásával kapcsolatos további információkért tekintse meg a frissítések [kezelése című témakörben](azure-stack-updates.md)található dokumentációt.

Egy adott frissítéssel kapcsolatos információkért, beleértve a letöltés módját is, tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack hub 2008 frissítése](./release-notes.md?preserve-view=true&view=azs-2008)
- [Azure Stack hub 2005 frissítése](./release-notes.md?preserve-view=true&view=azs-2005)
- [Azure Stack hub 2002 frissítése](./release-notes.md?preserve-view=true&view=azs-2002)

## <a name="hotfixes"></a>Gyorsjavítások

Alkalmanként a Microsoft gyorsjavításokat biztosít Azure Stack hubhoz, amely egy gyakran megelőző vagy időérzékeny probléma megoldására szolgál. Minden gyorsjavítást egy megfelelő Microsoft Tudásbázis (KB) című cikk bocsát ki, amely részletezi a gyorsjavításban tárgyalt problémákat.

A Build 2005-es verziójától kezdve, amikor új főverzióra frissít (például: 1.2002. x – 1.2005. x), a rendszer automatikusan telepíti a legújabb gyorsjavításokat (ha vannak ilyenek) az új főverzióban. Ettől a ponttól kezdve, ha a buildhez kiadott egy gyorsjavítást, telepítse azt.

A gyorsjavítások letöltése és telepítése ugyanúgy történik, mint a Azure Stack hub normál teljes frissítési csomagjai. Azonban a teljes frissítéstől eltérően a gyorsjavítások percek alatt telepíthetők. Javasoljuk, hogy a gyorsjavítások telepítésekor Azure Stack hub-operátorok állítsanak be karbantartási időszakokat. A gyorsjavítások frissítik a Azure Stack hub-felhő verzióját, így könnyen megállapíthatja, hogy a gyorsjavítás alkalmazása megtörtént-e. Külön gyorsjavítást biztosítanak a Azure Stack hub minden olyan verziójához, amely továbbra is támogatott. **Egy adott iterációhoz tartozó egyes gyorsjavítások kumulatívak, és az ugyanazon verzióhoz tartozó korábbi gyorsjavításokat is tartalmazzák.** A megfelelő TUDÁSBÁZIS-cikkben további információt talál egy adott gyorsjavítás alkalmazhatóságáról. Tekintse meg a kibocsátási megjegyzések hivatkozásait az előző szakaszban.

A jelenleg elérhető gyorsjavításokkal kapcsolatos további információkért tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack hub 2005 gyorsjavítás](./release-notes.md?preserve-view=true&view=azs-2005#hotfixes)
- [Azure Stack hub 2002 gyorsjavítás](./release-notes.md?preserve-view=true&view=azs-2002#hotfixes-1)

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ahhoz, hogy a Azure Stack hub-példány továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy futtatnia kell a két korábbi frissítési verzió valamelyikét.

Emellett rendelkeznie kell egy aktív támogatási szerződéssel a rendszer gyártójának hardveres partnerével. A Microsoft nem tudja támogatni a hardveres támogatási szerződés hiányában.

A gyorsjavítások nem tekintendők jelentős frissítési verzióknak. Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

### <a name="resource-provider-version-support"></a>Erőforrás-szolgáltató verziójának támogatása

Azure Stack hub-erőforrás-szolgáltatók esetében fontos megjegyezni, hogy a Azure Stack hub támogatott verziójával kompatibilis adott erőforrás-szolgáltató csak a legutóbb kiadott verziója támogatott, annak ellenére, hogy az Azure Stack hub egy régebbi verzióját használja, amely továbbra is a támogatási ablakban található.

Az erőforrás-szolgáltató kompatibilitásával kapcsolatos további információkért tekintse meg az adott erőforrás-szolgáltató kibocsátási megjegyzéseit.

## <a name="get-support"></a>Támogatás kérése

Azure Stack hub ugyanazt a támogatási folyamatot követi, mint az Azure. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú megoldás-szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

A frissítési problémák elhárításához tekintse meg az [Azure stack hub javításával és frissítésével kapcsolatos hibaelhárítással kapcsolatos ajánlott eljárásokat](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stack Hubban](azure-stack-updates.md)
- [Ajánlott eljárások a Azure Stack hub javításával és frissítésével kapcsolatos hibák elhárításához](azure-stack-troubleshooting.md)