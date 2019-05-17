---
title: Az Azure Stackben API-verzióprofilok kezelése |} A Microsoft Docs
description: További információ az Azure Stackben API-verzióprofilok.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 1557403536764f8aa88a271d183436a3d35954ae
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782480"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Az Azure Stackben API-verzióprofilok kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

API-profilok adja meg, az Azure erőforrás-szolgáltató és az Azure REST-végpontokat az API-verzió. Létrehozhat egyéni ügyfelek különböző nyelveken, API-profilok használatával. Mindegyik ügyfél API-profilt használ a megfelelő erőforrás-szolgáltatóhoz és az Azure Stack API-verziójához való kapcsolódáshoz.

Létrehozhat egy alkalmazást az Azure erőforrás-szolgáltatók dolgozhat ki, hogy pontosan mely mindegyik erőforrás-szolgáltató API verziója kompatibilis az Azure Stack rendezés nélkül. Az alkalmazás-profilhoz; csak igazítása Az SDK-t a megfelelő API-verzió vált át.

Ez a témakör segítségével:

 - API-t a profilok megismerése az Azure Stackhez.
 - Ismerje meg, hogyan használhat API-t, a megoldások fejlesztése.
 - Tekintse meg, hogy hol található a kód jellemző útmutatást.

## <a name="summary-of-api-profiles"></a>API-profilok összefoglalása

- API-profilok segítségével az Azure erőforrás-szolgáltatók és a saját API-verziók jelölik.
- API-profilokat hozhat létre a sablonok között több Azure-felhők jöttek létre. Profilok egy stabil és kompatibilis felületet kínálnak.
- Profilok évente négy alkalommal jelennek meg.
- Három profilt elnevezési konvenciókat használja:
    - **latest**  
        Tartalmazza a legújabb API-verziók, amely a globális Azure-ban.
    - **yyyy-mm-dd-hybrid**  
    Kiadás dátuma: kétévente történik, ebben a kiadásban összpontosít és stabilabbá teheti több felhő között. Ez a profil optimális Azure Stack-kompatibilitási célozza.
    - **éééé-hh-nn-profil** elosztja a optimális stabilitását és a legújabb funkciókhoz.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Az Azure API-profilok és az Azure Stack-kompatibilitás

A legújabb Azure API-profilok nem kompatibilisek az Azure Stack használatával. A következő tanúsítványelnevezési módszerek segítségével azonosíthatja a profilok használata az Azure Stack-megoldások számára.

**legfrissebb**  
Ez a profil van a legfrissebb API-verziók található a globális Azure-ban, amely nem működnek az Azure Stackben. **Legújabb** kompatibilitástörő változásokat legnagyobb száma. A profil fenntartott helyezi, stabilitás és más felhőkben való kompatibilitás érdekében. Ha szeretne a legújabb API-verziókat használhat **legújabb** a profilt kell használnia.

**Yyyy-mm-dd-hybrid**  
Ez a profil jelent márciusi és szeptemberi frissítések minden évben. Optimális stabilitását és különféle felhőkben való kompatibilitás rendelkezik. **Éééé-hh-nn-hibrid** célja, hogy a cél a globális Azure és az Azure Stackben. Az Azure API-verziói ehhez a profilhoz lesznek ugyanaz, mint az Azure Stacken felsorolt azokat. Ez a profil használatával kód a hibrid felhőalapú megoldások fejlesztése.

**yyyy-mm-dd-profile**  
Ez a profil jelent meg az Azure globális június és December. Nem működik az Azure Stack; általában lesz számos kompatibilitástörő változásokat. Bár ez elosztja a optimális stabilitását és a legújabb funkciók, az a különbség a között **legújabb** , és ezt a profilt, amely **legújabb** mindig a legújabb API-verziók, függetlenül attól, amikor az API-t volt áll kiadás dátuma. Például ha egy új API-verzió holnap létrehozása a Compute API-hoz, hogy API-verzió szerepel a **legújabb**, azonban nem a **éééé-hh-nn-profil** profil, mert ez a profil már létezik. **éééé-hh-nn-profil** június és December előtt kiadott a legújabb verziókat ismerteti.

## <a name="azure-resource-manager-api-profiles"></a>Az Azure Resource Manager API-profilok

