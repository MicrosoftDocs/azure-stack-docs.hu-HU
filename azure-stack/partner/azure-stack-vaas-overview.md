---
title: A Azure Stack szolgáltatás érvényesítésének áttekintése | Microsoft Docs
description: A szolgáltatás Azure Stack érvényesítésének áttekintése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d1cfc5c9c378ccfc48811c5896337ef91fdca2e8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418562"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Mi az érvényesítés a Azure Stack szolgáltatásként?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az érvényesítési szolgáltatásként (az Azure-ban) egy natív Azure-szolgáltatás, amely olyan megoldási partnereink számára készült, akik közös mérnöki Azure Stack ajánlatokat biztosítanak a Microsoft számára. A megoldás partnerei használhatják a szolgáltatást annak ellenőrzését, hogy a megoldásaiik megfelelnek-e a Microsoft követelményeinek, és a várt módon működnek Azure Stack.

Az "az" alapszolgáltatások elsődleges használata a következő:

- Új Azure Stack megoldások ellenőrzése
- A Azure Stack szoftver változásainak ellenőrzése
- Az üzemelő példányok digitális aláírására szolgáló megoldás partneri csomagjai
- Az Varga tesztelési biztosítékok előzetes megtekintése

## <a name="validate-a-new-azure-stack-solution"></a>Új Azure Stack megoldás érvényesítése

A partnerek a **megoldás-ellenőrzési** munkafolyamattal érvényesítik az új Azure stack megoldásokat. A megoldásnak át kell adnia a szükséges Hardware Lab Kit (HLK) Azure Stack összetevő-teszteket. A hardverkonfiguráció tartományának hitelesítéséhez a munkafolyamatot kétszer kell futtatni minden új megoldás esetében: egyszer a minimális és a maximális konfigurációkhoz.

További tudnivalókért tekintse meg az [új Azure stack megoldás érvényesítését](azure-stack-vaas-validate-solution-new.md)ismertető témakört.

## <a name="validate-changes-to-the-azure-stack-software"></a>A Azure Stack szoftver változásainak ellenőrzése

A partnerek a **csomag-ellenőrzési** munkafolyamattal ellenőrzi, hogy a megoldás működik-e a legújabb Azure stack szoftverfrissítések használatával. A csomag-ellenőrzési munkafolyamatot olyan Microsoft által ajánlott hardveres környezetben kell futtatni, ahol a frissítés alkalmazására a patch és a Update (P & U) használatos. Azt javasoljuk, hogy a munkafolyamatot az alapkonfiguráció összeállításán is futtassa.

További információ: [szoftverfrissítések érvényesítése a Microsofttól](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Digitálisan aláírt megoldási partner csomagjainak beolvasása

A Azure Stack frissítéseinek érvényesítése mellett a partnerek a **csomag-ellenőrzési** munkafolyamattal ellenőrizhetik az OEM-testreszabási csomagok frissítéseit, beleértve a Azure stack Partner-specifikus illesztőprogramjait, a belső vezérlőprogramot és a telepítése során használt egyéb szoftvereket is. a Azure Stack szoftver. Telepítse az érvényesíteni kívánt csomagot a Azure Stack szoftver aktuális verziójára legalább a támogatott minimális méretű megoldás használatával. A rendszer a tesztek végrehajtása előtt elküldi a csomagot az Varga számára. Ha a tesztek sikeresek, [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) értesítse, hogy a csomag befejezte a tesztelést, és digitálisan alá kell írnia a Azure stack digitális aláírással. A Microsoft aláírja a csomagot, és értesíti a Azure Stack partnert arról, hogy a csomag letölthető az alaprendszer-portálon.

További információ: OEM- [csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Az előzetes verziójú,

A Microsoft rendszeresen teszi elérhetővé Azure Stack új funkcióit. A szolgáltatások piacra való szállításának fejlesztési folyamata részeként új teszt-biztosítékot is elérhetővé tesznek a **test pass** munkafolyamatban. A test pass munkafolyamat a más munkafolyamatokból származó tesztelési biztosítékokat is tartalmaz, amelyek lehetővé teszik a nem hivatalos tesztek végrehajtását. Ne használja a test pass munkafolyamatot az eredmények jóváhagyásra való küldéséhez. A megoldás érvényesítése és a csomag ellenőrzése munkafolyamatok segítségével hivatalos jóváhagyást kaphat a megoldásához.

További információ: gyors útmutató [: Az érvényesítést szolgáltatásként szolgáló portálként használhatja az első teszt](azure-stack-vaas-schedule-test-pass.md)elvégzéséhez.

## <a name="validation-workflow-tests-summary"></a>Ellenőrzési munkafolyamat-tesztek összegzése

| Érvényesítési munkafolyamat | Szükséges tesztek |
|----|------------|
| [Új megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md) | Felhőalapú szimulációs motor<br>Számítási SDK operatív csomagja<br>Lemez-azonosítási teszt<br>Kulcstartó bővítmény SDK operatív csomagja<br>Kulcstartó SDK operatív csomag<br>Hálózati SDK operatív csomag<br>Storage-fiók SDK operatív csomagja<br> |
| [OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md) | OEM kiterjesztési csomag ellenőrzése<br>Felhőalapú szimulációs motor |
| [Havi frissítés ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) | Havi Azure Stack frissítés ellenőrzése<br>Felhőalapú szimulációs motor<br> |

## <a name="next-steps"></a>További lépések

- [Az érvényesítés beállítása szolgáltatási erőforrásként](azure-stack-vaas-set-up-resources.md)
- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)
