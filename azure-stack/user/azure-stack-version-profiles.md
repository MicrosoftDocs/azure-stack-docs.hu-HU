---
title: API-verziók profiljainak kezelése a Azure Stackban | Microsoft Docs
description: A Azure Stack API-verzió profiljainak megismerése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 2d57c0ff71cd0aa4280e6a21df4727ee4bbe7fb6
ms.sourcegitcommit: 7961fda0bfcdd3db8cf94a8c405b5c23a23643af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68616496"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>API-verziók profiljainak kezelése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

API-profilok adja meg, az Azure erőforrás-szolgáltató és az Azure REST-végpontokat az API-verzió. Az API-profilok használatával különböző nyelveken hozhat létre egyéni ügyfeleket. Mindegyik ügyfél API-profilt használ a megfelelő erőforrás-szolgáltatóhoz és az Azure Stack API-verziójához való kapcsolódáshoz.

Létrehozhat egy alkalmazást az Azure-erőforrás-szolgáltatókkal való együttműködéshez anélkül, hogy rendeznie kellene, hogy az egyes erőforrás-szolgáltatói API melyik verziója kompatibilis a Azure Stackokkal. Egyszerűen igazítsa az alkalmazást egy profilhoz, és az SDK visszaállít a megfelelő API-verzióra.

Ez a témakör a következőket segíti elő:

 - Az Azure Stack API-profiljainak megismerése.
 - Ismerje meg, hogyan fejlesztheti a megoldásait az API-profilok használatával.
 - Megtudhatja, hol található a kód-specifikus útmutató.

## <a name="summary-of-api-profiles"></a>API-profilok összefoglalása

- Az API-profilok az Azure-erőforrás-szolgáltatók és az API-verziók készletének ábrázolására szolgálnak.
- Az API-profilok azért jöttek létre, hogy több Azure-felhőben hozzon létre sablonokat. A profilok kompatibilis és stabil felületet biztosítanak.
- A profilok évente négy alkalommal jelennek meg.
- A rendszer három profil elnevezési konvenciót használ:
    - **latest**  
        A globális Azure-ban megjelent legújabb API-verziókat tartalmazza.
    - **yyyy-mm-dd-hybrid**  
    Megjelent a kétévente, ez a kiadás a több felhők konzisztenciájára és stabilitására koncentrál. Ez a profil optimális Azure Stack kompatibilitást céloz meg.
    - **yyyy-mm-dd-profile** <br>
    Kiegyensúlyozza az optimális stabilitást és a legújabb funkciókat.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API-profilok és Azure Stack kompatibilitás

A legújabb Azure API-profilok nem kompatibilisek a Azure Stackokkal. A következő elnevezési konvenciók segítségével azonosíthatja a Azure Stack-megoldásokhoz használandó profilokat:

**Legújabb**  
Ez a profil a globális Azure-ban található legnaprakészebb API-verziókkal rendelkezik, amelyek nem működnek Azure Stackban. A **legújabb** a legnagyobb számú megszakítási módosítást. A profil a stabilitást és a más Felhőkkel való kompatibilitást is lehetővé teszi. Ha a legnaprakészebb API-verziókat próbálja használni, a **legújabb** profilt kell használnia.

**Yyyy-mm-dd-hybrid**  
Ez a profil évente, márciusban és szeptemberben jelent meg. Az optimális stabilitás és a különböző Felhőkkel való kompatibilitás. Az **éééé-hh-dd-Hybrid** a globális Azure-ra és a Azure Stackra van kialakítva. Az ebben a profilban felsorolt Azure API-verziók ugyanazok lesznek, mint a Azure Stackban felsoroltak. Használja ezt a profilt a hibrid felhőalapú megoldások kódjának fejlesztéséhez.

**yyyy-mm-dd-profile**  
Ezt a profilt júniusban és decemberben adták ki a globális Azure-hoz. Nem működik a Azure Stack, és a rendszer általában sok megszakítást hajt végre. Annak ellenére, hogy az optimális stabilitást és a legújabb funkciókat is kiegyensúlyozza, a **legújabb** és a profil közötti különbség az, hogy a **legújabb** , az API kiadásának helyétől függetlenül mindig a legújabb API-verziókból áll. Ha például egy új API-verzió jön létre a számítási API-hoz holnap, az API-verzió a **legújabb**, de nem az **éééé-hh-nn-profil** profilban szerepel, mert ez a profil már létezik. az **éééé-hh-nn-profil** a június vagy december előtt kiadott legnaprakészebb verziókra terjed ki.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profilok

A Azure Stack nem a globális Azure-ban található API-verziók legújabb verzióját használja. Megoldás létrehozásakor meg kell keresnie az API-verziót minden olyan Azure-erőforrás-szolgáltatónál, amely kompatibilis a Azure Stackával.

