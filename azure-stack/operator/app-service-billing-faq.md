---
title: Az Azure App Service-ben az Azure Stack számlázás áttekintése és gyakori kérdések |} A Microsoft Docs
description: Adatokat az Azure App Service az Azure Stacken díjköteles és kell fizetnie.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: fa3bc647d11bca915c58aa1bd948881628405776
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828376"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Az Azure App Service-ben az Azure Stack számlázási áttekintése és gyakori kérdések

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ebben a dokumentumban található útmutatást mutatja be, hogyan történik a felhő üzemeltetői az ajánlat az Azure App Service az Azure Stack számlázása, és hogyan azok is viszont a Díjszámítás a szolgáltatás használatának számlázása a bérlőik számára.

## <a name="billing-overview"></a>Számlázás áttekintése

Az Azure Stack-felhő üzemeltetői váltani, üzembe helyezése az Azure App Service az Azure Stack az Azure Stack-blokk alakzatot annak érdekében, hogy az Azure App Service és az Azure Functions bérlői képességeit ügyfeleiknek.  Az Azure App Service erőforrás-szolgáltató infrastruktúra és a feldolgozói rétegek közötti oszthatók szerepkörök többféle típusú áll.

Mivel ezek a szolgáltatás alapvető működéséhez szükséges infrastruktúra-szerepkörök nem számolunk fel.  Infrastruktúra-szerepkörök kiterjeszthető, a felhő üzemeltetője bérlők igényeinek támogatásához szükséges.  Az infrastruktúra-szerepkörök a következők:

- Vezérlők
- Felügyeleti szerepkörök
- Kiadók
- A kezelőfelületek

Feldolgozói rétegek állnak, amelyek két fő típusba sorolhatók: közös és a dedikált. Feldolgozó, a felhő üzemeltetője a következő feltételek szerint lesz számlázva.

## <a name="shared-workers"></a>Megosztott feldolgozó

Megosztott feldolgozó olyan több-bérlős és a gazdagép ingyenes és közös App Service-csomagok és az Azure Functions fogyasztásalapú a több bérlő számára. Megosztott feldolgozó gridre bocsáthatja ki, amikor készként jelöli az Azure App Service erőforrás-szolgáltató a használati mérőszámok.

## <a name="dedicated-workers"></a>Dedikált feldolgozó

Dedikált feldolgozó bérlő létrehozása, amely az App Service-csomagok vannak társítva. Ha például az S1 termékváltozat alapértelmezés szerint bérlők méretezheti 10 példányra alapértelmezés szerint. Ezért ha egy bérlőt egy S1 szintű App Service-csomagot hoz létre, az Azure App Service foglal le egy adott bérlő App Service-csomag a kis méretű feldolgozói réteg méretezési példányt. Ezt követően a hozzárendelt feldolgozó már nem rendelhető hozzá semmilyen más bérlők számára elérhető.  Ha a bérlő méretezése App Service-csomag 10 példányok, egy további 9 feldolgozók törlődnek a rendelkezésre álló készlet, és a bérlő App Service-csomag hozzá vannak rendelve.

Mérőszámok vannak kibocsátva dedikált az adminisztratív munkatársak számára, amelyek:

- Készként jelöli az Azure App Service erőforrás-szolgáltatón.
- App Service-csomag rendelve.

Ezért a számlázási modell lehetővé teszi a felhő üzemeltetői az készen áll az ügyfelek számára anélkül, hogy a dolgozók kellene fizetnie, amíg hatékonyan lefoglalta a bérlőjének app service-csomag dedikált feldolgozók készletét hozza létre. Például ha 20 feldolgozók van a kis méretű feldolgozói réteg majd hozzon létre két S1 szintű App Service-csomagot minden 5 ügyfele van, és ezek mindegyike méretezése App Service-csomag legfeljebb két példányt, majd kell nem feldolgozó is rendelkezésre áll. Ennek eredményeképpen is lesz bármely ügyfél vagy az új ügyfeleket horizontális felskálázás, vagy hozzon létre új App Service-csomagok nem kapacitását. Felhő üzemeltetői / feldolgozói réteg rendelkezésre álló feldolgozók jelenlegi száma megtekintéséhez az Azure App Service a konfigurációban az Azure Stack felügyeleti a feldolgozói rétegek Hibaoldal.

