---
title: Azure Stack 1907 kibocsátási megjegyzések | Microsoft Docs
description: Ismerkedjen meg a 1907-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 961233bea7c2581e20cb5cae875b9dab9f617758
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497243"
---
# <a name="azure-stack-1907-update"></a>Azure Stack 1907 frissítés

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk az 1907-es frissítési csomag tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

A Azure Stack 1907 frissítési Build száma **1.1907.0.20**.

### <a name="update-type"></a>Frissítés típusa

A Azure Stack 1907 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](azure-stack-updates.md) cikkben. A belső tesztelés alapján a 1907-es frissítés befejezéséhez szükséges idő körülbelül 13 óra. 
 - A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek. 
 - A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. 
 - Ez a futásidejű közelítés az 1907-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A Azure Stack diagnosztikai napló gyűjtési szolgáltatásának általánosan elérhető kiadása a diagnosztikai naplók gyűjtésének megkönnyítéséhez és fejlesztéséhez. A Azure Stack diagnosztikai napló gyűjtése szolgáltatás lehetővé teszi a diagnosztikai naplók összegyűjtését és megosztását a Microsoft ügyfél-támogatási szolgálatával (CSS). A Azure Stack diagnosztikai napló-gyűjtési szolgáltatás új felhasználói élményt nyújt a Azure Stack felügyeleti portálon, amely lehetővé teszi, hogy a kezelők automatikusan feltöltsék a diagnosztikai naplókat egy tárolási blobba, amikor bizonyos kritikus riasztások bekövetkeznek, vagy végrehajtják Ugyanez a művelet igény szerint. További információkért lásd a [diagnosztikai napló gyűjtését](azure-stack-diagnostic-log-collection-overview.md) ismertető cikket.

- Egy belső titkos elforgatási eljárás lett hozzáadva a belső SQL TLS-tanúsítványok szükség szerinti elforgatásához a rendszer frissítése során.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack Update (frissítés) panel most megjeleníti az aktív frissítések utolsó lépésének időpontját. Ezt úgy tekintheti meg, ha a frissítés panelre kattint, és rákattint egy futó frissítésre. Az "utolsó lépés befejezve" szakasz a "frissítés futtatása részletei" szakaszban érhető el.

- A **Start-AzureStack** és a **stop-AzureStack** operátor műveleteinek fejlesztése. Az Azure Stack indításának ideje átlagosan 50%-kal csökkent. Az Azure Stack leállításához szükséges idő átlagosan 30%-kal csökkent. Az átlagos indítási és leállítási idők változatlanok maradnak, mint a csomópontok száma a méretezési egységekben. 

- Javított hibakezelés a leválasztott piactér eszközhöz. Ha a letöltés sikertelen vagy részlegesen sikeres az **export-AzSOfflineMarketplaceItem**használatakor, a rendszer részletes hibaüzenetet jelenít meg, amely további részleteket tartalmaz a hibával és a kockázatcsökkentő lépésekkel kapcsolatban, ha van ilyen.

- Javította a felügyelt lemezek létrehozásának teljesítményét egy nagyméretű oldal blob/pillanatkép alapján. Korábban egy nagyméretű lemez létrehozásakor időtúllépést váltott ki.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Továbbfejlesztett virtuális lemez állapot-ellenőrzését a csomópont leállítása előtt, hogy elkerülje a virtuális lemezek váratlan leválasztását.

- A belső naplók továbbfejlesztett tárolása rendszergazdai műveletekhez. Ez a művelet a rendszergazdai műveletek során jobb teljesítményt és megbízhatóságot eredményez azáltal, hogy minimalizálja a belső naplózási folyamatok memória-és tárterület-felhasználását. Előfordulhat, hogy az ügyfelek a felügyeleti portál frissítés paneljének jobb oldal betöltési idejét is láthatják. Ennek a fejlesztésnek a részeként a 6 hónaposnál régebbi frissítési naplók többé nem lesznek elérhetők a rendszeren. Ha a frissítésekhez naplókra van szüksége, ügyeljen arra, hogy az 1907-es frissítés végrehajtása előtt az összes frissítési művelet [összefoglalóját töltse le](https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-apply-updates) a 6 hónapnál régebbi verzióra.