Az összes erőforrás-szolgáltató és a Azure Stack által támogatott adott verzió helyett használhat API-profilt. A profil erőforrás-szolgáltatókat és API-verziókat határoz meg. A SDK-val létrehozott SDK vagy egy eszköz, amely a profilban megadott célhoz `api-version` fog visszatérni. Az API-profilok segítségével megadhatja a teljes sablonra érvényes profil-verziót. Futásidőben a Azure Resource Manager kiválasztja az erőforrás megfelelő verzióját.

Az API-profilok olyan eszközökkel működnek, amelyek Azure Resource Manager, például a PowerShell, az Azure CLI, az SDK-ban megadott kód és a Microsoft Visual Studio használatával működnek. Az eszközök és az SDK-k profilok használatával olvashatják el, hogy a modulok és kódtárak melyik verziója szerepeljen az alkalmazás létrehozásakor.

Ha például a PowerShell használatával hoz létre egy Storage-fiókot a **Microsoft. Storage** erőforrás-szolgáltató használatával, amely támogatja a 2016-03-30 **-es API-verziót** és egy virtuális gépet, amely a **Microsoft. számítási** erőforrás-szolgáltatót és az **API-verziót használja.** 2015-12-01, meg kell keresnie, hogy melyik PowerShell-modul támogatja a 2016-03-30 a Storage-hoz, és melyik modul támogatja a 2015-02-01-et a számítási feladatokhoz, majd telepíti őket. Ehelyett használhatja a profilt. Használja a parancsmagot `Install-Profile <profilename>`, és a PowerShell betölti a modulok megfelelő verzióját.

Hasonlóképpen, ha a Python SDK-t használja egy Python-alapú alkalmazás létrehozásához, megadhatja a profilt. Az SDK betölti a megfelelő modulokat a parancsfájlban megadott erőforrás-szolgáltatóknak.

Fejlesztőként a megoldás megírására koncentrálhat. Az API-verziók, az erőforrás-szolgáltató és a felhő együttes használata helyett használhatja a profilt, és tudja, hogy a kód a profilt támogató összes felhőben működik.

## <a name="api-profile-code-samples"></a>API-profil kódja – minták

Itt megtalálhatja a megoldásnak az előnyben részesített nyelvvel való integrálásához szükséges Azure Stack a profilok használatával. Jelenleg a következő nyelvekhez talál útmutatást és mintákat:

- **.NET** <br>
Használja a .NET API-profilt az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verziójának beszerzéséhez. További információ: az [API-verziók profiljainak használata a .net-ben Azure stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Használja az PowerShell-galéria elérhető **AzureRM. bootstrapper** modult az API-verzió profiljainak használatához szükséges PowerShell-parancsmagok beszerzéséhez. További információ: [API-verziók profiljainak használata a powershellhez](azure-stack-version-profiles-powershell.md).
- **Azure CLI**  
Frissítse környezeti konfigurációját a Azure Stack-specifikus API-verzió profiljának használatára. További információ: az [API-verziók profiljainak használata az Azure CLI-hez](azure-stack-version-profiles-azurecli2.md).
- **Go**  
A go SDK-ban a profilok különböző típusú, különböző szolgáltatásokból származó verziókkal rendelkező erőforrástípusok kombinációját jelentik. A profilok a profilok/elérési út alatt, a verziószámuk **éééé-hh-nn** formátumban érhetők el. További információ: az [API-verziók profiljainak használata a go](azure-stack-version-profiles-go.md)-hoz.
- **Ruby**  
A Azure Stack Resource Managerhez készült Ruby SDK eszközöket biztosít az infrastruktúra kiépítéséhez és kezeléséhez. Az SDK erőforrás-szolgáltatói közé tartozik a kiszámítások, a virtuális hálózatok és a Ruby nyelvű tárterület. További információ: [API-verziók profiljainak használata a Ruby használatával](azure-stack-version-profiles-ruby.md).
- **Python**  
A Python SDK támogatja az API-verziók profiljait különböző felhőalapú platformok, például a Azure Stack és a globális Azure megcélzásához. Az API-profilok használatával megoldásokat hozhat létre hibrid felhőhöz. További információ: [API-verziók profiljainak használata a Python használatával](azure-stack-version-profiles-python.md).
- **Node.js**  
A Azure Stack Resource Managerhez készült Node. js SDK olyan eszközöket biztosít, amelyek segítenek az infrastruktúra kiépítésében és kezelésében. További információ: API- [verziók profiljainak használata a Node. js használatával](azure-stack-version-profile-nodejs.md).

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
* [A Azure Stack felhasználó PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)
* [Tekintse át a profilok által támogatott erőforrás-szolgáltató API-verziók részleteit](azure-stack-profiles-azure-resource-manager-versions.md).
