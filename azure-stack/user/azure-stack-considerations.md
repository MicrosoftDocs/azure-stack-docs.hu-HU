---
title: A Azure Stack és az Azure közötti különbségek szolgáltatások használatakor és alkalmazások létrehozásakor | Microsoft Docs
description: Ismerje meg az Azure és a Azure Stack közötti különbségeket a szolgáltatások használata és az alkalmazások létrehozása során.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/17/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a7a61e8eef33ee6a6efb87001504fe5234e3cf16
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303145"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>A Azure Stack és az Azure közötti különbségek szolgáltatások és alkalmazások létrehozásakor

A szolgáltatások használata vagy az alkalmazások Azure Stack való létrehozása előtt fontos megérteni a Azure Stack és az Azure közötti különbségeket. Ez a cikk különböző szolgáltatásokat és kulcsfontosságú szempontokat mutat be a hibrid felhőalapú fejlesztési környezet Azure Stack használatakor.

## <a name="overview"></a>Áttekintés

A Azure Stack egy hibrid felhőalapú platform, amely lehetővé teszi az Azure-szolgáltatások használatát a vállalat vagy a szolgáltató adatközpontjában. Létrehozhat egy alkalmazást Azure Stack, majd üzembe helyezheti Azure Stackba, az Azure-ba vagy az Azure hibrid felhőbe.

A Azure Stack operátorral megtudhatja, hogy mely szolgáltatások használhatók, és hogyan kérhet támogatást. Ezeket a szolgáltatásokat a testreszabott csomagjaik és ajánlatok segítségével kínálják.

Az Azure technikai dokumentációjának tartalma feltételezi, hogy az alkalmazásokat egy Azure-szolgáltatáshoz fejlesztették ki, nem pedig a Azure Stack. Amikor alkalmazásokat hoz létre és telepít Azure Stackre, meg kell ismernie néhány lényeges különbséget, például:

* Azure Stack az Azure-ban elérhető szolgáltatások és funkciók egy részét biztosítja.
* A vállalata vagy szolgáltatója kiválaszthatja, hogy mely szolgáltatásokat szeretné nyújtani. Az elérhető lehetőségek lehetnek testreszabott szolgáltatások vagy alkalmazások. Saját testreszabott dokumentációt is biztosíthatnak.
* A megfelelő Azure Stack-specifikus végpontokat kell használnia (például a portál címének URL-címe és a Azure Resource Manager végpont).
* A Azure Stack által támogatott PowerShell-és API-verziókat kell használnia. A támogatott verziók használatával biztosíthatja, hogy alkalmazásai a Azure Stack és az Azure-ban is működjenek.

## <a name="cheat-sheet-high-level-differences"></a>Cheat Sheet: Magas szintű különbségek

Az alábbi táblázat a Azure Stack és az Azure közötti magas szintű különbségeket ismerteti. Tartsa szem előtt ezeket a különbségeket Azure Stack vagy Azure Stack-szolgáltatások használata esetén:

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

