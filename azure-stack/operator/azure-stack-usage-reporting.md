---
title: Azure Stack hub használati adatok jelentése az Azure-ba
description: Megtudhatja, hogyan állíthatja be a használati adatok jelentéskészítését Azure Stack központban.
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 195491ebd67f1e7741a793278b316bab028c4471
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882669"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Azure Stack hub használati adatok jelentése az Azure-ba

A használati adatok, más néven felhasználási adatok, a felhasznált erőforrások mennyiségét jelölik.

A használaton alapuló számlázási modellt használó Azure Stack hub több csomópontos rendszereinek a használati adatokat az Azure-ba kell jelenteniük számlázási célokra. Azure Stack hub-operátoroknak konfigurálniuk kell a Azure Stack hub-példányt a használati adatok Azure-ba való jelentéséhez.

> [!IMPORTANT]
> Minden munkaterhelést a [bérlői előfizetések alatt kell telepíteni](#are-users-charged-for-the-infrastructure-vms) , hogy megfeleljenek Azure stack hub licencelési feltételeinek.

A használati adatok jelentéskészítése szükséges ahhoz, hogy az Azure Stack hub több csomópontot használó felhasználóinak, akik licencet használnak az utólagos használatú modellben. A kapacitási modellben licencet nem igénylő ügyfelek számára nem kötelező (lásd a [vásárlást](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) ismertető oldalt). Azure Stack Development Kit (ASDK) felhasználók esetében Azure Stack hub-operátorok jelentést készíthetnek a használati adatokról, és ellenőrizhetik a szolgáltatást. A felhasználók azonban nem számítanak fel díjat a felmerülő használatért.

![számlázási folyamat](media/azure-stack-usage-reporting/billing-flow.png)

A használati adatokat a rendszer az Azure Stack hub-ból az Azure-ba küldi el a Azure Bridge keresztül. Az Azure-ban a kereskedelmi rendszerek feldolgozzák a használati adatokat, és létrehozzák a számlát. A számla létrehozása után az Azure-előfizetés tulajdonosa megtekintheti és letöltheti a [Azure Fiókközpont](https://account.windowsazure.com/subscriptions). Ha szeretne többet megtudni arról, hogy az Azure Stack hub milyen licenccel rendelkezik, tekintse meg a [Azure stack hub csomagolási és díjszabási dokumentumát](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Használati adatok jelentésének beállítása

A használati adatok jelentéskészítésének beállításához [regisztrálnia kell az Azure stack hub-példányt az Azure](azure-stack-registration.md)-ban. A regisztrációs folyamat részeként az Azure Stack hub Azure Bridge összetevője, amely összeköti az Azure Stack hubot az Azure-hoz, és a használati adatokat elküldi, konfigurálva van. A rendszer a következő használati adatokat küldi el Azure Stack hub-ról az Azure-ba:

- **Mérési azonosító** – a felhasznált erőforrás egyedi azonosítója.
- **Mennyiség** – az erőforrás-használat mennyisége.
- **Hely** – a jelenlegi Azure stack hub-erőforrás üzembe helyezési helye.
- **Erőforrás URI-ja** – annak az erőforrásnak a teljes URI azonosítója, amelynek a használatát jelenteni kell.
- **Előfizetés-azonosító** – az Azure stack hub-felhasználó előfizetés-azonosítója, amely a helyi (Azure stack hub) előfizetés.
- A használati adatok kezdő és befejező **időpontja.** Az erőforrások Azure Stack hub-ban való felhasználása és a használati adatok kereskedelembe való bejelentésének ideje között némi késés áll fenn. Azure Stack hub minden 24 órában összesíti a használati adatokat, és az Azure-ban a kereskedelmi folyamat felé irányuló jelentési használati adatokat igénybe vesz néhány óra. Ezért az éjfél előtt rövid idő alatt megjelenő használat a következő napon jelenhet meg az Azure-ban.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentéskészítésének előállítása

- A használati adatok jelentésének teszteléséhez hozzon létre néhány erőforrást Azure Stack hub-ban. Létrehozhat például egy [Storage-fiókot](azure-stack-provision-storage-account.md), a [Windows Server-alapú virtuális gépet](../user/azure-stack-create-vm-template.md), valamint egy alapszintű és standard SKU-val rendelkező Linux virtuális gépet, amelyből megtudhatja, hogyan történik az alapvető használat jelentése. A különböző típusú erőforrások használati adatait különböző mérőműszerek jelentik.

- Néhány órán keresztül elhagyhatja a futó erőforrásokat. A használati adatokat körülbelül óránként egyszer gyűjtjük. Az adatgyűjtés után ezeket az adatokat az Azure-ba továbbítjuk, és az Azure kereskedelmi rendszerébe dolgozzák fel. Ez a folyamat akár néhány órát is igénybe vehet.

## <a name="view-usage---csp-subscriptions"></a>Használat – CSP-előfizetések megtekintése

Ha a Azure Stack hub CSP-előfizetéssel van regisztrálva, a használat és a díjak ugyanúgy megtekinthetők, ahogyan az Azure-felhasználást is megtekintheti. Azure Stack hub-használat a [partner centeren](https://partnercenter.microsoft.com/partner/home)keresztül elérhető a számlán és a megbékélési fájlban is. Az egyeztetési fájl havonta frissül. Ha el kell érnie a közelmúltbeli Azure Stack hub használati adatait, használhatja a partner Center API-kat.

![partneri központ](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Használati Nagyvállalati Szerződés-előfizetések megtekintése

Ha Nagyvállalati Szerződés-előfizetéssel regisztrálta Azure Stack hub-t, a használat és a díjak megtekinthetők az [EA portálon](https://ea.azure.com/). Az Azure Stack hub használatát a speciális letöltések, valamint az Azure-használat részeként a portál jelentések szakasza tartalmazza.

## <a name="view-usage---other-subscriptions"></a>Használat megtekintése – egyéb előfizetések

Ha más előfizetés-típussal regisztrálta a Azure Stack hubot; az utólagos elszámolású előfizetések esetében például megtekintheti a használatot és a díjakat a Azure Fiókközpontban. Jelentkezzen be az [Azure Fiókközpontba](https://account.windowsazure.com/subscriptions) Azure-fiók rendszergazdájaként, és válassza ki azt az Azure-előfizetést, amelyet az Azure stack hub regisztrálásához használt. Az alábbi ábrán látható módon megtekintheti az Azure Stack hub használati adatait, az egyes felhasznált erőforrások díjait:

![számlázási folyamat](media/azure-stack-usage-reporting/pricing-details.png)

A ASDK esetében a Azure Stack hub-erőforrások nem számítanak fel díjat, így a feltüntetett ár $0,00.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>Mely Azure Stack hub üzemelő példányait számítjuk fel?

Az erőforrás-használat ingyenes a ASDK számára. Azure Stack hub több csomópontos rendszer, a számítási feladatok virtuális gépei, a tárolási szolgáltatások és a App Services díjat számítunk fel.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>A felhasználók díjat számítanak ki az infrastruktúra-alapú virtuális gépekért?

Nem. Egyes Azure Stack hub erőforrás-szolgáltatói virtuális gépek használati adatait a rendszer az Azure-ba küldi, de a virtuális gépekért és az üzembe helyezés során létrehozott virtuális gépekért nem számítunk fel díjat az Azure Stack hub-infrastruktúra engedélyezéséhez.  

A felhasználók csak a bérlői előfizetésekben futtatott virtuális gépekért számítanak fel díjat. Minden munkaterhelést a bérlői előfizetések alatt kell telepíteni, hogy megfeleljenek Azure Stack hub licencelési feltételeinek.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Van egy Windows Server-licencem, amelyet Azure Stack hub-on kívánok használni, hogyan csinálom?

A meglévő licencek használatával elkerülhető a használati fogyasztásmérők generálása. A meglévő Windows Server-licencek a [Azure stack hub licencelési útmutatójának](https://go.microsoft.com/fwlink/?LinkId=851536)"meglévő szoftver használata Azure stack hub használatával" című szakaszában leírtak szerint használhatók Azure stack központban. A meglévő licencek használatához a Windows Server rendszerű virtuális gépeket a [Windows Server-licenchez készült Hybrid Benefit](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)című cikkben leírtak szerint kell telepíteni.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Milyen előfizetést kell fizetni a felhasznált erőforrásokért?

Az [Azure stack hub Azure-beli regisztrálásakor](azure-stack-registration.md) biztosított előfizetés díja.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetéseket támogat a használati adatok jelentése?

Azure Stack hub több csomópontos, Nagyvállalati Szerződés (EA) és CSP-előfizetések támogatása. A Azure Stack Development Kit, a Nagyvállalati Szerződés (EA), a pay-as-you-go, a CSP és az MSDN-előfizetések támogatják a használati adatok jelentéskészítését.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Működik a használati adatok jelentése a szuverén felhőkben?

A Azure Stack Development Kit a használati adatok jelentéskészítéséhez a globális Azure-rendszeren létrehozott előfizetésekre van szükség. A szuverén felhők (a Azure Government, az Azure Germany és az Azure China 21Vianet-felhők) egyikében létrehozott előfizetések nem regisztrálhatók az Azure-ban, így nem támogatják a használati adatok jelentéskészítését.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>Miért nem a Azure Stack hub által jelentett használat egyezik a Azure Fiókközpont generált jelentéssel?

A Azure Stack hub használati API-k által jelentett használati adatok és a Azure Fiókközpontban jelentett használati adatok között mindig késés történik. Ez a késleltetés az Azure Stack hub használati adatainak az Azure Commerce szolgáltatásba való feltöltéséhez szükséges idő. Ennek a késésnek köszönhetően az éjfél előtt röviddel megjelenő használat a következő napon jelenhet meg az Azure-ban. Ha az [Azure stack hub használati API-kat](azure-stack-provider-resource-api.md)használja, és összehasonlítja az eredményeket az Azure számlázási portálon jelentett használattal, a különbség látható.

## <a name="next-steps"></a>Következő lépések

- [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
- [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
- [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
- [A használat és a számlázás kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
