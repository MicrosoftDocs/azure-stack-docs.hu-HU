---
title: Szolgáltatását az Azure Stack érvényesítési áttekintése |} A Microsoft Docs
description: Áttekintés az Azure Stack érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297919"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Mit jelent a szolgáltatását az Azure Stack érvényesítési?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Érvényesítés szolgáltatásként (VaaS) egy natív Azure-szolgáltatás, amelynek megoldás azon partnereinknek, akik a Microsoft Azure Stack-ajánlatok közös vannak mérnöki. Megoldáspartnerek a szolgáltatás használatával ellenőrizze, hogy megoldásaikat a Microsoft követelményeinek, és működnek megfelelően, az Azure Stack használatával.

VaaS elsődleges felhasználási módja van:

- Új Azure Stack-megoldások ellenőrzése
- Azure Stack szoftver módosítását ellenőrzése
- Üzembe helyezés során használt partner megoldáscsomagok digitális aláírása
- Előnézeti VaaS biztosíték tesztelése

## <a name="validate-a-new-azure-stack-solution"></a>Egy új Azure Stack megoldás ellenőrzése

Partnerek használata a **megoldás érvényesítési** munkafolyamat érvényesítése új Azure Stack megoldásokat. A megoldás sikeresen teljesítenie kell a szükséges hardver Lab Kit (HLK) az Azure Stack összetevő tesztjét. Hardverkonfigurációk számos tanúsításához a munkafolyamat kétszer esetében minden egyes új megoldást kell futtatni: egyszer egyenként a minimális és maximális konfigurációkat.

További információkért lásd: [érvényesíteni egy új Azure Stack megoldás](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Az Azure Stack szoftver ellenőrzése

Partnerek használata a **csomag** ellenőrizze, hogy a megoldás az Azure Stack legújabb szoftverfrissítésekkel működik-e a munkafolyamatot. A csomag érvényesítése munkafolyamat kell futtatni a Microsoft által ajánlott Hardverkörnyezet, javítási és frissítési (P & U) lett megadva a frissítés alkalmazásához. A munkafolyamat is futtathatók a referenciakonfiguráció build ajánlott.

További információkért lásd: [ellenőrzése a Microsoft szoftverfrissítések](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>A digitálisan aláírt megoldás partner csomagok beszerzése

Mellett az Azure Stack-frissítések ellenőrzése, a partnerek használata a **csomag** munkafolyamat számítógép-Gyártói testreszabási csomagokhoz, többek között az Azure Stack-partner-specifikus illesztőprogramokat, a belső vezérlőprogram és az egyéb szoftvereket frissítések ellenőrzése az Azure Stack szoftver központi telepítése során használt. Telepítse a csomagot, a jelenlegi verziója legalább a minimális méretű megoldást használni, amely támogatni fogja az Azure Stack szoftver vannak ellenőrzése. A csomag elküldve a VaaS tesztek végrehajtása előtt. Ha a vizsgálatok sikeresek, [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) , hogy a csomag vizsgálat befejeződött, és a digitális aláírással kell lennie az Azure Stack digitális aláírásával. A Microsoft aláírja a csomagot, és értesíti az Azure Stack-partner, hogy a csomag letölthető a VaaS portálon.

További információkért lásd: [ellenőrzése OEM csomagok](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Előzetes verzió VaaS biztosíték tesztelése

A Microsoft rendszeresen elérhetővé új funkciókat az Azure Stackben. Ezek a funkciók kidolgozását piacra a fejlesztési folyamat részeként a új teszt biztosíték szeretné elérhetővé tenni az a **Tesztmenetek** munkafolyamat. A tesztelési fázis munkafolyamat a munkafolyamatok lehetővé teszik a munkaköröket külsős informatikusok szoftvertesztek végrehajtása a teszt biztosíték tartalmaz. Ne használja a Tesztmenetek munkafolyamat elküldése jóváhagyásra eredményeket. A megoldás érvényesítési és a csomag munkafolyamatok segítségével a megoldás hivatalos jóváhagyást kérhet.

További információkért lásd: [a rövid útmutató: Az első vizsgálat ütemezése a portálon az érvényesítés használja](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Szűrőérvényesítési munkafolyamat tesztek összefoglalása

| Szűrőérvényesítési munkafolyamat | Szükséges tesztek |
|----|------------|
| [Új megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md) | Felhőbeli szimuláció motor<br>COMPUTE SDK üzemeltetési csomag<br>Lemez azonosítása teszt<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Hálózati SDK üzemeltetési csomag<br>Tárolási fiók SDK üzemeltetési csomag<br> |
| [OEM csomag érvényességének ellenőrzése](azure-stack-vaas-validate-oem-package.md) | OEM-bővítmény csomag ellenőrzése<br>Felhőbeli szimuláció motor |
| [Havi update-ellenőrzés](azure-stack-vaas-validate-microsoft-updates.md) | Az Azure Stack havi frissítés-ellenőrzés<br>Felhőbeli szimuláció motor<br> |

## <a name="next-steps"></a>További lépések

- [A szolgáltatási erőforrások, az ellenőrzés beállítása](azure-stack-vaas-set-up-resources.md)
- Ismerje meg [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)
