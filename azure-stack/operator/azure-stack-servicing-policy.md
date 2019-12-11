---
title: Azure Stack karbantartási szabályzat
titleSuffix: Azure Stack
description: Ismerje meg a Azure Stack karbantartási szabályzatát, valamint azt, hogyan tarthat fenn egy integrált rendszer támogatott állapotban.
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
ms.date: 10/30/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: c9f9558d1885de28b1bf685d370e67951542a6ac
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993796"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack karbantartási szabályzat

Ez a cikk ismerteti Azure Stack integrált rendszerek karbantartási szabályzatát, és azt, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon.

## <a name="download-update-packages-for-integrated-systems"></a>Frissítési csomagok letöltése az integrált rendszerekhez

A Microsoft a teljes havi frissítési csomagokat és a gyorsjavítási csomagokat is kibocsátja a konkrét problémák megoldására.

A havi frissítési csomagokat egy biztonságos Azure-végpont tárolja. Ezeket manuálisan is letöltheti a [Azure stack Updates Downloader eszközzel](https://aka.ms/azurestackupdatedownload). Ha a skálázási egység csatlakoztatva van, a frissítés **elérhetőként**automatikusan megjelenik a felügyeleti portálon. A teljes, havi frissítési csomagok minden kiadásban jól dokumentálva vannak. Az egyes kiadásokkal kapcsolatos további információkért kattintson a jelen cikk [frissítési csomag kiadási lépésszám](#update-package-release-cadence) szakaszának bármelyik kiadására.

A gyorsjavítás-frissítési csomagok ugyanazon a biztonságos Azure-végponton futnak. Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozások használatával töltheti le. például [Azure stack gyorsjavítások 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). A teljes, havi frissítési csomagokhoz hasonlóan Azure Stack operátorok az. XML,. bin és. exe fájlokat is letöltik, és a [frissítések alkalmazása a Azure stack-ban](azure-stack-apply-updates.md)című eljárással importálhatók. A csatlakoztatott méretezési egységeket Azure Stack operátorok automatikusan megjelennek a felügyeleti portálon, az üzenet **frissítésével**együtt.

Ha a méretezési egység nincs csatlakoztatva, és értesítést szeretne kapni az egyes gyorsjavításokról, fizessen elő az [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) -vagy [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) -hírcsatornára, amely minden kiadásban fel van tüntetve.

## <a name="update-package-types"></a>Frissítési csomagok típusai

Az integrált rendszerek két típusú frissítési csomaggal rendelkeznek:

- **Microsoft-szoftverfrissítések**. A Microsoft a Microsoft szoftverfrissítési csomagjainak teljes körű karbantartási életciklusát felelős. Ezek a csomagok magukban foglalhatják a Windows Server legújabb biztonsági frissítéseit, a nem biztonsági frissítéseket és a Azure Stack szolgáltatás frissítéseit. A tézisek frissítési csomagjai közvetlenül a Microsofttól tölthetők le.

- **OEM hardver gyártó által megadott frissítések**. Azure Stack hardveres partnerek felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusához (beleértve az útmutatást is). Emellett Azure Stack a hardveres partnereknek is, és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="update-package-release-cadence"></a>A csomag kiadási ritmusának frissítése

A Microsoft havi ütemben várja a szoftverfrissítési csomagok kiadását. Előfordulhat azonban, hogy egy hónapon belül több vagy nem rendelkezik frissítési kiadással. Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket.

A frissítések tervezésével és kezelésével, valamint az aktuális verzió meghatározásával kapcsolatos további információkért tekintse meg a frissítések [kezelése című témakörben](azure-stack-updates.md)található dokumentációt.

Egy adott frissítéssel kapcsolatos információkért, beleértve a letöltés módját is, tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack 1910 frissítés](/azure-stack/operator/release-notes?view=azs-1910)
- [Azure Stack 1908 frissítés](/azure-stack/operator/release-notes?view=azs-1908)
- [Azure Stack 1907 frissítés](/azure-stack/operator/release-notes?view=azs-1907)
- [Azure Stack 1906 frissítés](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Gyorsjavítások

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Alkalmanként a Microsoft gyorsjavításokat biztosít Azure Stack, amelyek gyakran megelőző vagy időérzékeny problémát jelentenek. Az egyes gyorsjavítások a Microsoft Tudásbázis megfelelő cikkében jelennek meg, amely a probléma, az ok és a megoldás részleteit ismerteti.

A gyorsjavítások letöltése és telepítése ugyanúgy történik, mint a Azure Stack normál teljes frissítési csomagjai. Azonban a teljes frissítéstől eltérően a gyorsjavítások percek alatt telepíthetők. Javasoljuk, hogy a gyorsjavítások telepítésekor Azure Stack operátorok állítsanak be karbantartási időszakokat. A gyorsjavítások frissítik a Azure Stack-felhő verzióját, így könnyen megállapítható, hogy a gyorsjavítás alkalmazása megtörtént-e. Külön gyorsjavítást kell megadnia a Azure Stack minden olyan verziójához, amely továbbra is támogatott. Egy adott iterációhoz tartozó javítások kumulatívak, és az adott verzió korábbi frissítéseit tartalmazzák. A megfelelő Tudásbázis-cikkben további információt talál egy adott gyorsjavítás alkalmazhatóságáról. Tekintse meg a kibocsátási megjegyzések hivatkozásait az előző szakaszban.

A jelenleg elérhető gyorsjavításokkal kapcsolatos további információkért tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack 1910 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Azure Stack 1908 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-1)
- [Azure Stack 1907 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2)
- [Azure Stack 1906 gyorsjavítás](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3)

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ahhoz, hogy a Azure Stack példány továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy futtatnia kell a két korábbi frissítési verzió valamelyikét.

A gyorsjavítások nem tekintendők jelentős frissítési verzióknak. Ha a Azure Stack-példánya *több mint két frissítés*mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

## <a name="get-support"></a>Támogatás kérése

Azure Stack az Azure-beli támogatási folyamatot követi. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú megoldás-szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések

- [Frissítések kezelése az Azure Stackben](azure-stack-updates.md)
