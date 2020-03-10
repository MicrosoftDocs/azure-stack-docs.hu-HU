---
title: Azure Stack hub karbantartási szabályzata
titleSuffix: Azure Stack Hub
description: Ismerje meg az Azure Stack hub karbantartási szabályzatát, valamint azt, hogyan tarthat egy integrált rendszer támogatott állapotú.
author: sethmanheim
ms.topic: article
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: f81ca3e48387f806e591deea08c4b0c9c518cebd
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367640"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack hub karbantartási szabályzata

Ez a cikk ismerteti az Azure Stack hub integrált rendszerek karbantartási szabályzatát, és azt, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon.

## <a name="download-update-packages-for-integrated-systems"></a>Frissítési csomagok letöltése az integrált rendszerekhez

A Microsoft a teljes havi frissítési csomagokat és a gyorsjavítási csomagokat is kibocsátja a konkrét problémák megoldására.

A havi frissítési csomagokat egy biztonságos Azure-végpont tárolja. Ezeket manuálisan is letöltheti az [Azure stack hub Updates Downloader eszközzel](https://aka.ms/azurestackupdatedownload). Ha a skálázási egység csatlakoztatva van, a frissítés **elérhetőként**automatikusan megjelenik a felügyeleti portálon. A teljes, havi frissítési csomagok minden kiadásban jól dokumentálva vannak. Az egyes kiadásokkal kapcsolatos további információkért kattintson a jelen cikk [frissítési csomag kiadási lépésszám](#update-package-release-cadence) szakaszának bármelyik kiadására.

A gyorsjavítás-frissítési csomagok ugyanazon a biztonságos Azure-végponton futnak. Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozások használatával töltheti le. például [Azure stack hub gyorsjavítási 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). A teljes, havi frissítési csomagokhoz hasonlóan Azure Stack hub-operátorok letöltik az. XML,. bin és. exe fájlokat, és importálják azokat a [frissítések alkalmazása a Azure stack hub-ban](azure-stack-apply-updates.md)című témakörben leírtak alapján. A csatlakoztatott méretezési egységekkel rendelkező Azure Stack hub-operátorok automatikusan megjelennek a felügyeleti portálon az üzenet **frissítése lehetőséggel**.

Ha a méretezési egység nincs csatlakoztatva, és értesítést szeretne kapni az egyes gyorsjavításokról, fizessen elő az [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) -vagy [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) -hírcsatornára, amely minden kiadásban fel van tüntetve.

## <a name="update-package-types"></a>Frissítési csomagok típusai

Az integrált rendszerek két típusú frissítési csomaggal rendelkeznek:

- **Microsoft-szoftverfrissítések**. A Microsoft a Microsoft szoftverfrissítési csomagjainak teljes körű karbantartási életciklusát felelős. Ezek a csomagok magukban foglalhatják a Windows Server legújabb biztonsági frissítéseit, a nem biztonsági frissítéseket és a Azure Stack hub szolgáltatás frissítéseit. A tézisek frissítési csomagjai közvetlenül a Microsofttól tölthetők le.

- **OEM hardver gyártó által megadott frissítések**. Azure Stack hub hardveres partnerei felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusának (beleértve az útmutatást is). Emellett a Azure Stack hub Hardware-partnerek saját és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="update-package-release-cadence"></a>A csomag kiadási ritmusának frissítése

A Microsoft havi ütemben várja a szoftverfrissítési csomagok kiadását. Előfordulhat azonban, hogy egy hónapon belül több vagy nem rendelkezik frissítési kiadással. Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket.

A frissítések tervezésével és kezelésével, valamint az aktuális verzió meghatározásával kapcsolatos további információkért tekintse meg a frissítések [kezelése című témakörben](azure-stack-updates.md)található dokumentációt.

Egy adott frissítéssel kapcsolatos információkért, beleértve a letöltés módját is, tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack hub 1910 frissítése](/azure-stack/operator/release-notes?view=azs-1910)
- [Azure Stack hub 1908 frissítése](/azure-stack/operator/release-notes?view=azs-1908)
- [Azure Stack hub 1907 frissítése](/azure-stack/operator/release-notes?view=azs-1907)
- [Azure Stack hub 1906 frissítése](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Gyorsjavítások

Alkalmanként a Microsoft gyorsjavításokat biztosít Azure Stack hubhoz, amely egy gyakran megelőző vagy időérzékeny probléma megoldására szolgál. Az egyes gyorsjavítások a Microsoft Tudásbázis megfelelő cikkében jelennek meg, amely a probléma, az ok és a megoldás részleteit ismerteti.

A gyorsjavítások letöltése és telepítése ugyanúgy történik, mint a Azure Stack hub normál teljes frissítési csomagjai. Azonban a teljes frissítéstől eltérően a gyorsjavítások percek alatt telepíthetők. Javasoljuk, hogy a gyorsjavítások telepítésekor Azure Stack hub-operátorok állítsanak be karbantartási időszakokat. A gyorsjavítások frissítik a Azure Stack hub-felhő verzióját, így könnyen megállapíthatja, hogy a gyorsjavítás alkalmazása megtörtént-e. Külön gyorsjavítást biztosítanak a Azure Stack hub minden olyan verziójához, amely továbbra is támogatott. Egy adott iterációhoz tartozó javítások kumulatívak, és az adott verzió korábbi frissítéseit tartalmazzák. A megfelelő Tudásbázis-cikkben további információt talál egy adott gyorsjavítás alkalmazhatóságáról. Tekintse meg a kibocsátási megjegyzések hivatkozásait az előző szakaszban.

A jelenleg elérhető gyorsjavításokkal kapcsolatos további információkért tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack hub 1910 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Azure Stack hub 1908 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-1)
- [Azure Stack hub 1907 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2)
- [Azure Stack hub 1906 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3)

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ahhoz, hogy a Azure Stack hub-példány továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy futtatnia kell a két korábbi frissítési verzió valamelyikét.

A gyorsjavítások nem tekintendők jelentős frissítési verzióknak. Ha a Azure Stack hub-példánya *több mint két frissítés*mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

## <a name="get-support"></a>Támogatás kérése

Azure Stack hub ugyanazt a támogatási folyamatot követi, mint az Azure. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú megoldás-szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

A frissítési problémák elhárításához tekintse meg az [Azure stack hub javításával és frissítésével kapcsolatos hibaelhárítással kapcsolatos ajánlott eljárásokat](azure-stack-updates-troubleshoot.md).

## <a name="next-steps"></a>Következő lépések

- [Frissítések kezelése Azure Stack központban](azure-stack-updates.md)
- [Ajánlott eljárások a Azure Stack hub javításával és frissítésével kapcsolatos hibák elhárításához](azure-stack-updates-troubleshoot.md)
