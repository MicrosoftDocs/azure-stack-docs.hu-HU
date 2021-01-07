---
title: API-verziók profiljainak kezelése Azure Stack hub-ban
description: Az API-verziók profiljainak megismerése Azure Stack hub-ban.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: fe28503ec5072cb11996563efcb70662bb1025fa
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97973962"
---
# <a name="manage-api-version-profiles-in-azure-stack-hub"></a>API-verziók profiljainak kezelése Azure Stack hub-ban

Az API-profilok megadják az Azure erőforrás-szolgáltatót és az API-verziót az Azure REST-végpontokhoz. Az API-profilok használatával különböző nyelveken hozhat létre egyéni ügyfeleket. Mindegyik ügyfél egy API-profilt használ a Azure Stack hub megfelelő erőforrás-szolgáltatójának és API-verziójának a meghívásához.

Létrehozhat egy alkalmazást az Azure-erőforrás-szolgáltatókkal való együttműködéshez anélkül, hogy meg kellene tudnia, hogy az egyes erőforrás-szolgáltatói API melyik verziója kompatibilis az Azure Stack hubhoz. Egyszerűen igazítsa az alkalmazást egy profilhoz, és az SDK visszaállít a megfelelő API-verzióra.

Ez a témakör a következőkben nyújt segítséget:

- Az Azure Stack hub API-profiljainak megismerése.
- Ismerje meg, hogyan fejlesztheti a megoldásait az API-profilok használatával.
- Megtudhatja, hol található a kód-specifikus útmutató.

## <a name="summary-of-api-profiles"></a>API-profilok összefoglalása

- Az API-profilok az Azure-erőforrás-szolgáltatók és az API-verziók készletének ábrázolására szolgálnak.
- Az API-profilok azért jöttek létre, hogy több Azure-felhőben hozzon létre sablonokat. A profilok kompatibilis és stabil felületet biztosítanak.
- A profilok évente négy alkalommal jelennek meg.
- A rendszer három profil elnevezési konvenciót használ:
  - **legújabb**  
        A globális Azure-ban megjelent legújabb API-verziókat tartalmazza.
  - **éééé-hh-nn-Hybrid**  
    Ez a kiadás félévente jelent meg, és a több felhők közötti konzisztencia és stabilitás. Ez a profil optimális Azure Stack hub-kompatibilitást céloz meg.
  - **éééé-hh-nn-profil** <br>
    Kiegyensúlyozza az optimális stabilitást és a legújabb funkciókat.

## <a name="azure-api-profiles-and-azure-stack-hub-compatibility"></a>Azure API-profilok és Azure Stack hub kompatibilitása

A legújabb Azure API-profilok nem kompatibilisek Azure Stack hubhoz. A következő elnevezési konvenciók segítségével azonosíthatja a Azure Stack hub-megoldásokhoz használandó profilokat:

**Legújabb**  
Ez a profil a globális Azure-ban található legnaprakészebb API-verziókkal rendelkezik, amelyek nem működnek Azure Stack központban. A **legújabb** a legnagyobb számú megszakítási módosítást. A profil a stabilitást és a más Felhőkkel való kompatibilitást is lehetővé teszi. Ha a legnaprakészebb API-verziókat próbálja használni, a **legújabb** profilt kell használnia.

**Éééé-hh-nn-Hybrid**  
Ez a profil évente, márciusban és szeptemberben jelent meg. Az optimális stabilitást és a különböző Felhőkkel való kompatibilitást is lehetővé teszi, és célja a globális Azure és Azure Stack hub megcélzása. Az ebben a profilban felsorolt Azure API-verziók ugyanazok lesznek, mint az Azure Stack hub-ban felsoroltak. Használja ezt a profilt a hibrid felhőalapú megoldások kódjának fejlesztéséhez.

**éééé-hh-nn-profil**  
Ezt a profilt júniusban és decemberben adták ki a globális Azure-hoz. Nem működik Azure Stack hub-vel, és általában sok törési változás történik. Annak ellenére, hogy az optimális stabilitást és a legújabb funkciókat is kiegyensúlyozza, a **legújabb** és a profil közötti különbség az, hogy a **legújabb** , az API kiadásának helyétől függetlenül mindig a legújabb API-verziókból áll. Ha például egy új API-verzió jön létre a számítási API-hoz holnap, az API-verzió a **legújabb**, de nem az **éééé-hh-nn-profil** profilban szerepel, mert ez a profil már létezik. az **éééé-hh-nn-profil** a június vagy december előtt kiadott legnaprakészebb verziókra terjed ki.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profilok

Azure Stack hub nem a globális Azure-ban található API-verziók legújabb verzióját használja. Megoldás létrehozásakor meg kell keresnie az API-verziót minden olyan Azure-erőforrás-szolgáltatónál, amely kompatibilis Azure Stack hubhoz.

