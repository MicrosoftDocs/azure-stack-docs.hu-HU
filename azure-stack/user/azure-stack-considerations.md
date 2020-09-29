---
title: Azure Stack hub és az Azure közötti különbségek szolgáltatások és alkalmazások létrehozásakor
description: Ismerje meg az Azure és a Azure Stack hub közötti különbséget a szolgáltatások használata és az alkalmazások létrehozása során.
author: sethmanheim
ms.topic: overview
ms.date: 09/21/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 25d836bece262f881901df6c62b5dc8f4aeaf11d
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946462"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Azure Stack hub és az Azure közötti különbségek szolgáltatások és alkalmazások létrehozásakor

Az Azure Stack hub szolgáltatásainak használata vagy az alkalmazások összeállítása előtt fontos megérteni az Azure Stack hub és az Azure közötti különbségeket. Ez a cikk a Azure Stack hub hibrid felhőalapú fejlesztési környezetként való használatakor felmerülő különböző funkciókat és kulcsfontosságú szempontokat ismerteti.

## <a name="overview"></a>Áttekintés

Az Azure Stack hub egy hibrid felhőalapú platform, amely lehetővé teszi az Azure-szolgáltatások használatát a vállalat vagy a szolgáltató adatközpontjában. Létrehozhat egy alkalmazást Azure Stack hub-on, majd üzembe helyezheti Azure Stack hubhoz, az Azure-ba vagy az Azure hibrid felhőbe.

A Azure Stack hub-operátor jelzi, hogy mely szolgáltatások érhetők el, és hogyan kérhet támogatást. Ezeket a szolgáltatásokat a testreszabott csomagjaik és ajánlatok segítségével kínálják.

Az [Azure technikai dokumentációjának tartalma](/azure) feltételezi, hogy az alkalmazásokat egy Azure-szolgáltatáshoz fejlesztették ki, nem pedig Azure stack hubhoz. Ha Azure Stack hubhoz hoz létre és telepít alkalmazásokat, meg kell ismernie néhány fontos különbséget, például:

* Azure Stack hub az Azure-ban elérhető szolgáltatások és funkciók egy részét biztosítja.
* A vállalata vagy szolgáltatója kiválaszthatja, hogy mely szolgáltatásokat szeretné nyújtani. Az elérhető lehetőségek lehetnek testreszabott szolgáltatások vagy alkalmazások. Saját testreszabott dokumentációt is biztosíthatnak.
* Használja a megfelelő Azure Stack Hub-specifikus végpontokat (például a portál címének URL-címe és a Azure Resource Manager végpont).
* Az Azure Stack hub által támogatott PowerShell-és API-verziókat kell használnia. A támogatott verziók használatával biztosíthatja, hogy alkalmazásai a Azure Stack hub-ban és az Azure-ban is működjenek.

## <a name="cheat-sheet-high-level-differences"></a>Cheat Sheet: magas szintű különbségek

Az alábbi táblázat az Azure Stack hub és az Azure közötti magas szintű különbségeket ismerteti. Tartsa szem előtt ezeket a különbségeket Azure Stack hub fejlesztésekor vagy Azure Stack hub-szolgáltatások használatakor:

