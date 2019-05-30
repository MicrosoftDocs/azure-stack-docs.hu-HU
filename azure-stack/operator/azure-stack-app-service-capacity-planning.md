---
title: Kapacitás megtervezése a az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben |} A Microsoft Docs
description: Az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben kapacitástervezése
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 8c41bd0241339cc4c29637bc70ef0fb71704feee
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269180"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben kapacitástervezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Állítsa be az Azure App Service az Azure Stacken kész éles környezet, meg kell terveznie a kapacitás, a rendszer támogatja a várt.  

Ez a cikk nyújt útmutatást a számítási példányok és számítási éles rendszerek üzembe használjon SKU-k minimális száma.

Az App Service-ben kapacitás stratégia használatával ezeket az irányelveket is tervezhet.

| Az App Service-kiszolgálói szerepkör | Legrégebbi ajánlott példányok száma | Javasolt számítási Termékváltozat|
| --- | --- | --- |
| Vezérlő | 2 | A1 |
| Előtér | 2 | A1 |
| Kezelés | 2 | A3 |
| Gyártó | 2 | A1 |
| Webes feldolgozó – megosztott | 2 | A1 |
| Webes feldolgozó – dedikált | 2 / csomag | A1 |

## <a name="controller-role"></a>Vezérlői szerepkör

**Ajánlott minimális**: A1 Standard két példánya

Az Azure App Service-vezérlő általában teljesen alacsony felhasználás a Processzor, memória és hálózati erőforrásokat. Ugyanakkor a magas rendelkezésre állás érdekében rendelkeznie kell két vezérlőn. Két vezérlőn is rendelkezésre állnak a tartományvezérlők engedélyezett maximális számát. Hozhat létre a második websites-vezérlő közvetlen a telepítő a telepítés során.

## <a name="front-end-role"></a>Előtér-szerepkör

**Ajánlott minimális**: A1 Standard két példánya

A webes feldolgozó rendelkezésre állás a webes feldolgozók előtér-útvonalak kéréseket. A magas rendelkezésre állás érdekében egynél több előtér rendelkeznie kell, és legfeljebb két rendelkezhet. Kapacitástervezési célból fontolja meg, hogy egyes maghoz kezelhetik a körülbelül 100 vonatkozó kérelmek másodpercenkénti száma.

## <a name="management-role"></a>Felügyeleti szerepkör

**Ajánlott minimális**: A3 Standard két példánya

Az Azure App Service felügyeleti szerepkör az App Service az Azure Resource Manager és API-végpontokat, portál extensions (felügyeleti, a bérlő, a Functions portálon.) és az adatszolgáltatás felelős. A felügyeleti kiszolgálói szerepkör általában csak a 4 GB RAM, éles környezetben kapcsolatos van szükség. Azonban ez tapasztalhat magas CPU-szintű (például a webhely létrehozása) számos felügyeleti feladatot el kell végezni. A magas rendelkezésre állás érdekében kell rendelkeznie ehhez a szerepkörhöz hozzárendelt egynél több kiszolgálón, és legalább két kiszolgáló magok.

## <a name="publisher-role"></a>Közzétevői szerepkör

**Ajánlott minimális**: A1 Standard két példánya

Hány felhasználó egyszerre tesz közzé, ha a közzétevői szerepkör tapasztalhat a nagy CPU-használat. Magas rendelkezésre állás érdekében győződjön meg arról, hogy egynél több közzétevői szerepkör-e. A kiadó csak kezeli az FTP-/ FTPS-forgalmat.

## <a name="web-worker-role"></a>Webes feldolgozó szerepkör

**Ajánlott minimális**: A1 Standard két példánya

A magas rendelkezésre állás érdekében legalább négy webes feldolgozói szerepkörök, két megosztott webhely-üzemmód és két minden csomagot az ajánlathoz dedikált feldolgozói réteg kell rendelkezniük. A közös vagy dedikált számítási mód szolgáltatás különböző szinteken biztosít a bérlők számára. Ha az ügyfelek számos további a webes feldolgozók lehet szükség:

- Dedikált számítási mód feldolgozói rétegek (amelyek erőforrás-igényes) használatával.
- A megosztott számítási üzemmódban fut.

Miután a felhasználó által létrehozott App Service-csomag esetében dedikált számítási üzemmódra Termékváltozat esetében, az App Service-csomag a megadott webes feldolgozót száma már nem érhető el a felhasználók számára.

Adja meg az Azure Functions a használatalapú csomag modell a felhasználók számára, megosztott a webes feldolgozók kell telepíteni.

Amikor eldönti, megosztott webes feldolgozói szerepkörök száma használatához, tekintse át ezeket a szempontokat:

- **Memória**: Memória mérete a kritikus fontosságú erőforrás egy webes feldolgozói szerepkör esetében. Nincs elég memória a virtuális memória lemezről átváltásakor hatással van a webhely teljesítményét. Minden kiszolgálón az operációs rendszer 1,2 GB RAM szükséges. A küszöbérték fölött RAM segítségével webhelyeket.
- **Aktív webhelyek százaléka**: Azure Stack üzembe helyezés az Azure App Service-alkalmazások körülbelül 5 %-os általában aktív. Azonban, amely egy adott időpontban aktív kérelmek aránya magasabb vagy alacsonyabb lehet. Egy aktív alkalmazás arány 5 %-os helyezze el az Azure Stack üzemelő példányon, egy Azure App Service-alkalmazások maximális számát (5 x 20 = 100) aktív webhelyek száma kevesebb mint 20 alkalommal kell lennie.
- **Átlagos memória-erőforrás-igényű**: Az alkalmazások éles környezetben megfigyelt átlagos memóriaigénye körülbelül 70 MB. Az erőforrás-igényű használja, minden webes feldolgozói szerepkörben működő számítógép vagy virtuális gépek számára fenntartott memória mérete számítható ki a következő:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Például ha vannak a környezetben, 10 webes feldolgozói szerepkörök 5000 alkalmazásokat, egyes webes feldolgozói szerepkörök VM 7060 MB-os RAM kell rendelkeznie:

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   Feldolgozópéldányok további adásával kapcsolatos információkért lásd: [további feldolgozói szerepkörök hozzáadása](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>A frissítés és karbantartás során dedikált dolgozó további szempontok

A frissítés és karbantartás a munkavállalók során Azure App Service az Azure Stacken végrehajtják a karbantartási a 20 %-át az egyes feldolgozói réteg egyidejűleg.  Felhőszolgáltatás-rendszergazdák emiatt mindig kell karbantartása nem lefoglalt feldolgozók száma feldolgozói réteg biztosítása a bérlőik számára nem tapasztalnak semmilyen szolgáltatáskimaradást frissítés és karbantartás során a 20 %-os készletét.  Például ha 10 feldolgozók található olyan feldolgozói réteg biztosítania kell, hogy 2 le nem foglalt, hogy a frissítés és karbantartás, ha a teljes 10 feldolgozók lesznek lefoglalva, kell növelheti a feldolgozói réteg nem lefoglalt feldolgozók készletét fenntartásához. A frissítés és karbantartás során az Azure App Service kerül át számítási feladatok nem lefoglalt Worker annak érdekében, hogy a számítási feladatokhoz továbbra is működik, azonban ha nem szabad során rendelkezésre álló feldolgozók frissítése majd van bérlői számítási feladatot a lehetséges lesz állásidő.  Megosztott feldolgozó tartományállapot ügyfeleknek nem kell további feldolgozók kiépítéséhez, mivel a szolgáltatás foglal le bérlői alkalmazások rendelkezésre álló feldolgozók belül automatikusan, azonban nincs magas rendelkezésre állású minimális követelmény két az adminisztratív munkatársak számára a jelen csomag.

Felhő-rendszergazdák figyelhetik a feldolgozói réteg felosztására, az App Service felügyeleti területen, az Azure Stack felügyeleti portálon.  Navigáljon az App Service-ben, és válassza ki a feldolgozói rétegek a bal oldali ablaktáblán.  A feldolgozói rétegek táblázat feldolgozói réteg neve, a mérete, a használt lemezképet, a rendelkezésre álló feldolgozók száma (szabad), az egyes szintek feldolgozók száma összesen és a feldolgozói réteg általános állapotát.

![Az App Service felügyeleti - feldolgozói rétegek][1]

## <a name="file-server-role"></a>Fájlkiszolgálói szerepkör

A fájlkiszolgáló szerepkört használhatja a különálló fájlkiszolgáló fejlesztési és tesztelési; Ha például az Azure App Service-ben a az Azure Stack Development Kit (ASDK) üzembe helyezésekor ezzel [sablon](https://aka.ms/appsvconmasdkfstemplate).  Termelési célra egy előre konfigurált Windows-fájlkiszolgáló, vagy egy előre konfigurált nem Windows fájlkiszolgáló kell használnia.

Éles környezetben a fájlkiszolgáló szerepkört teljesen intenzív lemez i/o. Kezelőkód összes tartalom és alkalmazás fájlt a felhasználó-webhelyekhez, mert előre konfigurálnia kell egy ehhez a szerepkörhöz a következő forrásanyagokat:

- Windows fájlkiszolgáló
- Windows fájlkiszolgáló fürt
- Nem-Windows-fájlkiszolgáló
- Nem-Windows-fájlkiszolgálói fürt
- (Hálózati tárolóeszközök) NAS-eszközök

A következő cikkben talál további információt [fájlkiszolgáló üzembe helyezése](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>További lépések

További információ a következő cikkben talál:

[Mielőtt elkezdené, az Azure Stack App Service-szel](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png