Az Azure Stack nem használja a globális Azure-ban található API-verziók legújabb verzióját. Amikor létrehoz egy megoldást, minden egyes Azure erőforrás-szolgáltató, amely kompatibilis az Azure Stack használatával keresse meg az API-verziót.

Inkább, mint a minden erőforrás-szolgáltató és az Azure Stack által támogatott verziót kutatás, használhat egy API-profilt. A profil az erőforrás-szolgáltatók és API-verziók egy halmazát határozza meg. Az SDK-t vagy egy eszközt, az SDK visszaáll a cél `api-version` a profilban megadott. Az API-profiljaival megadhatja a profil verziója, amely egy teljes sablont és futásidőben vonatkozik, az Azure Resource Manager kiválasztja a megfelelő verziót az erőforrás.

API-profilok, amelyek használják az Azure Resource Managerrel, például a PowerShell, az Azure CLI, az SDK-t mind a Microsoft Visual Studio code tools együttműködve. Eszközök és SDK-k segítségével profilok olvassa el a modulok és a egy application készítése során alkalmazandó kódtárak melyik verzióját.

Például, ha a PowerShell használatával hozzon létre egy tárolási fiókot a **Microsoft.Storage** erőforrás-szolgáltató, amely támogatja a **api-version** 2016-03-30-as és a egy virtuális gépet a  **Microsoft.Compute** erőforrás-szolgáltató **api-version** 2015-12-01 Storage PowerShell modul támogatja a 2016-03-30 kell meg, és mely modul 2015-02-01 támogatja a számítás, majd Telepítse a frissítéseket. Ehelyett egy profil is használhatja. A parancsmag `Install-Profile <profilename>`, és a PowerShell betölti a modulokat megfelelő verzióját.

Ehhez hasonlóan ha Python-alapú alkalmazás készítése a Python SDK-t használja, megadhatja a profil. Az SDK-t a parancsfájlban megadott erőforrás-szolgáltatókat betölti a megfelelő modulokat.

A fejlesztők összpontosíthat a megoldás írása. Ahelyett, hogy melyik API verzió, az erőforrás-szolgáltató és a felhő kutatást működik együtt, -profillal, és tudja, hogy működik-e a kód, amely támogatja a profilt az összes felhőkben.

## <a name="api-profile-code-samples"></a>API-profil-Kódminták

Megtalálhatja a segítséget nyújtanak a választott nyelven az Azure Stack-profilok használatával a megoldás integrálása Kódminták. Jelenleg található útmutatás és példák a következő nyelveken:

- **.NET** a .NET API-profil segítségével egy erőforrás-szolgáltatói csomag az egyes erőforrástípusok legújabb és legnagyobb stabil verziójának beszerzéséhez. További információkért lásd: [a .NET-tel az Azure Stack használata API-verzióprofilok](azure-stack-version-profiles-net.md).
- **PowerShell**  
Használhatja a **AzureRM.Bootstrapper** modul elérhető a PowerShell-galériából, a PowerShell-parancsmagok használata API-verzióprofilok kell lekérni. További információ: [használata API-verzióprofilok PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI**  
Frissítheti az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. További információ: [használata API-verzióprofilok Azure CLI-hez](azure-stack-version-profiles-azurecli2.md).
- **Go**  
A Go SDK egy profil a különböző szolgáltatásokhoz különböző verziójú különböző erőforrástípusok kombinációját. A profilok/elérési úton, a verziójuk érhetők el profilok a **éééé-hh-nn** formátumban. További információ: [használata API-verzióprofilok Góhoz készült](azure-stack-version-profiles-go.md).
- **Ruby**  
A Ruby SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltató például számítási, a virtuális hálózatok és a tárolási Ruby nyelven. További információ: [használata API-verzióprofilok Ruby használatával](azure-stack-version-profiles-ruby.md)
- **Python**  
A Python SDK API-verzióprofilok célozhat meg például az Azure Stack és a globális Azure más felhőalapú platformot támogatja. API-profilok segítségével hibrid felhőbeli megoldások létrehozásához. További információ: [használata API-verzióprofilok Python használatával](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)
* [Tekintse át a profilok által támogatott erőforrás-szolgáltató API-ja verziókra](azure-stack-profiles-azure-resource-manager-versions.md).