![App Service-ben feldolgozói rétegek][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>Tekintse meg az ügyfelek általi használatot az Azure Stack használatának szolgáltatással

Lekérdezheti a felhő üzemeltetői az [Azure Stack-bérlői használati API](azure-stack-tenant-resource-usage-api.md) lekérdezni a használati adatokat az ügyfeleknek, és megkeresheti az egyes mérőszámok adott App Service-ben a bocsát ki írja le a bérlők a [használat – gyakori kérdések ](azure-stack-usage-related-faq.md). Ezeket a mérőszámok majd, amelyek ezután felhasználhatók költségek kiszámításához ügyfél-előfizetéshez tartozó használatot kiszámításához használható.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>Hogyan lehet licencet az SQL Server és a kiszolgálói infrastruktúra szükséges az Előfeltételek

Az SQL és a fájl szükség kiszolgálói infrastruktúrára, az Azure App Service erőforrás-szolgáltató által foglalkozik az Azure App Service az Azure Stack - licencelés [használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) cikk.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>A használat – gyakori kérdések listája a bérlő mérőszámok, de nem adott mérőszámok, ahol megtalálja a díjszabása

Felhő üzemeltetői használata ingyenes, saját díjszabási modell a végfelhasználók számára a alkalmazni. A használatmérő szolgáltatást nyújt a használatmérés, a felhő üzemeltetője kell majd a mérőszám mennyiség alapján határozzák meg a díjszabási modell ügyfelek díja szerint számítjuk fel. Ez a képesség lehetővé teszi, hogy operátorok díjszabás beállításához és az egyéb Azure Stack-operátorok megkülönböztetése érdekében kellene.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-trial-the-service"></a>Egy CSP-hez, hogyan tudom kínálnak ingyenes és közös termékváltozatok az ügyfelek számára a próbaverzió a szolgáltatást

Felhő-felelősként, költségekkel fog járni az ajánlat ingyenes és közös SKU-k, ahogyan az Azure-ban, a megosztott feldolgozók üzemeltetik azokat.  Követelnek egyéb fontos minimalizálása érdekében dönthet úgy, hogy legalább egy, a megosztott feldolgozói réteg leskálázása.  Például az ingyenes és közös App Service csomag termékváltozat és fogyasztásalapú funkciókat kínáló, kell legalább 1 a1-es példány érhető el.  Megosztott feldolgozó több-bérlősek, ezért több ügyfél alkalmazás üzemeltethető, minden egyes külön-külön elkülönített és védi az App Service tesztkörnyezetben.  Ezzel a módszerrel a megosztott feldolgozói réteg felskálázásával korlátozhatja 1vCPU havi költségét, a költségek.

Emellett kiválaszthatja kvóta, egy terv, amely csak az ingyenes és közös termékváltozatok kínál, és korlátozza az ingyenes és közös app service-csomagot az ügyfél hozhat létre a használatra.

## <a name="sample-scripts-to-assist-with-billing"></a>Mintaszkriptek, amelyek segítik a számlázás

Az Azure App Service csapata létrehozott PowerShell-példaszkriptekre lekérdezése az Azure Stack használatmérő szolgáltatást nyújt, amelyek segítik a felhő üzemeltetői saját számlázás a bérlők előkészítése.  Tekintheti meg a minta parancsprogramok a [Azure Stack eszközök tárház](https://github.com/Azure/AzureStack-tools) a GitHub és az App Service-ben parancsfájlok szerepelnek a [AppService mappához tartozó használati](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Elérhető a következők:

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1) – Ez a példa lekéri az Azure App Service-ben az Azure Stacken számlázási rekordok az Azure Stack használati API
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1) – ezt a mintát az Azure App Service-ben az Azure Stack használatának összegek előfizetésenként számítja ki.  Ez a szkript kiszámítja a használati összeget, és a Usage API-t és az árak a felhő üzemeltetője mérni / biztosítják.
- [Suspend-UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1) – Ez a minta felfüggeszti vagy előfizetés lehetővé teszi, hogy a felhő üzemeltetője által meghatározott használati korlát alapján.

## <a name="next-steps"></a>További lépések

- [Az Azure Stack-bérlői használati API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png