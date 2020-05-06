---
title: Azure App Service Azure Stack hub számlázási áttekintése és gyakori kérdések
description: Tudnivalók a Azure Stack hub Azure App Service számlázásáról.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2020
ms.openlocfilehash: 3717308e28b7dfa62534ee1abd4e71ff06361d50
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847945"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Azure App Service Azure Stack hub számlázási áttekintése és gyakori kérdések

Ez a cikk azt mutatja be, hogyan történik a Felhőbeli operátorok kiszámlázása az Azure Stack hub-beli Azure App Service, valamint arról, hogy a szolgáltatás használatáért hogyan számlázzák fel a bérlőket.

## <a name="billing-overview"></a>Számlázási áttekintés

Azure Stack hub Cloud operátorok úgy döntenek, hogy az Azure Stack hub-on lévő Azure App Service üzembe helyezését Azure Stack hub-bélyegzőre helyezik, hogy Azure App Service és Azure Functions bérlői képességeit is felkínálják ügyfeleiknek. A Azure App Service erőforrás-szolgáltató több típusú szerepkörből áll, amelyek az infrastruktúra és a munkavégző rétegek között oszthatók fel.

Az infrastruktúra-szerepkörök számlázása nem történik meg, mert a szolgáltatás alapvető működéséhez szükségesek. Az infrastruktúra szerepkörei igény szerint méretezhetők a Felhőbeli operátor bérlői igényeinek kielégítése érdekében. Az infrastruktúra-szerepkörök a következők:

- Vezérlők
- Felügyeleti szerepkörök
- Kiadók
- Elülső végek

A feldolgozói rétegek két fő típusból állnak: megosztott és dedikált. A feldolgozó általi használat számlázása a Felhőbeli kezelő alapértelmezett szolgáltatói előfizetéséhez a következő feltételek szerint történik.

## <a name="shared-workers"></a>Megosztott feldolgozók

A közös feldolgozók több-bérlős, valamint ingyenes és közös App Service csomagok és fogyasztáson alapuló Azure-függvények számos bérlő számára. A megosztott munkavégzők a használati mérőszámokat a Azure App Service erőforrás-szolgáltatón készként jelölik ki.

## <a name="dedicated-workers"></a>Dedikált feldolgozók

A dedikált feldolgozók a bérlők által létrehozott App Service-csomagokhoz kötődnek. Az S1 SKU-ban például a bérlők alapértelmezés szerint 10 példányra méretezhetők. Ha egy bérlő S1 App Service csomagot hoz létre, Azure App Service a kisméretű feldolgozó rétegben lévő példányok egyikét rendeli hozzá a bérlő App Service tervéhez. A hozzárendelt feldolgozót ezután már nem lehet más bérlőhöz rendelni. Ha a bérlő úgy dönt, hogy 10 példányra méretezi a App Service tervet, a rendszer kilenc feldolgozót távolít el a rendelkezésre álló készletből, és hozzárendeli a bérlő App Service tervéhez.

A mérőórák a dedikált feldolgozók számára vannak kibocsátva, amikor:

- Készként van megjelölve a Azure App Service erőforrás-szolgáltatóban.
- App Service-csomaghoz rendelve.

Ez a számlázási modell lehetővé teszi, hogy a felhő-kezelők olyan dedikált feldolgozók készletét használják fel, akik a munkavégzők számára készen állnak arra, hogy a feldolgozók fizetése nélkül is használhassák a bérlői App Service

Tegyük fel például, hogy 20 feldolgozója van a kisméretű munkavégző szinten. Ezután, ha öt olyan ügyfele van, amely két S1 App Service tervet hoz létre, és mindegyik méretezéssel a App Service két példányra, akkor nem állnak rendelkezésre munkavégzők. Ennek eredményeképpen az ügyfelek vagy az új ügyfelek nem képesek felskálázásra vagy új App Servicei csomagok létrehozására.

A Felhőbeli operátorok megtekinthetik a rendelkezésre álló munkavégző rétegek aktuális számát az Azure Stack hub felügyelet Azure App Service konfigurációjának munkavégző szintjeinek megtekintésével.

