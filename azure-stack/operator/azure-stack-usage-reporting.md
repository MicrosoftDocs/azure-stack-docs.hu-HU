---
title: Jelentés Azure Stack használati adatok az Azure-ba | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a használati adatok jelentéseit a Azure Stackban.
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 5fecdbe7bcc85ea01e1502afcfc1c67d3b830c3e
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019391"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure Stack használati adatok jelentése az Azure-ban

A használati adatok, más néven felhasználási adatok, a felhasznált erőforrások mennyiségét jelölik.

Azure Stack a használaton alapuló számlázási modellt használó többcsomópontos rendszereknek számlázási célokra kell jelenteniük a használati adatokat az Azure-ban. Azure Stack operátoroknak konfigurálniuk kell a Azure Stack-példányt a használati adatok Azure-ba való jelentéséhez.

> [!IMPORTANT]
> Az összes munkaterhelést a [bérlői előfizetések alatt kell telepíteni](#are-users-charged-for-the-infrastructure-vms) , hogy megfeleljenek a Azure stack licencelési feltételeinek.

A használati adatok jelentéskészítése szükséges az Azure Stack több csomópontot használó felhasználók számára, akik licencet igényelnek az utólagos használatú modellben. A kapacitási modellben licencet nem igénylő ügyfelek számára nem kötelező (lásd a [vásárlást](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) ismertető oldalt). Azure Stack Development Kit (ASDK) felhasználók esetében Azure Stack operátorok jelentést készíthetnek a használati adatokról, és ellenőrizhetik a szolgáltatást. A felhasználók azonban nem számítanak fel díjat a felmerülő használatért.

![számlázási folyamat](media/azure-stack-usage-reporting/billing-flow.png)

A használati adatokat a rendszer a Azure Stack az Azure-ba küldi el a Azure Bridge keresztül. Az Azure-ban a kereskedelmi rendszerek feldolgozzák a használati adatokat, és létrehozzák a számlát. A számla létrehozása után az Azure-előfizetés tulajdonosa megtekintheti és letöltheti a [Azure Fiókközpont](https://account.windowsazure.com/subscriptions). A Azure Stack licencének megismeréséhez tekintse meg a [Azure stack csomagolási és díjszabási dokumentumát](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Használati adatok jelentésének beállítása

A használati adatok jelentéskészítésének beállításához [regisztrálnia kell az Azure stack-példányt az Azure](azure-stack-registration.md)-ban. A regisztrációs folyamat részeként a Azure Stack Azure Bridge összetevője, amely összeköti a Azure Stack az Azure-hoz, és elküldi a használati adatokat, konfigurálva van. A rendszer a következő használati adatokat küldi el a Azure Stackról az Azure-ba:

- **Mérési azonosító** – a felhasznált erőforrás egyedi azonosítója.
- **Mennyiség** – az erőforrás-használat mennyisége.
- **Hely** – a jelenlegi Azure stack erőforrás központi telepítésének helye.
- **Erőforrás URI-ja** – annak az erőforrásnak a teljes URI azonosítója, amelynek a használatát jelenteni kell.
- **Előfizetés-azonosító** – a Azure stack felhasználó előfizetés-azonosítója, amely a helyi (Azure stack) előfizetés.
- A használati adatok kezdő és befejező **időpontja.** A Azure Stack és a használati adatok kereskedelembe való bejelentésének ideje között némi késés áll fenn. A Azure Stack 24 óránként összesíti a használati adatokat, és az Azure-ban a kereskedelmi folyamatra vonatkozó jelentési használati adatokat igénybe vesz néhány óra. Ezért az éjfél előtt rövid idő alatt megjelenő használat a következő napon jelenhet meg az Azure-ban.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentéskészítésének előállítása

- A használati adatok jelentésének teszteléséhez hozzon létre néhány erőforrást a Azure Stackban. Létrehozhat például egy [Storage-fiókot](azure-stack-provision-storage-account.md), a [Windows Server-alapú virtuális gépet](../user/azure-stack-create-vm-template.md), valamint egy alapszintű és standard SKU-val rendelkező Linux virtuális gépet, amelyből megtudhatja, hogyan történik az alapvető használat jelentése. A különböző típusú erőforrások használati adatait különböző mérőműszerek jelentik.

- Néhány órán keresztül elhagyhatja a futó erőforrásokat. A használati adatokat körülbelül óránként egyszer gyűjtjük. Az adatgyűjtés után ezeket az adatokat az Azure-ba továbbítjuk, és az Azure kereskedelmi rendszerébe dolgozzák fel. Ez a folyamat akár néhány órát is igénybe vehet.

## <a name="view-usage---csp-subscriptions"></a>Használat – CSP-előfizetések megtekintése

Ha a Azure Stack CSP-előfizetéssel regisztrálta, a használatot és a díjakat ugyanúgy tekintheti meg, ahogyan az Azure-felhasználást is megtekintheti. A Azure Stack használat a [partner centeren](https://partnercenter.microsoft.com/partner/home)keresztül elérhető, a számlán és a megbékélési fájlban is szerepel. Az egyeztetési fájl havonta frissül. Ha el kell érnie a legutóbbi Azure Stack használati adatokat, használhatja a partner Center API-kat.

![partnerközpont](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Használati Nagyvállalati Szerződés-előfizetések megtekintése

Ha Nagyvállalati Szerződés-előfizetéssel regisztrálta a Azure Stack, a használat és a díjak megtekinthetők az [EA portálon](https://ea.azure.com/). Azure Stack használatot a speciális letöltések, valamint az Azure-használat című rész tartalmazza a portál jelentések szakaszában.

## <a name="view-usage---other-subscriptions"></a>Használat megtekintése – egyéb előfizetések

Ha a Azure Stack bármely más előfizetés-típussal regisztrálta, az utólagos elszámolású előfizetések esetében például megtekintheti a használatot és a díjakat a Azure Fiókközpontban. Jelentkezzen be az [Azure Fiókközpontba](https://account.windowsazure.com/subscriptions) Azure-fiók rendszergazdájaként, és válassza ki a Azure stack regisztrálásához használt Azure-előfizetést. Megtekintheti a Azure Stack használati adatait, az egyes felhasznált erőforrások összegét, az alábbi ábrán látható módon:

![számlázási folyamat](media/azure-stack-usage-reporting/pricing-details.png)

A ASDK esetében Azure Stack erőforrások nem számítanak fel díjat, így a feltüntetett ár $0,00.

## <a name="which-azure-stack-deployments-are-charged"></a>Mely Azure Stack üzemelő példányok díja?

Az erőforrás-használat ingyenes a ASDK számára. Azure Stack a többcsomópontos rendszerek, a számítási feladatok virtuális gépei, a tárolási szolgáltatások és a App Services díjait.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>A felhasználók díjat számítanak ki az infrastruktúra-alapú virtuális gépekért?

Nem. Egyes Azure Stack erőforrás-szolgáltatói virtuális gépek használati adatait a rendszer az Azure-ba küldi, de a virtuális gépekért nem számítunk fel díjat, sem pedig az üzembe helyezés során létrehozott virtuális gépeket, amelyek lehetővé teszik a Azure Stack infrastruktúra használatát.  

A felhasználók csak a bérlői előfizetésekben futtatott virtuális gépekért számítanak fel díjat. Az összes munkaterhelést a bérlői előfizetések alatt kell telepíteni, hogy megfeleljenek a Azure Stack licencelési feltételeinek.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Egy Windows Server-licenccel szeretnék használni a Azure Stackon, hogyan csinálom?

A meglévő licencek használatával elkerülhető a használati fogyasztásmérők generálása. A meglévő Windows Server-licencek Azure Stackban használhatók a [Azure stack licencelési útmutatójának](https://go.microsoft.com/fwlink/?LinkId=851536)"meglévő szoftver használata Azure stack" című részében leírtak szerint. A meglévő licencek használatához a Windows Server rendszerű virtuális gépeket a [Windows Server-licenchez készült Hybrid Benefit](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)című cikkben leírtak szerint kell telepíteni.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Milyen előfizetést kell fizetni a felhasznált erőforrásokért?

Az Azure-beli [Azure stack regisztrálásakor](azure-stack-registration.md) biztosított előfizetés díja.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetéseket támogat a használati adatok jelentése?

Azure Stack több csomópontos, Nagyvállalati Szerződés (EA) és CSP-előfizetések támogatottak. A Azure Stack Development Kit, a Nagyvállalati Szerződés (EA), a pay-as-you-go, a CSP és az MSDN-előfizetések támogatják a használati adatok jelentéskészítését.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Működik a használati adatok jelentése a szuverén felhőkben?

A Azure Stack Development Kit a használati adatok jelentéskészítéséhez a globális Azure-rendszeren létrehozott előfizetésekre van szükség. A szuverén felhők (a Azure Government, az Azure Germany és az Azure China 21Vianet-felhők) egyikében létrehozott előfizetések nem regisztrálhatók az Azure-ban, így nem támogatják a használati adatok jelentéskészítését.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Miért nem szerepel a Azure Stackban jelentett felhasználás a Azure Fiókközpont-ből generált jelentéssel?

Mindig van késés a Azure Stack használati API-k által jelentett használati adatok és a Azure Fiókközpontban jelentett használati adatok között. Ez a késleltetés az Azure Stack-ból az Azure-ba történő használati adatok feltöltéséhez szükséges idő. Ennek a késésnek köszönhetően az éjfél előtt röviddel megjelenő használat a következő napon jelenhet meg az Azure-ban. Ha a [Azure stack használati API-kat](azure-stack-provider-resource-api.md)használja, és összehasonlítja az eredményeket az Azure számlázási portálon jelentett használattal, a különbség látható.

## <a name="next-steps"></a>További lépések

- [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
- [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
- [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
- [A használat és a számlázás kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