| Terület | Azure (globális) | Azure Stack |
| -------- | ------------- | ----------|
| Ki üzemelteti? | Microsoft | A szervezet vagy a szolgáltató.|
| Kihez forduljon a támogatási szolgálathoz? | Microsoft | Az integrált rendszerek támogatásához forduljon a Azure Stack-kezelőhöz (a szervezetnél vagy a szolgáltatónál).<br><br>A Azure Stack Development Kit (ASDK) támogatásához látogasson el a [Microsoft fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft ügyfélszolgálati szolgálatai (CSS) nem kínálunk hivatalos támogatást.
| Elérhető szolgáltatások | Tekintse meg az [Azure-termékek](https://azure.microsoft.com/services/?b=17.04b)listáját. Az elérhető szolgáltatások az Azure-régiótól eltérőek. | Azure Stack támogatja az Azure-szolgáltatások egy részhalmazát. A tényleges szolgáltatások attól függően változnak, hogy a szervezet vagy a szolgáltató milyen ajánlatot tesz.
| Azure Resource Manager végpont * | https://management.azure.com | Azure Stack integrált rendszer esetén használja a Azure Stack operátor által biztosított végpontot.<br><br>A fejlesztői csomaghoz használja a következőt: https://management.local.azurestack.external.
| Portál URL-címe * | [https://portal.azure.com](https://portal.azure.com) | Azure Stack integrált rendszer esetén használja a Azure Stack operátor által biztosított URL-címet.<br><br>A fejlesztői csomaghoz használja a következőt: https://portal.local.azurestack.external.
| Régió | Kiválaszthatja, hogy melyik régiót szeretné központilag telepíteni. | Azure Stack integrált rendszer esetén használja a rendszeren elérhető régiót.<br><br>A fejlesztői csomag esetében a régió mindig **helyi**lesz.
| Erőforráscsoportok | Az erőforráscsoportok A régiókra terjedhetnek. | Mind az integrált rendszerek, mind a fejlesztői csomag esetében csak egy régió van.
|Támogatott névterek, erőforrástípusok és API-verziók | A legújabb (vagy korábbi verziók, amelyek még nem elavultak). | Azure Stack támogatja az adott verziókat. Tekintse meg a jelen cikk [verzióra vonatkozó követelmények](#version-requirements) című szakaszát.
| | |

\* Ha Ön Azure Stack operátor, további információért tekintse meg [a felügyeleti portál és az](../operator/azure-stack-manage-portals.md) [Adminisztráció alapjaival](../operator/azure-stack-manage-basics.md) foglalkozó témakört.

## <a name="helpful-tools-and-best-practices"></a>Hasznos eszközök és ajánlott eljárások

A Microsoft olyan eszközöket és útmutatást biztosít, amelyek segítenek a Azure Stack fejlesztésében.

| Ajánlás | Referencia |
| -------- | ------------- |
| Telepítse a megfelelő eszközöket a fejlesztői munkaállomásra. | - [A PowerShell telepítése](../operator/azure-stack-powershell-install.md)<br>- [Eszközök letöltése](../operator/azure-stack-powershell-download.md)<br>- [A PowerShell konfigurálása](azure-stack-powershell-configure-user.md)<br>- [A Visual Studio telepítése](azure-stack-install-visual-studio.md) 
| Tekintse át a következő elemekre vonatkozó információkat:<br>– Azure Resource Manager a sablonra vonatkozó megfontolások<br>– Útmutató a Gyorsindítás sablonokhoz<br>– Az Azure-t a Azure Stack fejlesztéséhez használni kívánt irányelvmodul segítségével | [Fejlesztés az Azure Stackhez](azure-stack-developer.md) | 
| Tekintse át és kövesse a sablonok ajánlott eljárásait. | [Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
| | |

## <a name="version-requirements"></a>A verzióra vonatkozó követelmények

A Azure Stack a Azure PowerShell és az Azure Service API-k adott verzióit támogatja. A támogatott verziók használatával biztosíthatja, hogy az alkalmazás a Azure Stack és az Azure-ba is telepíthető legyen.

Annak ellenőrzéséhez, hogy a Azure PowerShell megfelelő verzióját használja-e, használja az [API-verziók profiljait](azure-stack-version-profiles.md). A legújabb API-verzió profiljának meghatározásához tekintse meg az Ön által használt Azure Stack összeállítását. Ezt az információt a Azure Stack rendszergazdájától kérheti le.

> [!NOTE]
> Ha a Azure Stack Development Kit használja, és rendelkezik rendszergazdai hozzáféréssel, tekintse meg az [aktuális verzió meghatározása](../operator/azure-stack-updates.md#determine-the-current-version) szakaszt a Azure stack Build meghatározásához.

Más API-k esetén futtassa a következő PowerShell-parancsot a Azure Stack-előfizetésben támogatott névterek, erőforrástípusok és API-verziók kimenetéhez (a tulajdonságok szintjén továbbra is lehetnek különbségek). Ahhoz, hogy a parancs működjön, egy Azure Stack-környezethez már [telepítve](../operator/azure-stack-powershell-install.md) és [konfigurálva](azure-stack-powershell-configure-user.md) kell lennie a PowerShell-nek. Azure Stack ajánlathoz is előfizetéssel kell rendelkeznie.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Példa kimenetre (csonkolt): ![Példa a Get-AzureRmResourceProvider parancs kimenetére](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>További lépések

További információ a szolgáltatási szintbeli eltérésekről:

* [A Azure Stack-beli virtuális gépekkel kapcsolatos megfontolások](azure-stack-vm-considerations.md)
* [A Azure Stack való tárolás szempontjai](azure-stack-acs-differences.md)
* [Az Azure Stack-hálózatkezelés szempontjai](azure-stack-network-differences.md)
