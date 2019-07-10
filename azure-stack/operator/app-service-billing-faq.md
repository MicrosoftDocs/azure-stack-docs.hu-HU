---
title: Az Azure App Service-ben az Azure Stack számlázási áttekintése és gyakori kérdések |} A Microsoft Docs
description: Azure App Service az Azure Stacken mérése és számlázása részletei.
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
ms.openlocfilehash: 8b17020419183067793309055a6487f31da51246
ms.sourcegitcommit: d67ae2877e3b049bf70b2567b917e55394fb8984
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67712683"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Az Azure App Service-ben az Azure Stack számlázási áttekintése és gyakori kérdések

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk bemutatja, hogyan történik a felhő üzemeltetői az ajánlat az Azure App Service az Azure Stack számlázása, és hogyan azok bill használata során a szolgáltatás a bérlőik számára.

## <a name="billing-overview"></a>Számlázás áttekintése

Az Azure Stack-felhő üzemeltetői váltani, üzembe helyezése az Azure App Service az Azure Stack az Azure App Service és az Azure Functions bérlői képességeit nyújtani az ügyfeleknek az Azure Stack stamp alakzatot. Az Azure App Service erőforrás-szolgáltató infrastruktúra és a feldolgozói rétegek közötti oszthatók szerepkörök többféle típusú áll.

Infrastruktúra-szerepkörök nem számolunk fel, mert a szolgáltatás alapvető működéséhez szükséges. Infrastruktúra-szerepkörök kiterjeszthető, a felhő üzemeltetője bérlők igényeinek támogatásához szükséges. Az infrastruktúra-szerepkörök a következők:

- Vezérlők
- Felügyeleti szerepkörök
- Kiadók
- A kezelőfelületek

Feldolgozói rétegek állnak, amelyek két fő típusba sorolhatók: közös és a dedikált. Feldolgozó, a felhő üzemeltetője a következő feltételek szerint lesz számlázva.

## <a name="shared-workers"></a>Megosztott feldolgozó

Megosztott feldolgozó olyan több-bérlős és gazdagép ingyenes és közös App Service-csomagok és az Azure functions fogyasztásalapú több bérlő számára. Megosztott feldolgozó gridre bocsáthatja ki, amikor készként jelöli az Azure App Service erőforrás-szolgáltató a használati mérőszámok.

## <a name="dedicated-workers"></a>Dedikált feldolgozó

Dedikált feldolgozó a bérlők létrehozása App Service-csomagok vannak társítva. Ha például az S1 termékváltozat bérlők méretezheti 10 példányra alapértelmezés szerint. Ha egy bérlőt egy S1 szintű App Service-csomagot hoz létre, az Azure App Service foglal le egy App Service-csomag a bérlőt a kis méretű feldolgozói réteg méretezési példányt. A hozzárendelt feldolgozó már majd nem rendelhető hozzá semmilyen más bérlők számára elérhető. Ha a bérlő az App Service-csomag 10 példányra skálázható, kilenc további feldolgozóra törlődnek a rendelkezésre álló készlet, és vannak hozzárendelve a bérlő App Service-csomag.

Mérőszámok dedikált dolgozó vannak kibocsátva, ha:

- Készként jelöli az Azure App Service erőforrás-szolgáltatón.
- App Service-csomag rendelve.

Ez a számlázási modell lehetővé teszi, hogy a felhő üzemeltetői az készen áll az ügyfelek számára anélkül, hogy a dolgozók kellene fizetnie, amíg hatékonyan a bérlőjének App Service-csomag által használt fenntartott dedikált feldolgozók készletét hozza létre. 

Tegyük fel például, a kis méretű feldolgozói réteg 20 feldolgozók rendelkezik. Majd ha öt, hozzon létre két S1 szintű App Service-csomagot minden ügyfele van, és ezek mindegyike méretezése App Service-csomag legfeljebb két példányt, azok nem feldolgozó is rendelkezésre áll. Ennek eredményeképpen nincs bármely ügyfél vagy az új ügyfeleket horizontális felskálázás, vagy hozzon létre új App Service-csomagok nem kapacitását. 

Felhő üzemeltetői / feldolgozói réteg rendelkezésre álló feldolgozók jelenlegi száma megtekintéséhez az Azure App Service a konfigurációban az Azure Stack felügyeleti a feldolgozói rétegek Hibaoldal.