### <a name="changes"></a>Módosítások

- Új kimenő szabály (HTTPS) lett hozzáadva a Azure Stack diagnosztikai napló-gyűjtési szolgáltatás kommunikációjának engedélyezéséhez. További információ: [Azure stack Datacenter Integration – közzétételi végpontok](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentéseket, ha a külső tárolóhely kapacitása kifogyott.

- Az infrastruktúra biztonsági mentései már nem tartalmazzák a tartományi szolgáltatások adatai biztonsági mentését. Ez csak a Azure Active Directoryt használó rendszerekre vonatkozik az identitás-szolgáltatóként.

- Mostantól ellenőrizni fogjuk, hogy a számítási > virtuálisgép-lemezképek paneljén betöltött kép oldal blob típusú.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Kijavított egy olyan problémát, amelyben a közzétevőt, az ajánlatot és az SKU-t egy Resource Manager-sablonban bizalmasként kezeli a rendszer: a rendszerkép nem lett beolvasva a központi telepítéshez, kivéve, ha a képparaméterek nem egyeznek meg a közzétevő, az ajánlat és az SKU esetében.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- A tárolási szolgáltatás metaadatainak biztonsági mentésének időtúllépése miatt nem sikerült a **PartialSucceeded** hibaüzenettel rendelkező biztonsági mentések hibájának kijavítása.  

- Kijavítva a probléma, amelyben a felhasználói előfizetések törlése árva erőforrásokat eredményezett.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a WMI-szolgáltató gazdagépe zárolta a DLL-fájlt.

-   Kijavított egy problémát a frissítési szolgáltatásban, amely megakadályozta az elérhető frissítések megjelenítését a frissítés csempén vagy erőforrás-szolgáltatón. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511282](https://support.microsoft.com/help/4511282/).

- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a felügyeleti sík helytelen konfiguráció miatt nem megfelelő állapotba kerül. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/).

- Kijavított egy problémát, amely megakadályozza, hogy a felhasználók elvégezzék a harmadik féltől származó rendszerképek üzembe helyezését a piactéren. Ez a probléma a 1906-es verzióban található, és a gyorsjavítások [KB4511259](https://support.microsoft.com/help/4511259/)van kijavítva.

- Kijavítva egy olyan problémát, amely miatt a virtuális gép létrehozása a felügyelt lemezképből sikertelen volt a felhasználói rendszerkép-kezelő szolgáltatás összeomlása miatt. Ez a probléma a 1906-es verzióban található, és javítva lett a [KB4512794](https://support.microsoft.com/help/4512794/)

- Kijavítva egy probléma, amelyben a virtuális gépek szifilisz-műveletei sikertelenek voltak, mert a appgateway gyorsítótár nem a várt módon frissült. Ez a probléma a 1906-es verzióban található, és javítva lett a [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

-   Kijavított egy problémát az állapot-erőforrás-szolgáltatóban, amely hatással volt a régió és a riasztási panel elérhetőségére a felügyeleti portálon. Ez a probléma a 1906-es verzióban található, és a [KB4512794](https://support.microsoft.com/help/4512794)-mel lett javítva.


## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](azure-stack-release-notes-security-updates-1907.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1907.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates-1907.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1907 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1906-es Azure Stack legújabb gyorsjavítást telepíti a 1907-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1907-update"></a>Az 1907-es frissítés alkalmazása előtt

A Azure Stack 1907-es kiadását a 1906-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.11.52](https://support.microsoft.com/help/4513119)

### <a name="after-successfully-applying-the-1907-update"></a>Az 1907-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Nem érhető el gyorsjavítás a 1907-es kiadáshoz.

## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerekkel rendelkező ügyfelek az **elérhető frissítés** üzenet jelenik meg az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

[A Azure stack kibocsátási megjegyzések régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)tekintheti meg. Ezek az archivált kibocsátási megjegyzések kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ a Azure Stack támogatásáról: [Azure stack karbantartási szabályzat](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