![App Service – feldolgozói rétegek képernyő][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>Az Azure Stack hub használati szolgáltatásának használatával kapcsolatban lásd az ügyfelek használatát

A Felhőbeli operátorok lekérhetik az [Azure stack hub-bérlő erőforrás-használati API](azure-stack-tenant-resource-usage-api.md) -ját az ügyfelek használati adatainak lekérésére. Megtalálhatja az összes olyan mérőszámot, amelyet a App Service bocsát ki a bérlők használatának leírására a [használati gyakori kérdések](azure-stack-usage-related-faq.md)között. Ezeket a mérőszámokat ezután a használati díjak kiszámítására használják a költségek kiszámításához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Hogyan licenc az előfeltételekhez szükséges SQL Server és fájlkiszolgáló infrastruktúra?

A Azure App Service erőforrás-szolgáltató által megkövetelt SQL Server-és fájlkiszolgáló-infrastruktúra licencelése itt található: az [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>A használattal kapcsolatos gyakori kérdések felsorolják a bérlői mérőszámokat, de nem a mérőórák árait. Hol találhatom meg őket?

Felhőbeli operátorként ingyenesen alkalmazhatja saját díjszabási modelljét az ügyfeleknek. A használati szolgáltatás biztosítja a használat mérését. Ezt követően az Ön által meghatározott díjszabási modell alapján felszámíthatja az ügyfelek felszámolására szolgáló mérési mennyiséget. A díjszabás beállítása lehetővé teszi a kezelők számára a más Azure Stack hub-operátorok megkülönböztetését.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Hogyan biztosíthatok ingyenes és megosztott SKU-ket az ügyfeleknek, hogy kipróbálják a szolgáltatást?

A Felhőbeli üzemeltetők az ingyenes és a közös SKU-k nyújtásával járó költségekkel járnak, mert megosztott feldolgozók üzemeltetik őket. Ennek a lehetőségnek a minimálisra csökkentése érdekében dönthet úgy, hogy a megosztott feldolgozói szintet lekicsinyíti minimálisra.

> [!IMPORTANT] 
> A közös feldolgozók telepítési alapértelmezett értékei a Azure Stack hub 2020. Q2-ben Azure App Service módosultak az új telepítések esetén.  Alapértelmezés szerint a megosztott munkavégzők a A4_v2 számítási SKU használatával vannak kiépítve, amelyet a kezelő a telepítéskor vagy a telepítés után módosíthat.

Ha például az ingyenes és a közös App Service csomagokra van szüksége, és a felhasználáson alapuló függvények is rendelkezésre állnak, akkor legalább egy a1-es példányra van szükség. A megosztott munkavégzők több-bérlős gépek, így több, egyenként elkülönített és a App Service sandbox által védett ügyfél-alkalmazást tárolhatnak. A közös feldolgozói réteg ily módon történő skálázásával havi egy vCPU költségét korlátozhatja.

Ezután dönthet úgy, hogy létrehoz egy kvótát a csomagban való használatra, amely csak az ingyenes és a közös SKU-t kínál, és korlátozza az ügyfél által létrehozható ingyenes és közös App Service csomagok számát.

## <a name="sample-scripts-to-assist-with-billing"></a>Példák a számlázást segítő parancsfájlokra

A Azure App Service csapat létrehozott egy PowerShell-parancsfájlokat, amelyek segítséget nyújtanak a Azure Stack hub használati szolgáltatás lekérdezéséhez. A Felhőbeli operátorok ezeket a szkripteket használhatják a saját bérlőik saját számlázási felkészítésére. A minta szkriptek a GitHub [Azure stack hub Tools adattárában](https://github.com/Azure/AzureStack-tools) találhatók. A App Service szkriptek a [AppService mappában találhatók a használat alatt](https://aka.ms/aa6zku8).

A rendelkezésre álló minta szkriptek a következők:

- [Get-AppServiceBillingRecords](https://aka.ms/aa6zku2): Ez a minta a Azure stack hub használati API-ból Azure stack hub számlázási rekordok Azure app Service beolvasása.
- [Get-AppServiceSubscriptionUsage](https://aka.ms/aa6zku6): Ez a minta kiszámítja Azure app Service Azure stack hub használati összegének előfizetését. Ez a parancsfájl a használati API adatai alapján számítja ki a használati összegeket, valamint a Cloud operátor által mért mérőszámok árait.
- [Felfüggesztés – UserSubscriptions](https://aka.ms/aa6zku7): Ez a minta felfüggeszti vagy engedélyezi az előfizetéseket a Felhőbeli operátor által megadott használati korlátok alapján.

## <a name="next-steps"></a>További lépések

- [Azure Stack hub-bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