Ahelyett, hogy az összes erőforrás-szolgáltatót és az Azure Stack hub által támogatott adott verziót használja, használhat API-profilt. A profil erőforrás-szolgáltatókat és API-verziókat határoz meg. A SDK-val létrehozott SDK vagy egy eszköz, amely a profilban megadott célhoz fog visszatérni `api-version` . Az API-profilok segítségével megadhatja a teljes sablonra érvényes profil-verziót. Futásidőben a Azure Resource Manager kiválasztja az erőforrás megfelelő verzióját.

Az API-profilok olyan eszközökkel működnek, amelyek Azure Resource Manager, például a PowerShell, az Azure CLI, az SDK-ban megadott kód és a Microsoft Visual Studio használatával működnek. Az eszközök és az SDK-k profilok használatával olvashatják el, hogy a modulok és kódtárak melyik verziója szerepeljen az alkalmazás létrehozásakor.

Ha például a PowerShell használatával hoz létre egy Storage-fiókot a **Microsoft. Storage** erőforrás-szolgáltató használatával, amely támogatja a 2016-03-30 **-es API-verziót** és egy virtuális gépet, amely a **Microsoft. számítási** erőforrás-szolgáltatót használja a 2015-12-01 **-es verziójú API-** val, akkor meg kell keresnie, hogy melyik PowerShell-modul támogatja a 2016-03-30 for Storage szolgáltatást, és hogy melyik modul támogatja a 2015-02-01 használatát Ehelyett használhatja a profilt. Használja a parancsmagot `Install-Profile <profilename>` , és a PowerShell betölti a modulok helyes verzióját.

Hasonlóképpen, ha a Python SDK-t használja egy Python-alapú alkalmazás létrehozásához, megadhatja a profilt. Az SDK betölti a megfelelő modulokat a parancsfájlban megadott erőforrás-szolgáltatóknak.

Fejlesztőként a megoldás megírására koncentrálhat. Az API-verziók, az erőforrás-szolgáltató és a felhő együttes használata helyett használhatja a profilt, és tudja, hogy a kód a profilt támogató összes felhőben működik.

## <a name="api-profile-code-samples"></a>API-profil kódja – minták

Az Azure Stack hub használatával a profilok segítségével integrálhatja megoldásait az előnyben részesített nyelvvel. Jelenleg a következő nyelvekhez talál útmutatást és mintákat:

- **.NET** <br>
Használja a .NET API-profilt az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verziójának beszerzéséhez. További információ: az [API-verziók profiljainak használata a .net-ben Azure stack hub-ban](azure-stack-version-profiles-net.md).
- **PowerShell**  
Használja az az  **. bootstrapper** modult az PowerShell-galériaon keresztül az API-verzió profiljainak használatához szükséges PowerShell-parancsmagok beszerzéséhez. További információ: [API-verziók profiljainak használata a powershellhez](azure-stack-version-profiles-powershell.md).
Használja az PowerShell-galéria elérhető  **AzureRM. bootstrapper** modult az API-verzió profiljainak használatához szükséges PowerShell-parancsmagok beszerzéséhez. További információ: [API-verziók profiljainak használata a powershellhez](../operator/powershell-install-az-module.md).
- **Azure CLI**  
Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. További információ: az [API-verziók profiljainak használata az Azure CLI-hez](azure-stack-version-profiles-azurecli2.md).
- **Ugrás**  
A go SDK-ban a profilok különböző típusú, különböző szolgáltatásokból származó verziókkal rendelkező erőforrástípusok kombinációját jelentik. A profilok a profilok/elérési út alatt, a verziószámuk **éééé-hh-nn** formátumban érhetők el. További információ: az [API-verziók profiljainak használata a go](azure-stack-version-profiles-go.md)-hoz.
- **Ruby**  
Az Azure Stack hub Resource Managerhez készült Ruby SDK olyan eszközöket biztosít, amelyek segítenek az infrastruktúra kiépítésében és kezelésében. Az SDK erőforrás-szolgáltatói közé tartozik a kiszámítások, a virtuális hálózatok és a Ruby nyelvű tárterület. További információ: [API-verziók profiljainak használata a Ruby használatával](azure-stack-version-profiles-ruby.md).
- **Python**  
A Python SDK támogatja az API-verziók profiljait különböző felhőalapú platformok, például az Azure Stack hub és a globális Azure megcélzásához. Az API-profilok használatával megoldásokat hozhat létre hibrid felhőhöz. További információ: [API-verziók profiljainak használata a Python használatával](azure-stack-version-profiles-python.md).
- **Node.js**  
Az Azure Stack hub Resource Managerhez készült Node.js SDK olyan eszközöket biztosít, amelyek segítenek az infrastruktúra kiépítésében és kezelésében. További információ: az [API-verziók profiljainak használata a Node.jshasználatával ](azure-stack-version-profile-nodejs.md).

## <a name="next-steps"></a>Következő lépések

- [A PowerShell telepítése Azure Stack hubhoz](../operator/powershell-install-az-module.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)
- [Tekintse át a profilok által támogatott erőforrás-szolgáltató API-verziók részleteit](azure-stack-profiles-azure-resource-manager-versions.md).
