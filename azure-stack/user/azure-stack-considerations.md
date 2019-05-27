---
title: Az Azure Stacket és használatakor, szolgáltatások és alkalmazások létrehozása az Azure közötti különbségeket |} A Microsoft Docs
description: Az Azure és az Azure Stack közötti különbségek megértéséhez használatakor, szolgáltatások és alkalmazások létrehozása.
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
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 9fcf27c8ebbde86e775b54eda593b25fcd03979c
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197292"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Az Azure Stacket és használatakor, szolgáltatások és alkalmazások létrehozása az Azure közötti különbségek

Mielőtt szolgáltatások használatára, vagy alkalmazásokat hozhat létre az Azure Stack, fontos, az Azure Stack és az Azure közötti különbségeket. Ez a cikk azonosítja a különböző funkciókat és a legfontosabb szempont a hibrid felhőalapú fejlesztési környezet az Azure Stack használata esetén.

## <a name="overview"></a>Áttekintés

Az Azure Stack egy hibrid felhőplatform, amely lehetővé teszi, hogy az Azure services vállalattal vagy szolgáltatással szolgáltató adatközpontjának. Alkalmazás készítése az Azure Stacken, és ezután telepítheti az Azure Stack, az Azure-bA vagy az Azure hibrid felhőalapú.

Az Azure Stack-operátorokról lehetővé teszi, hogy mely szolgáltatások érhetők el, amelyet használhat, és hogyan kérhet támogatást. Ezek a szolgáltatások saját testre szabott csomagokat és ajánlatokat keresztül kínálnak.

Az Azure műszaki tartalom azt feltételezi, hogy alkalmazásokat fejlesztenek ki egy Azure-szolgáltatás, és nem az Azure Stack. Amikor hozhat létre, és az alkalmazások üzembe helyezése az Azure Stack, néhány fontos eltérés, például kell ismernie:

* Az Azure Stack tesz lehetővé a szolgáltatások és az Azure-ban elérhető funkciók egy részét.
* A vállalat vagy a service provider kiválaszthatja, hogy mely szolgáltatásokat, azok kíván kíván felajánlani. A rendelkezésre álló beállítások testre szabott szolgáltatások vagy alkalmazások lehetnek. Előfordulhat, hogy a saját testre szabott dokumentáció kínálnak.
* A megfelelő kell használni (például a portál címet és az Azure Resource Manager-végpont URL-címek) az Azure Stack-specifikus végpontok.
* PowerShell és API-verziók az Azure Stack által támogatott kell használnia. Támogatott verziók használata biztosítja, hogy működik-e az alkalmazások az Azure Stack és az Azure.

## <a name="cheat-sheet-high-level-differences"></a>Hasznos tanácsok: Magas szintű különbségek

A következő táblázat ismerteti az Azure Stack és az Azure magas szintű különbségeit. Fejlesztés az Azure Stackhez vagy az Azure Stack-szolgáltatások használatára, tartsa szem előtt a különbségeket.

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

