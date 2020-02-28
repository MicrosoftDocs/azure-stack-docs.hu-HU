---
title: Kapacitás megtervezése App Service kiszolgálói szerepkörökhöz Azure Stack hub-ban
description: További információ a Azure Stack hub App Service kiszolgálói szerepköreinek kapacitásának megtervezéséről.
author: BryanLa
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/20192
ms.openlocfilehash: 9e9447baf9f5676cac8555513682bab8da750bb2
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701173"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack-hub"></a>Kapacitás megtervezése App Service kiszolgálói szerepkörökhöz Azure Stack hub-ban

Az Azure App Service Azure Stack hub-on történő éles használatra kész központi telepítésének beállításához meg kell terveznie a rendszer által támogatott kapacitást.  

Ez a cikk útmutatást nyújt az éles üzembe helyezéshez használni kívánt számítási példányok és számítási egységek minimális számához.

Ezen irányelvek alapján megtervezheti a App Service kapacitási stratégiát.

| App Service kiszolgálói szerepkör | A példányok minimálisan ajánlott száma | Ajánlott számítási SKU|
| --- | --- | --- |
| Tartományvezérlő | 2 | A1 |
| Előtér | 2 | A1 |
| Kezelés | 2 | A3 |
| Közzétevő | 2 | A1 |
| Webes feldolgozók – közös | 2 | A1 |
| Webes feldolgozók – dedikált | 2/szintenként | A1 |

## <a name="controller-role"></a>Vezérlő szerepkör

**Ajánlott minimum**: az a1 standard két példánya

A Azure App Service vezérlő általában a processzor, a memória és a hálózati erőforrások alacsony felhasználását tapasztalja. A magas rendelkezésre állás érdekében azonban két vezérlővel kell rendelkeznie. Két vezérlő is engedélyezett a maximálisan megengedett vezérlők számára. A második webhely-vezérlőt közvetlenül a telepítőből is létrehozhatja a telepítés során.

## <a name="front-end-role"></a>Előtér-szerepkör

**Ajánlott minimum**: az a1 standard két példánya

Az előtér-útvonalak a webes feldolgozók rendelkezésre állása alapján a webes feldolgozóknak küldött kérelmeket. A magas rendelkezésre állás érdekében több előtérrel kell rendelkeznie, és kettőnél több is lehet. A kapacitás megtervezése érdekében vegye figyelembe, hogy minden mag másodpercenként körülbelül 100 kérelmet képes kezelni.

## <a name="management-role"></a>Felügyeleti szerepkör

**Ajánlott minimum**: az a3 standard két példánya

Az Azure-alkalmazás klasszikus üzembe helyezési modellének feladata a App Service Azure Resource Manager és az API-végpontok, a portál-bővítmények (rendszergazda, bérlő, functions portál) és az adatszolgáltatás felelőse. A felügyeleti kiszolgálói szerepkörhöz általában csak 4 GB RAM-ra van szükség éles környezetben. Előfordulhat azonban, hogy magas CPU-szintet tapasztal, ha sok felügyeleti feladat (például webhely létrehozása) van végrehajtva. A magas rendelkezésre állás érdekében több kiszolgálót kell hozzárendelni ehhez a szerepkörhöz, és kiszolgálónként legalább két magot kell megadnia.

## <a name="publisher-role"></a>Közzétevői szerepkör

**Ajánlott minimum**: az a1 standard két példánya

Ha sok felhasználó egyszerre tesz közzé egyidejű közzétételt, a kiadói szerepkör nagy CPU-használatot tapasztalhatnak. A magas rendelkezésre állás érdekében győződjön meg arról, hogy egynél több közzétevői szerepkör érhető el. A közzétevő csak az FTP-/FTPS-forgalmat kezeli.

## <a name="web-worker-role"></a>Webes feldolgozói szerepkör

**Ajánlott minimum**: az a1 standard két példánya

A magas rendelkezésre állás érdekében legalább négy webes feldolgozói szerepkörrel kell rendelkeznie: kettőt a közös webhely üzemmódhoz, és kettőt minden olyan dedikált feldolgozói réteghez, amelyet kínál. A megosztott és dedikált számítási módok különböző szolgáltatási szinteket biztosítanak a bérlőknek. Ha sok ügyfelünk van, több webes feldolgozóra lehet szüksége:

- Dedikált számítási mód feldolgozói szintjeinek használata (amely erőforrás-igényes).
- Megosztott számítási módban fut.

Miután a felhasználó létrehozott egy App Service tervet egy dedikált számítási mód SKU-hoz, az adott App Service-csomagban megadott webes munkavégző (k) száma már nem érhető el a felhasználók számára.

Ha Azure Functions szeretne biztosítani a felhasználók számára a használati terv modelljében, telepítenie kell a megosztott webes munkavégzőket.

A használni kívánt megosztott webes feldolgozói szerepkörök számának meghatározásakor tekintse át a következő szempontokat:

- **Memória**: a memória a webes feldolgozói szerepkör legkritikusabb erőforrása. Nincs elég memória a webhely teljesítményére, ha a virtuális memóriát a lemezről cseréli a rendszer. Az egyes kiszolgálók 1,2 GB RAM memóriát igényelnek az operációs rendszerhez. A küszöbérték feletti RAM a webhelyek futtatására használható.
- **Az aktív webhelyek százalékos aránya**: általában az alkalmazások 5%-a az Azure stack hub-telepítés Azure app Service. Az adott pillanatban aktív alkalmazások százalékos aránya azonban magasabb vagy alacsonyabb lehet. Az 5%-os aktív alkalmazási arány esetében az Azure Stack hub-telepítésben a Azure App Serviceba helyezendő alkalmazások maximális száma kevesebb, mint 20 alkalommal az aktív webhelyek száma (5 x 20 = 100).
- **Átlagos memória-lábnyom**: az éles környezetekben megfigyelt alkalmazások átlagos memória-lábnyoma körülbelül 70 MB. Ennek a lábnyomnak a használatával a rendszer az összes webes feldolgozói szerepkörű számítógép vagy virtuális gép számára lefoglalt memóriát a következőképpen számítja ki:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Ha például 5 000 alkalmazás van 10 webes feldolgozói szerepkört futtató környezetben, minden webes feldolgozó szerepkörű virtuális gépnek 7060 MB RAM memóriával kell rendelkeznie:

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   További feldolgozói példányok hozzáadásával kapcsolatos információkért lásd: [további feldolgozói Szerepkörök hozzáadása](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>További szempontok a dedikált feldolgozók számára a frissítés és a karbantartás során

A feldolgozók frissítése és karbantartása során a Azure App Service on Azure Stack hub minden munkavégző réteg 20%-ában karbantartást végez egyszerre.  Ezért a Felhőbeli rendszergazdáknak mindig 20%-os készletet kell fenntartaniuk a nem lefoglalt feldolgozók számára a munkavégző szinten, hogy a bérlők ne tapasztalják a szolgáltatás elvesztését a frissítés és a karbantartás során.  Ha például 10 feldolgozója van egy feldolgozói szinten, akkor győződjön meg arról, hogy a frissítés és a karbantartás lehetővé teszi a 2 lefoglalását. Ha a teljes 10 feldolgozót kiosztják, a feldolgozói szintet fel kell mérnie, hogy fenntartsa a nem lefoglalt feldolgozók készletét. 

A frissítés és a karbantartás során Azure App Service áthelyezi a munkaterheléseket a nem lefoglalt feldolgozók számára, hogy a munkaterhelések továbbra is működőképesek legyenek. Ha azonban a frissítés során nem érhető el nem lefoglalt feldolgozók, akkor lehetséges, hogy a bérlői számítási feladatok leállása is fennáll. A megosztott feldolgozók tekintetében az ügyfeleknek nem kell további dolgozókat kiépíteniük, mivel a szolgáltatás automatikusan kiosztja a bérlői alkalmazásokat a rendelkezésre álló feldolgozókon belül. A magas rendelkezésre állás érdekében ebben a szinten két feldolgozóra van szükség.

A Felhőbeli rendszergazdák a Azure Stack hub felügyeleti portál App Service felügyeleti területén tudják figyelni a munkavégző rétegek kiosztását. Navigáljon App Service, majd válassza a feldolgozói rétegek elemet a bal oldali ablaktáblán. A feldolgozói rétegek táblázat a feldolgozói rétegek nevét, méretét, a felhasznált képeket, a rendelkezésre álló feldolgozók számát (nem lefoglalt), az egyes rétegek feldolgozóinak teljes számát és a feldolgozói szintek általános állapotát jeleníti meg.

![App Service adminisztráció – feldolgozói rétegek][1]

## <a name="file-server-role"></a>Fájlkiszolgálói szerepkör

A fájlkiszolgáló szerepkörhöz önálló fájlkiszolgáló használható fejlesztéshez és teszteléshez. Ha például a Azure App Service telepítését végzi a Azure Stack Development Kit (ASDK), akkor ezt a [sablont](https://aka.ms/appsvconmasdkfstemplate)használhatja.  Éles környezetben egy előre konfigurált Windows-fájlkiszolgáló vagy egy előre konfigurált, nem Windows-fájlkiszolgáló használatára van szüksége.

Éles környezetekben a fájlkiszolgálói szerepkör intenzív lemezes I/O-műveleteket tapasztal. Mivel a felhasználói webhelyek összes tartalom-és alkalmazásfájlok számára elérhetővé válik, előre be kell állítania a következő erőforrások egyikét ehhez a szerepkörhöz:

- Windows-fájlkiszolgáló
- Windows fájlkiszolgálói fürt
- Nem Windows rendszerű fájlkiszolgáló
- Nem Windows fájlkiszolgálói fürt
- NAS (hálózati csatlakoztatott tároló) eszköz

További információ: [fájlkiszolgáló kiépítése](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Következő lépések

[Az App Service Azure Stack hub-beli üzembe helyezésének előfeltételei](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png