| Terület | Azure (globális) | Azure Stack Hub |
| -------- | ------------- | ----------|
| Ki üzemelteti? | Microsoft | A szervezet vagy a szolgáltató.|
| Kihez forduljon a támogatási szolgálathoz? | Microsoft | Az integrált rendszerek támogatásához forduljon a Azure Stack hub-kezelőhöz (a szervezetnél vagy a szolgáltatónál).<br><br>A Azure Stack Development Kit (ASDK) támogatásához látogasson el a [Microsoft fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Mivel a fejlesztői csomag egy kiértékelési környezet, Microsoft ügyfélszolgálataon keresztül nem érhető el hivatalos támogatás.
| Elérhető szolgáltatások | Tekintse meg az [Azure-termékek](https://azure.microsoft.com/services/?b=17.04b)listáját. Az elérhető szolgáltatások az Azure-régiótól eltérőek. | Azure Stack hub az Azure-szolgáltatások egy részhalmazát támogatja. A tényleges szolgáltatások attól függően változnak, hogy a szervezet vagy a szolgáltató milyen ajánlatot tesz.
| Azure Resource Manager végpont * | `https://management.azure.com` | Azure Stack hub integrált rendszer esetén használja a Azure Stack hub-operátor által biztosított végpontot.<br><br>A fejlesztői csomaghoz használja a következőt: `https://management.local.azurestack.external` .
| Portál URL-címe * | [https://portal.azure.com](https://portal.azure.com) | Azure Stack hub integrált rendszer esetén használja a Azure Stack hub-operátor által biztosított URL-címet.<br><br>A fejlesztői csomaghoz használja a következőt: `https://portal.local.azurestack.external` .
| Region | Kiválaszthatja, hogy melyik régiót szeretné központilag telepíteni. | Azure Stack hub integrált rendszer esetén használja a rendszeren elérhető régiót.<br><br>A Azure Stack Development Kit (ASDK) esetében a régió mindig **helyi**lesz.
| Erőforráscsoportok | Az erőforráscsoportok A régiókra terjedhetnek. | Mind az integrált rendszerek, mind a fejlesztői csomag esetében csak egy régió van.
|Támogatott névterek, erőforrástípusok és API-verziók | A legújabb (vagy korábbi verziók, amelyek még nem elavultak). | Azure Stack hub bizonyos verziókat támogat. Tekintse meg a jelen cikk [verzióra vonatkozó követelmények](#version-requirements) című szakaszát.
| | |

* Ha Ön Azure Stack hub-operátor, további információért tekintse meg [a felügyeleti portál és az](../operator/azure-stack-manage-portals.md) [Adminisztráció alapjaival](../operator/azure-stack-manage-basics.md) foglalkozó témakört.

## <a name="helpful-tools-and-best-practices"></a>Hasznos eszközök és ajánlott eljárások

A Microsoft olyan eszközöket és útmutatást biztosít, amelyek segítenek az Azure Stack hub fejlesztésében.

| Ajánlás | Referencia |
| -------- | ------------- |
| Telepítse a megfelelő eszközöket a fejlesztői munkaállomásra. | - [A PowerShell telepítése](../operator/azure-stack-powershell-install.md)<br>- [Eszközök letöltése](../operator/azure-stack-powershell-download.md)<br>- [A PowerShell konfigurálása](azure-stack-powershell-configure-user.md)<br>- [A Visual Studio telepítése](azure-stack-install-visual-studio.md)
| Tekintse át a következő elemekre vonatkozó információkat:<br>– Azure Resource Manager a sablon szempontjait.<br>– Útmutató a Gyorsindítás sablonokhoz.<br>– Az Azure-ban Azure Stack hub fejlesztéséhez használható irányelvmodul segítségével. | [Fejlesztés Azure Stack hub-hoz](azure-stack-developer.md) |
| Tekintse át és kövesse a sablonok ajánlott eljárásait. | [Resource Manager gyorsindítási sablonok](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>A verzióra vonatkozó követelmények

Az Azure Stack hub a Azure PowerShell és az Azure Service API-k adott verzióit támogatja. A támogatott verziók használatával biztosíthatja, hogy az alkalmazás a Azure Stack hubhoz és az Azure-ba is telepíthető legyen.

Annak ellenőrzéséhez, hogy a Azure PowerShell megfelelő verzióját használja-e, használja az [API-verziók profiljait](azure-stack-version-profiles.md). A legújabb API-verzió profiljának meghatározásához tekintse meg az Ön által használt Azure Stack hub összeállítását. Ezt az információt az Azure Stack hub rendszergazdájától kérheti le.

> [!NOTE]
> Ha a Azure Stack Development Kit használja, és rendelkezik rendszergazdai hozzáféréssel, tekintse meg a [jelenlegi verzió meghatározása](../operator/azure-stack-updates.md) című szakaszt a Azure stack hub-Build meghatározásához.

Más API-k esetén futtassa a következő PowerShell-parancsot a Azure Stack hub-előfizetés által támogatott névterek, erőforrástípusok és API-verziók kimenetének kinyomtatásához (a tulajdonságok szintjén továbbra is lehetnek különbségek). Ahhoz, hogy a parancs működjön, rendelkeznie kell egy Azure Stack hub-környezethez már [telepített](../operator/azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-user.md) PowerShell-lel. Azure Stack hub-ajánlathoz is előfizetéssel kell rendelkeznie.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Példa kimenetre (csonkolt): ![ példa a Get-AzureRmResourceProvider parancs kimenetére](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Következő lépések

További információ a szolgáltatási szintbeli eltérésekről:

* [Az Azure Stack hub-beli virtuális gépekkel kapcsolatos megfontolások](azure-stack-vm-considerations.md)
* [Az Azure Stack hub tárolójának szempontjai](azure-stack-acs-differences.md)
* [A Azure Stack hub hálózatkezelésével kapcsolatos megfontolások](azure-stack-network-differences.md)
* [Az Azure Stack hub SQL erőforrás-szolgáltatóval kapcsolatos megfontolások](../operator/azure-stack-sql-resource-provider.md)
