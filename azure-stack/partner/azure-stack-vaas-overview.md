---
title: Az Azure Stack hub szolgáltatásként való érvényesítésének áttekintése | Microsoft Docs
description: Azure Stack hub ellenőrzésének áttekintése szolgáltatásként.
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
ms.openlocfilehash: 0bd490b5eaf91540d7789c8eb7a7a2d34eb1ae4d
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595359"
---
# <a name="what-is-validation-as-a-service-for-azure-stack-hub"></a>Mi az a Azure Stack hub szolgáltatásként való érvényesítése?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az érvényesítési szolgáltatásként (az Azure-ban) egy natív Azure-szolgáltatás, amely olyan megoldási partnereink számára készült, akik a Microsofttal közös mérnöki Azure Stack hub-ajánlatokat biztosítanak. A megoldás partnerei használhatják a szolgáltatást annak ellenőrzését, hogy a megoldásaiik megfelelnek-e a Microsoft követelményeinek, és a várt módon működnek Azure Stack hub használatával.

Az "az" alapszolgáltatások elsődleges használata a következő:

- Új Azure Stack hub-megoldások ellenőrzése
- Az Azure Stack hub szoftver változásainak ellenőrzése
- Az üzemelő példányok digitális aláírására szolgáló megoldás partneri csomagjai
- Az Varga tesztelési biztosítékok előzetes megtekintése

## <a name="validate-a-new-azure-stack-hub-solution"></a>Új Azure Stack hub-megoldás érvényesítése

A partnerek a **megoldás-ellenőrzési** munkafolyamattal ellenőrzik az új Azure stack hub-megoldásokat. A megoldásnak át kell adnia a szükséges Hardware Lab Kit (HLK) Azure Stack hub-összetevők tesztjét. A hardverkonfiguráció tartományának hitelesítéséhez a munkafolyamatot kétszer kell futtatni minden új megoldás esetében: egyszer a minimális és a maximális konfigurációkhoz.

További információ: [új Azure stack hub-megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>Az Azure Stack hub szoftver változásainak ellenőrzése

A partnerek a **csomag-ellenőrzési** munkafolyamattal ellenőrzi, hogy a megoldás a legújabb Azure stack hub-szoftverfrissítések használatával működik-e. A csomag-ellenőrzési munkafolyamatot olyan Microsoft által ajánlott hardveres környezetben kell futtatni, ahol a frissítés alkalmazására a patch és a Update (P & U) használatos. Azt javasoljuk, hogy a munkafolyamatot az alapkonfiguráció összeállításán is futtassa.

További információ: [szoftverfrissítések érvényesítése a Microsofttól](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Digitálisan aláírt megoldási partner csomagjainak beolvasása

A Azure Stack hub frissítéseinek ellenőrzése mellett a partnerek a csomag- **ellenőrzési** munkafolyamattal ellenőrizhetik az OEM-testreszabási csomagok frissítéseit, amelyek közé tartoznak a Azure stack hub Partner-specifikus illesztőprogramjai, belső vezérlőprogram és egyéb szoftverek, amelyeket a Az Azure Stack hub szoftver üzembe helyezése. Telepítse az érvényesíteni kívánt csomagot az Azure Stack hub szoftver aktuális verziójára legalább a támogatott minimális méretű megoldás használatával. A rendszer a tesztek végrehajtása előtt elküldi a csomagot az Varga számára. Ha a tesztek sikeresek, értesítse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) arról, hogy a csomag befejezte a tesztelést, és digitálisan alá kell írnia az Azure stack hub digitális aláírásával. A Microsoft aláírja a csomagot, és értesíti a Azure Stack hub-partnert arról, hogy a csomag letölthető az alaprendszer-portálon.

További információ: OEM- [csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Az előzetes verziójú,

A Microsoft rendszeresen teszi elérhetővé Azure Stack hub új funkcióit. A szolgáltatások piacra való szállításának fejlesztési folyamata részeként új teszt-biztosítékot is elérhetővé tesznek a **test pass** munkafolyamatban. A test pass munkafolyamat a más munkafolyamatokból származó tesztelési biztosítékokat is tartalmaz, amelyek lehetővé teszik a nem hivatalos tesztek végrehajtását. Ne használja a test pass munkafolyamatot az eredmények jóváhagyásra való küldéséhez. A megoldás érvényesítése és a csomag ellenőrzése munkafolyamatok segítségével hivatalos jóváhagyást kaphat a megoldásához.

További információ [: gyors üzembe helyezés az ellenőrzés során a szolgáltatás-portálon az első teszt elvégzéséhez](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Ellenőrzési munkafolyamat-tesztek összegzése

| Érvényesítési munkafolyamat | Szükséges tesztek |
|----|------------|
| [Új megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md) | Felhőalapú szimulációs motor<br>Számítási SDK operatív csomagja<br>Lemez-azonosítási teszt<br>Kulcstartó bővítmény SDK operatív csomagja<br>Kulcstartó SDK operatív csomag<br>Hálózati SDK operatív csomag<br>Storage-fiók SDK operatív csomagja<br> |
| [OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md) | OEM kiterjesztési csomag ellenőrzése<br>Felhőalapú szimulációs motor |
| [Havi frissítés ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) | Havi Azure Stack hub frissítésének ellenőrzése<br>Felhőalapú szimulációs motor<br> |

## <a name="next-steps"></a>További lépések

- [Az érvényesítés beállítása szolgáltatási erőforrásként](azure-stack-vaas-set-up-resources.md)
- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)
