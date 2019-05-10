---
title: Az Azure-bA az Azure Stack-használati adatok jelentése |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Stackben jelentéskészítés a használati adatokról.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: c744a686be2a00418f48b769a5971997a603693f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172655"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Az Azure-bA az Azure Stack-használati adatok jelentése

Használati adatok, más néven a használati adatokat, a használt erőforrások mennyisége jelöli.

Használja a fogyasztás alapú számlázási modellt az Azure Stack több csomópontos rendszerek jelentse használati adatokat az Azure számlázási célból. Az Azure Stack-operátorok konfigurálnia kell az Azure Stack-példány jelentés használati adatokat az Azure-bA.

> [!IMPORTANT]
> Minden számítási [kell rendelni a bérlő előfizetéseknél](#are-users-charged-for-the-infrastructure-vms) az Azure Stack licencelési feltételeit.

Használati adatok jelentése szükség a több csomópontos Azure Stack-felhasználókat, akik a használatalapú mint-akkor-használható modellben licenc. Nem kötelező ügyfelek esetében, akik a kapacitás modellben licenc (lásd a [megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) lap). Az Azure Stack Development Kit a felhasználóknak az Azure Stack-operátorok használati adatokat, és tesztelheti a szolgáltatást. Azonban felhasználók nem kell fizetni keletkeznek használatot.

![számlázási folyamat](media/azure-stack-usage-reporting/billing-flow.png)

Használati adatok az Azure Stack az Azure-bA az Azure hídon keresztül zajlik. Az Azure-ban a kereskedelmi rendszer dolgozza fel a használati adatok, és állít elő, a számlán. Miután a számla jön létre, az Azure-előfizetés tulajdonosa megtekintheti és töltse le a [Azure Account Center](https://account.windowsazure.com/subscriptions). Hogyan licencelve van-e az Azure Stack kapcsolatos további információkért tekintse meg a [csomagolása és a díjszabás a dokumentum az Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Állítsa be a használati adatok jelentése

Használati adatok jelentése beállításához kell [regisztrálása az Azure Stack-példány az Azure-ral](azure-stack-registration.md). A regisztrációs folyamat részeként az Azure Stack, amely kapcsolódik az Azure Stack az Azure-ba, és a használati adatokat küld, Azure híd összetevője van konfigurálva. A következő használati adatok Azure Stackben is küld az Azure-bA:

- **Mérőszám azonosítója** – az a felhasznált erőforrás egyedi azonosítója.
- **Mennyiség** – erőforrás-használat mennyisége.
- **Hely** -helyet, ahol a jelenlegi Azure Stack-erőforrás üzembe van helyezve.
- **Erőforrás-URI** – teljes URI-t az erőforrás, amelynek használati jelentett.
- **Előfizetés-azonosító** -előfizetés az Azure Stack felhasználónak, amely a helyi (az Azure Stack) előfizetés-azonosítója.
- **Idő** -kezdési és befejezési idő, a használati adatok. Bizonyos időbeli késedelem van az időpont közötti amikor ezeket az erőforrásokat az Azure Stackben felhasználják, és a használati adatok kereskedelmi jelentésekor. Az Azure Stack a 24 óránként összesíti az használati adatait, és a kereskedelmi folyamat az Azure-beli használati adatokról szóló jelentéseket egy másik néhány óráig tart. Ezért hamarosan éjfél előtt következik be használati is megjelenhetnek az Azure-ban a következő napon.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentések készítése

- Jelentéskészítés a használati adatokról teszteléséhez hozzon létre néhány erőforrásokat az Azure Stackben. Létrehozhat például egy [tárfiók](azure-stack-provision-storage-account.md), [Windows Server rendszerű virtuális gép](../user/azure-stack-create-vm-template.md), és a egy Linuxos virtuális Gépre, az alapszintű és Standard alapvető használati jelentett hogyan megtekintéséhez. A használati adatok a különböző típusú erőforrások különböző mérőszámok alapján jelenti.

- Az erőforrások néhány órán keresztül fut, hagyja. Körülbelül óránként gyűjt használati adatokat. Ezeket az adatokat, begyűjtését követően az Azure-bA továbbított adatok köre és dolgozzák fel az Azure kereskedelmi rendszerben. Ez a folyamat több óráig is eltarthat.

## <a name="view-usage---csp-subscriptions"></a>Használati adatok megtekintése – CSP-előfizetésekben

Ha regisztrált az Azure Stack használatával a CSP-előfizetésekben, ahol megtekintheti az Azure-használati ugyanúgy a használat és költségek megtekintheti. Azure Stack-használat szerepel a számlán, és az egyeztetési fájlban, keresztül elérhető a [Partnerközpont](https://partnercenter.microsoft.com/partner/home). Az egyeztetési fájlt a havonta frissül. Ha a legutóbbi Azure Stack-használati adatok elérésére van szüksége, használhatja a Partner Center API-k.

![partnerközpont](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Használati adatok megtekintése – nagyvállalati szerződéses előfizetések

Ha regisztrált az Azure Stack használatával a nagyvállalati szerződéses előfizetésébe, megtekintheti a használattal és a díjak a [a nagyvállalati szerződések portáljának](https://ea.azure.com/). Azure Stack-használat a speciális letöltések együtt az Azure-használatát ezen a portálon a jelentések szakaszban szerepel.

## <a name="view-usage---other-subscriptions"></a>Használati adatok megtekintése – más előfizetések

Ha regisztrált az Azure Stack segítségével bármely más előfizetésre írja; Ha például egy használatalapú fizetéses előfizetésre, megtekintheti az Azure Account Center használat és költségek. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/subscriptions) az Azure-rendszergazdai fiók, és válassza ki az Azure-előfizetést, amellyel az Azure Stack regisztrálni. Az Azure Stack használati adatok, az összeg, az egyes, a használt erőforrások díjat számítunk fel, a következő képen látható módon tekintheti meg:

![számlázási folyamat](media/azure-stack-usage-reporting/pricing-details.png)

Az Azure Stack Development Kit for Azure Stack-erőforrások nem számítunk fel, így a díj látható 0,00 USD.

## <a name="which-azure-stack-deployments-are-charged"></a>Melyik Azure Stack üzemelő példányok díja?

Erőforrás-használatot pedig ingyenesen használható az Azure Stack Development Kit. Az Azure Stack több csomópontos rendszerekhez, virtuális gépek számítási, tárolási szolgáltatások és App Services, számítjuk fel.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Felhasználók a virtuális gépek infrastruktúra díjkötelesek?

Nem. Használati adatok az egyes Azure Stack erőforrás-szolgáltató virtuális gépeket az Azure-bA jelez, de nem számítunk fel díjat ezeknek a virtuális gépeknek, sem a virtuális gépek üzembe helyezésének részeként létrehozott engedélyezése az Azure Stack-infrastruktúra.  

Felhasználók csak a bérlő az előfizetések alatt futó virtuális gépek fizetnie. Minden számítási feladatok Azure Stack licencelési feltételeit ahhoz, hogy a bérlő előfizetéseknél kell telepíteni.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Az Azure Stacken használni kívánt Windows Server-licence van, hogyan kell ehhez tennem?

A meglévő licencek használatával elkerülhető a használati mérőszámok létrehozása. Windows Server-licenceit az Azure Stackben, használható, a "Meglévő szoftverek használata az Azure Stack használatával" szakaszban leírtak szerint a [Azure Stack-licencelési útmutató](https://go.microsoft.com/fwlink/?LinkId=851536). Annak érdekében, hogy a meglévő licenceit használja, üzembe kell helyezni a Windows Server virtuális gépeik leírtak szerint [Hybrid Benefit értékelem a Windows Server-licenc](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Melyik előfizetést a felhasznált erőforrásokért fizet?

Az előfizetés, mikor biztosított [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md) díjat számítunk fel.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetések használati adatok jelentése támogatottak?

Az Azure Stack több csomópontos a nagyvállalati szerződés (EA) és a CSP-előfizetések használata támogatott. Az Azure Stack Development Kit, nagyvállalati szerződés (EA), használatalapú fizetés, a CSP és az MSDN-előfizetések támogatása használati adatok jelentése.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>A független felhőkben munkahelyi jelentéskészítés a használati adatokról nem?

Az Azure Stack Development Kit szükséges használati adatok jelentése a globális Azure rendszerben létrehozott előfizetések. A független felhőkben (Azure Government, Azure Germany és Azure China felhők) egyikében létrehozott előfizetések nem lehet regisztrálni az Azure-ban, így nem támogatják a használati adatok jelentése.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Miért nem egyezik meg a használati jelentés készül az Azure Stack az Azure Account Center által létrehozott jelentést?

Az Azure Stack-használati API-k által jelentett használati adatok és a használati adatok az Azure Account Center webhelyen jelentett késleltetés mindig van. Ez a késleltetés a használati adatokat az Azure Stack az Azure kereskedelmi feltöltéséhez szükséges idő. Miatt ez a késleltetés hamarosan éjfél előtt következik be használati jelenhet meg az Azure-ban a következő napon. Ha használja a [Azure Stack-használati API-k](azure-stack-provider-resource-api.md), és hasonlítsa össze az eredményeket a használati jelentés készül az Azure számlázási portálján, akkor megtekintheti, különbség.

## <a name="next-steps"></a>További lépések

- [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
- [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
- [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
- [Kezelése a használati és számlázási információkkal Felhőszolgáltató](azure-stack-add-manage-billing-as-a-csp.md)
