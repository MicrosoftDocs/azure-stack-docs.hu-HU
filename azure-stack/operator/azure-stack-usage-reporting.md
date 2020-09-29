---
title: Azure Stack hub használati adatok jelentése az Azure-ba
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan jelentheti be Azure Stack hub használati adatait az Azure-ba.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 07/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8dff2f6debb12b8579e3b8e0b451b8d6bac17cad
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250350"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Azure Stack hub használati adatok jelentése az Azure-ba

A használati adatok, más néven felhasználási adatok, a felhasznált erőforrások mennyiségét jelölik.

A használaton alapuló számlázási modellt használó Azure Stack hub több csomópontos rendszereinek a használati adatokat az Azure-ba kell jelenteniük számlázási célokra. Azure Stack hub-operátoroknak konfigurálniuk kell a Azure Stack hub-példányt a használati adatok Azure-ba való jelentéséhez.

> [!IMPORTANT]
> Minden munkaterhelést a [bérlői előfizetések alatt kell telepíteni](#are-users-charged-for-the-infrastructure-vms) , hogy megfeleljenek Azure stack hub licencelési feltételeinek.

A használati adatok jelentéskészítése Azure Stack hub több csomópontos felhasználó számára szükséges, akik licencet igényelnek az utólagos használatú modellben. Ez nem kötelező azon ügyfelek számára, akik a kapacitási modell keretében licenccel rendelkeznek (lásd a [vásárlást](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) ismertető oldalt). Azure Stack Development Kit (ASDK) felhasználók esetében Azure Stack hub-operátorok jelentést készíthetnek a használati adatokról, és ellenőrizhetik a szolgáltatást. A felhasználók azonban nem számítanak fel díjat a felmerülő használati díjakért.

![Azure Stack hub használati adatok számlázási folyamata](media/azure-stack-usage-reporting/billing-flow.svg)

A használati adatokat a rendszer az Azure Stack hub-ból az Azure-ba küldi el a Azure Bridge keresztül. Az Azure-ban a kereskedelmi rendszerek feldolgozzák a használati adatokat, és létrehozzák a számlát. A számla létrehozása után az Azure-előfizetés tulajdonosa megtekintheti és letöltheti a [Azure Fiókközpont](https://account.windowsazure.com/subscriptions). Ha szeretne többet megtudni arról, hogy az Azure Stack hub milyen licenccel rendelkezik, tekintse meg a [Azure stack hub csomagolási és díjszabási](https://go.microsoft.com/fwlink/?LinkId=842847) dokumentumát.

## <a name="set-up-usage-data-reporting"></a>Használati adatok jelentésének beállítása

A használati adatok jelentéskészítésének beállításához [regisztrálnia kell az Azure stack hub-példányt az Azure](azure-stack-registration.md)-ban. A regisztrációs folyamat részeként a Azure Stack hub Azure Bridge összetevője konfigurálva van. A Azure Bridge összetevő az Azure Stack hub Azure-hoz való csatlakoztatását ismerteti. A rendszer a következő használati adatokat küldi el Azure Stack hub-ról az Azure-ba:

- **Mérési azonosító** – a felhasznált erőforrás egyedi azonosítója.
- **Mennyiség** – az erőforrás-használat mennyisége.
- **Hely** – a jelenlegi Azure stack hub-erőforrás üzembe helyezési helye.
- **Erőforrás URI-ja** – annak az erőforrásnak a teljes URI azonosítója, amelynek a használatát jelenteni kell.
- **Előfizetés-azonosító** – az Azure stack hub-felhasználó előfizetés-azonosítója, amely a helyi (Azure stack hub) előfizetés.
- A használati adatok kezdő és befejező **időpontja.** Az erőforrások Azure Stack hub-ban való felhasználása és a használati adatok kereskedelembe való bejelentésének ideje között némi késés áll fenn. Azure Stack hub minden 24 órában összesíti a használati adatokat, és az Azure-ban a kereskedelmi folyamat felé irányuló jelentési használati adatokat igénybe vesz néhány óra. Ezért az éjfél előtt hamarosan megjelenő használat a következő napon jelenhet meg az Azure-ban.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentéskészítésének előállítása

- A használati adatok jelentésének teszteléséhez hozzon létre néhány erőforrást Azure Stack hub-ban. Létrehozhat például egy [Storage-fiókot](azure-stack-provision-storage-account.md), a [Windows Server-alapú virtuális gépet](../user/azure-stack-create-vm-template.md), valamint egy alapszintű és standard SKU-val rendelkező Linux virtuális gépet, amelyből megtudhatja, hogyan történik az alapvető használat jelentése. A különböző típusú erőforrások használati adatait különböző mérőműszerek jelentik.

- Néhány órán keresztül elhagyhatja a futó erőforrásokat. A használati adatokat körülbelül óránként egyszer gyűjtjük. Az adatgyűjtés után ezeket az adatokat az Azure-ba továbbítjuk, és az Azure kereskedelmi rendszerébe dolgozzák fel. Ez a folyamat akár néhány órát is igénybe vehet.

## <a name="view-usage---csp-subscriptions"></a>Használat – CSP-előfizetések megtekintése

Ha a Azure Stack hub-t CSP-előfizetéssel regisztrálta, a használatot és a díjakat ugyanúgy tekintheti meg, ahogy az Azure-felhasználást is megtekinti. A Azure Stack hub használata a számlán és az egyeztetési fájlban is megtalálható, amely a [partneri központban](https://partnercenter.microsoft.com/partner/home)érhető el. Az egyeztetési fájl havonta frissül. Ha el kell érnie a közelmúltbeli Azure Stack hub használati adatait, használhatja a partner Center API-kat.

![Azure Stack hub számlázási és használati adatok megtekintése a Microsoft partner Centerben](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Használati Nagyvállalati Szerződés-előfizetések megtekintése

Ha Nagyvállalati Szerződés-előfizetéssel regisztrálta Azure Stack hub-t, a használat és a díjak megtekinthetők az [EA portálon](https://ea.azure.com/). Az Azure Stack hub használatát a speciális letöltések, valamint az Azure-használat részeként a portál jelentések szakasza tartalmazza.

## <a name="view-usage---other-subscriptions"></a>Használat megtekintése – egyéb előfizetések

Ha a Azure Stack hub-t bármely más előfizetés-típussal regisztrálta (például utólagos elszámolású előfizetés), a használat és a díjak megtekinthetők a Azure Fiókközpontban. Jelentkezzen be az [Azure Fiókközpontba](https://account.windowsazure.com/subscriptions) Azure-fiók rendszergazdájaként, és válassza ki az Azure-előfizetést, amelyet az Azure stack hub regisztrálásához használt. Az alábbi ábrán látható módon megtekintheti az Azure Stack hub használati adatait és az egyes felhasznált erőforrások összegét:

![A számlázási és a használati folyamat megtekintése Azure Fiókközpont](media/azure-stack-usage-reporting/pricing-details.png)

A ASDK esetében Azure Stack hub-erőforrásokért nem számítunk fel díjat, így a feltüntetett ár $0,00.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>Mely Azure Stack hub üzemelő példányait számítjuk fel?

Az erőforrás-használat ingyenes a ASDK számára. Azure Stack hub több csomópontos rendszer, a számítási feladatok virtuális gépei, a tárolási szolgáltatások és a App Services díja.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>A felhasználók díjat számítanak ki az infrastruktúra-alapú virtuális gépekért?

Nem. Egyes Azure Stack hub erőforrás-szolgáltatói virtuális gépek használati adatait a rendszer az Azure-ba küldi, de a virtuális gépekért és az üzembe helyezés során létrehozott virtuális gépekért nem számítunk fel díjat az Azure Stack hub-infrastruktúra engedélyezéséhez.  

A felhasználók csak a bérlői előfizetésekben futtatott virtuális gépekért számítanak fel díjat. Minden munkaterhelést a bérlői előfizetések alatt kell telepíteni, hogy megfeleljenek Azure Stack hub licencelési feltételeinek.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Van egy Windows Server-licencem, amelyet Azure Stack hub-on kívánok használni, hogyan csinálom?

A meglévő licencek használatával elkerülhető a használati fogyasztásmérők generálása. A meglévő Windows Server-licenceket Azure Stack központban lehet használni. Ezt a folyamatot a [Azure stack hub licencelési útmutatójának](https://go.microsoft.com/fwlink/?LinkId=851536)"meglévő szoftverek használata Azure stack hub használatával" című szakasza ismerteti. A meglévő licencek használatához a Windows Server-alapú virtuális gépeket a [hibrid juttatás Windows Server-licenchez](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)című cikkben leírtak szerint kell telepíteni.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Milyen előfizetést kell fizetni a felhasznált erőforrásokért?

Az [Azure stack hub Azure-beli regisztrálásakor](azure-stack-registration.md) megadott előfizetés díja.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetéseket támogat a használati adatok jelentése?

Azure Stack hub több csomópontos, Nagyvállalati Szerződés (EA) és CSP-előfizetések támogatása. A ASDK, a Nagyvállalati Szerződés (EA), az utólagos elszámolású, a CSP és az MSDN-előfizetések támogatják a használati adatok jelentéskészítését.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Működik a használati adatok jelentése a szuverén felhőkben?

A ASDK a használati adatok jelentéskészítéséhez a globális Azure-rendszeren létrehozott előfizetések szükségesek. A szuverén felhők (a Azure Government, az Azure Germany és az Azure China 21Vianet felhők) egyikében létrehozott előfizetések nem regisztrálhatók az Azure-ban, így nem támogatják a használati adatok jelentését.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>Miért nem a Azure Stack hub által jelentett használat egyezik a Azure Fiókközpont generált jelentéssel?

A Azure Stack hub használati API-k által jelentett használati adatok és a Azure Fiókközpontban jelentett használati adatok között mindig késés van. Ez a késleltetés az Azure Stack hub használati adatainak az Azure Commerce szolgáltatásba való feltöltéséhez szükséges idő. Emiatt a késés miatt az éjfél előtt röviddel megjelenő használat a következő napon jelenhet meg az Azure-ban. Ha az [Azure stack hub használati API-kat](azure-stack-provider-resource-api.md) használja, és összehasonlítja az eredményeket az Azure számlázási portálon jelentett használattal, a különbség látható.

## <a name="next-steps"></a>Következő lépések

- [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
- [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
- [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
- [A használat és a számlázás kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