| Terület | Azure (globális) | Azure Stack |
| -------- | ------------- | ----------|
| Akik működik ez? | Microsoft | A szervezet vagy a service provider.|
| Akik tegye meg a kapcsolatot? | Microsoft | Egy integrált rendszer lépjen kapcsolatba az Azure Stack-operátorokról (a saját szervezet vagy szolgáltatás szolgáltató) támogatást.<br><br>Azure Stack Development Kit támogatásért látogasson el a [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Mivel a csomag egy kiértékelési környezete, rendszer nem érhető el – a Microsoft támogatja a szolgáltatások (CSS) hivatalos támogatja.
| Elérhető szolgáltatások | Listájának megtekintéséhez [Azure-termékek](https://azure.microsoft.com/services/?b=17.04b). Az Azure-régióban elérhető szolgáltatások változhat. | Az Azure Stack egy Azure-szolgáltatások részét támogatja. Tényleges szolgáltatások terméktípustól függ, hogy a szervezet vagy a service provider választ kínálnak.
| Az Azure Resource Manager endpoint * | https://management.azure.com | Az Azure Stackkel integrált rendszer használja a végpontot, amely az Azure Stack-operátorokról biztosít.<br><br>A fejlesztői készlet használata: https://management.local.azurestack.external.
| Portál URL-címe * | [https://portal.azure.com](https://portal.azure.com) | Az Azure Stackkel integrált rendszer az Azure Stack-operátorokról biztosító URL-címet használja.<br><br>A fejlesztői készlet használata: https://portal.local.azurestack.external.
| Régió | Kiválaszthatja, hogy melyik régiót szeretné telepíteni. | Az Azure Stackkel integrált rendszer használja a régiót, amelyben a rendszeren érhető el.<br><br>A csomag, a régiót mindig lesz **helyi**.
| Erőforráscsoportok | Egy erőforráscsoport régióban is kiterjedhetnek. | Integrált rendszerek és a csomag nincs csak egy régiót.
|Támogatott névtereket, erőforrástípusok és API-verziók | A legújabb (vagy korábbi verziók, amelyek még nincsenek elavult). | Az Azure Stack támogatja a verziókkal. Tekintse meg a [verziókövetelményekért](#version-requirements) című szakaszát.
| | |

Ha a Ön az Azure Stack operátorait, lásd: [a felügyeleti portál használatával](../operator/azure-stack-manage-portals.md) és [Adminisztráció alapjai](../operator/azure-stack-manage-basics.md) további információt.

## <a name="helpful-tools-and-best-practices"></a>Hasznos eszközök és ajánlott eljárások

A Microsoft biztosít eszközöket és útmutatást, amelyek segítségével fejlesztése az Azure Stackhez.

| Ajánlás | Hivatkozások |
| -------- | ------------- |
| Telepítse a megfelelő eszközöket a fejlesztői munkaállomásán. | - [A PowerShell telepítése](../operator/azure-stack-powershell-install.md)<br>- [Eszközök letöltése](../operator/azure-stack-powershell-download.md)<br>- [A PowerShell konfigurálása](azure-stack-powershell-configure-user.md)<br>- [A Visual Studio telepítése](azure-stack-install-visual-studio.md) 
| Tekintse át a következő elemek:<br>– Az azure Resource Manager-sablon kapcsolatos szempontok<br>-Hogyan gyorsindítási sablonok keresése<br>-Házirendmodul használata segít az Azure-fejlesztés az Azure Stackhez | [Fejlesztés az Azure Stackhez](azure-stack-developer.md) | 
| Tekintse át és kövesse az ajánlott eljárások a sablonokhoz. | [Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Verzióra vonatkozó követelmények

Az Azure Stack az Azure PowerShell és az Azure-szolgáltatás API-k adott verzióit támogatja. Támogatott verziók használatával győződjön meg arról, hogy az alkalmazás telepíthető központilag a két Azure stack, és az Azure-bA.

Győződjön meg arról, hogy az Azure PowerShell megfelelő verzióját használja, használja a [API-verzióprofilok](azure-stack-version-profiles.md). Annak megállapításához, a legújabb API verzió-profilt, amelyet használhat, megtudhatja a build, az Azure Stack használata esetén. Ezt az információt kaphat az Azure Stack rendszergazdájától.

> [!NOTE]
> Ha az Azure Stack fejlesztői készletet használ, és rendelkezik rendszergazdai hozzáféréssel, tekintse meg a [határozza meg a jelenlegi verzió](../operator/azure-stack-updates.md#determine-the-current-version) szakaszban határozza meg az Azure Stack hozhat létre.

Más API-k futtassa a következő PowerShell-parancsot a kimenetben a névterek, erőforrástípusok és az Azure Stack-előfizetés által támogatott API-verziók (továbbra is lehetnek különbségek tulajdonság szinten). Ez a parancs működéséhez rendelkeznie kell már [telepített](../operator/azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-user.md) PowerShell az Azure Stack-környezet. Ajánlat az Azure Stack-előfizetést is rendelkeznie kell.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Példa a kimenetre (csonkolt): ![Például a Get-AzureRmResourceProvider parancs kimenetéből](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>További lépések

Szolgáltatási szinten különbségek kapcsolatos részletesebb információkért lásd:

* [Szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md)
* [Az Azure Stackben tárolási szempontjai](azure-stack-acs-differences.md)
* [Az Azure Stack-hálózatkezelés szempontjai](azure-stack-network-differences.md)