![Az App Service - feldolgozói rétegek képernyő][1]

## <a name="see-customer-usage-by-using-the-azure-stack-usage-service"></a>Tekintse meg az ügyfelek általi használatot az Azure Stack használatának használatával

Lekérdezheti a felhő üzemeltetői az [Azure Stack bérlő erőforrás-használati API](azure-stack-tenant-resource-usage-api.md) ügyfelek használati adatainak beolvasása. Összes a egyéni mérőszámok, amelyek az App Service-ben írja le a bérlői használat bocsát ki a [használat – gyakori kérdések](azure-stack-usage-related-faq.md). Ezeket a mérőszámok majd rendszer kiszámítja a használatot biztosít ügyfél-előfizetés költségek kiszámításához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Hogyan hajthatom végre licenc az SQL Server és az előfeltételeket szükséges kiszolgálói infrastruktúrát?

Az SQL Server és a kiszolgálói infrastruktúra és az Azure App Service erőforrás-szolgáltató által igényelt licencelési foglalkozik az Azure App Service az Azure Stacken [használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) cikk.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>A használat – gyakori kérdések a bérlő mérőszámok, de nem az adott mérőszámok árak sorolja fel. Hol találhatok őket?

Felhő-felelősként Ön azonban a alkalmazni a saját díjszabási modell az ügyfelek számára. A használatmérő szolgáltatást nyújt a használatmérés. Ezután használhatja a mérőszám-mennyiség, az ügyfelek alapján meghatározhatja, hogy a díjszabási modell díja szerint számítjuk fel. Megadhatja a díjszabás lehetővé teszi, hogy az operátorok és az egyéb Azure Stack-operátorok megkülönböztetése érdekében.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Egy CSP-hez, hogyan tudom kínálnak ingyenes és közös SKU-k, az ügyfelek számára a szolgáltatás kipróbálásához?

Felhő-felelősként az ajánlat ingyenes és közös SKU-k, mivel azokat már a megosztott feldolgozók költségek számítunk fel. A költségek minimalizálása érdekében választhat a legalább a megosztott feldolgozói réteg leskálázása. 

Például az ajánlat ingyenes és közös App Service-csomag SKU-k és fogyasztásalapú funkciókat kínáló kell legalább egy a1-es példány érhető el. Megosztott feldolgozó több-bérlős, így több ügyfél alkalmazások esetében minden egyes külön-külön elkülönített, és az App Service tesztkörnyezetben által védett üzemeltethető. Ezzel a módszerrel a megosztott feldolgozói réteg felskálázásával korlátozhatja a költségek és a költség az egy vCPU / hó.

Ezután kiválaszthatja kvóta, egy terv, amely csak az ingyenes és közös termékváltozatok kínál, és korlátozza az ingyenes és közös App Service-csomagok az ügyfél hozhat létre a használatra.

## <a name="sample-scripts-to-assist-with-billing"></a>Mintaszkriptek, amelyek segítik a számlázás

Az Azure App Service csapata létrehozott minta PowerShell-parancsfájlok, amelyek segítik a lekérdezése az Azure Stack használatmérő szolgáltatást. Felhő üzemeltetői a mintaszkriptek használatával saját számlázás a bérlők előkészítése. A minta parancsprogramok vannak a [Azure Stack eszközök tárház](https://github.com/Azure/AzureStack-tools) a Githubon. Az App Service-parancsprogramok vannak a [AppService mappához tartozó használati](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Elérhető a következők:

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1): Ez a minta olvas be az Azure App Service-ben a számlázási rekordok az Azure Stack az Azure Stack-használati API.
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1): Ez a példa az Azure App Service-ben az Azure Stack használatának összegek előfizetésenként számítja ki. Ez a szkript kiszámítja a használati összeget, és a Usage API-t és az árak a felhő üzemeltetője mérni / biztosítják.
- [Suspend-UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1): Ez a minta felfüggeszti, vagy lehetővé teszi, hogy az előfizetések a felhő üzemeltetője által meghatározott használati korlátok alapján.

## <a name="next-steps"></a>További lépések

- [Az Azure Stack bérlő erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
