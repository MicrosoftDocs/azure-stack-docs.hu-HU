---
title: Azure Stack 1907 kibocsátási megjegyzések | Microsoft Docs
description: Tudnivalók a Azure Stack integrált rendszerek frissítéseiről 1907
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5ac273ef07747ffa0f5f32e681afd25ecc60bad6
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187214"
---
# <a name="azure-stack-updates-1907-release-notes"></a>Azure Stack frissítések: 1907 kibocsátási megjegyzések

Ez a cikk a Azure Stack frissítési csomagjainak tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues-1907.md)
- [Biztonsági frissítések](../release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](../release-notes-checklist.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért tekintse meg a [javítások és a frissítési problémák elhárítása Azure stack](../azure-stack-troubleshooting.md).

## <a name="1907-build-reference"></a>1907-Build referenciája

A Azure Stack 1907 frissítési Build száma **1.1907.0.20**.

### <a name="update-type"></a>Frissítéstípus

A Azure Stack 1907 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](../azure-stack-updates.md) cikkben. A belső tesztelés alapján a 1907-es frissítés befejezéséhez szükséges idő körülbelül 13 óra.

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futásidejű közelítés az 1907-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A Azure Stack diagnosztikai napló gyűjtési szolgáltatásának általánosan elérhető kiadása a diagnosztikai naplók gyűjtésének megkönnyítéséhez és fejlesztéséhez. A Azure Stack diagnosztikai napló gyűjtése szolgáltatás lehetővé teszi a diagnosztikai naplók összegyűjtését és megosztását a Microsoft ügyfél-támogatási szolgálatával (CSS). Ez a diagnosztikai napló-gyűjtési szolgáltatás új felhasználói élményt nyújt a Azure Stack felügyeleti portálon, amely lehetővé teszi, hogy az operátorok bizonyos kritikus riasztások esetén beállítsák a diagnosztikai naplók automatikus feltöltését egy Storage-blobba, vagy igény szerint végezze el ugyanezt a műveletet. További információkért lásd a [diagnosztikai napló gyűjtését](../diagnostic-log-collection.md) ismertető cikket.

- A Azure Stack hálózati infrastruktúra ellenőrzésének általánosan elérhető kiadása az Azure Stack Validation Tool **test-AzureStack** részeként. Azure Stack hálózati infrastruktúra a **test-AzureStack** részét képezi, annak megállapításához, hogy hiba történik-e a Azure stack hálózati infrastruktúráján. A teszt ellenőrzi a hálózati infrastruktúra kapcsolatát, ha megkerüli a Azure Stack szoftver által meghatározott hálózatot. Egy nyilvános VIP kapcsolatát mutatja be a konfigurált DNS-továbbítókkal, az NTP-kiszolgálókkal és az identitás-végpontokkal. Emellett ellenőrzi az Azure-hoz való csatlakozást, ha az Azure AD-t az identitás-szolgáltatóként vagy az összevont kiszolgálóként használja az ADFS használatakor. További információkért tekintse meg a [Azure stack ellenőrzési eszközét](../azure-stack-diagnostic-test.md) ismertető cikket.

- Egy belső titkos elforgatási eljárás lett hozzáadva a belső SQL TLS-tanúsítványok szükség szerinti elforgatásához a rendszer frissítése során.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack Update (frissítés) panel most megjeleníti az aktív frissítések **utolsó lépésének befejezési** idejét. Ezt úgy tekintheti meg, ha a frissítés panelre kattint, és rákattint egy futó frissítésre. Az **utolsó lépés befejeződött** , majd elérhető a **frissítés futtatása részletek** szakaszban.

- A **Start-AzureStack** és a **stop-AzureStack** operátor műveleteinek fejlesztése. Azure Stack elindításának ideje a 50%-os átlagos értékkel csökkent. A leállítási idő Azure Stack átlagosan 30%-kal csökkent. Az átlagos indítási és leállítási idők változatlanok maradnak, mint a csomópontok száma a méretezési egységekben.

- Javított hibakezelés a leválasztott piactér eszközhöz. Ha a letöltés sikertelen vagy részlegesen sikeres az **export-AzSOfflineMarketplaceItem** használatakor, a rendszer részletes hibaüzenetet jelenít meg, amely további részleteket tartalmaz a hibával és a kockázatcsökkentő lépésekkel kapcsolatban, ha van ilyen.

- Javította a felügyelt lemezek létrehozásának teljesítményét egy nagyméretű oldal blob/pillanatkép alapján. Korábban egy nagyméretű lemez létrehozásakor időtúllépést váltott ki.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Továbbfejlesztett virtuális lemez állapot-ellenőrzését a csomópont leállítása előtt, hogy elkerülje a virtuális lemezek váratlan leválasztását.

- A belső naplók továbbfejlesztett tárolása rendszergazdai műveletekhez. Ez a felügyeleti műveletek során jobb teljesítményt és megbízhatóságot eredményez azáltal, hogy minimalizálja a belső naplózási folyamatok memória-és tárterület-felhasználását. Emellett a felügyeleti portál frissítés paneljének továbbfejlesztett oldal betöltési idejét is megfigyelheti. Ennek a fejlesztésnek a részeként a 6 hónaposnál régebbi frissítési naplók többé nem lesznek elérhetők a rendszeren. Ha a frissítések esetében naplókra van szüksége, ügyeljen arra, hogy az 1907-es frissítés végrehajtása előtt [töltse le az](../azure-stack-apply-updates.md) összes frissítési művelet összefoglalóját 6 hónapnál régebbi verzióra.

### <a name="changes"></a>Módosítások

- Az 1907-es verzió olyan figyelmeztető riasztást tartalmaz, amely arra utasítja a kezelőket, hogy az 1908-as verzióra való frissítés előtt frissítse a rendszer OEM-csomagját a 2,1-es vagy újabb verzióra. Azure Stack Az Azure Stack OEM-frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure stack eredeti berendezésgyártó frissítésének alkalmazása](../azure-stack-update-oem.md).

- Új kimenő szabály (HTTPS) lett hozzáadva a Azure Stack diagnosztikai napló-gyűjtési szolgáltatás kommunikációjának engedélyezéséhez. További információ: [Azure stack Datacenter Integration – közzétételi végpontok](../azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentéseket, ha a külső tárolóhely kapacitása elfogy.

- Az infrastruktúra biztonsági mentései már nem tartalmazzák a tartományi szolgáltatások adatai biztonsági mentését. Ez csak azokra a rendszerekre vonatkozik, amelyek a Azure Active Directory használják az identitás-szolgáltatóként.

- Most ellenőrizjük, hogy egy, a **számítási > virtuálisgép-lemezképek** panelén betöltött kép oldal blob típusú.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Kijavított egy olyan problémát, amelyben a közzétevőt, az ajánlatot és az SKU-t egy Resource Manager-sablonban bizalmasként kezeli a rendszer: a rendszerkép nem lett beolvasva a központi telepítéshez, kivéve, ha a képparaméterek nem egyeznek meg a közzétevő, az ajánlat és az SKU esetében.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- A tárolási szolgáltatás metaadatainak biztonsági mentésének időtúllépése miatt nem sikerült a **PartialSucceeded** hibaüzenettel rendelkező biztonsági mentések hibájának kijavítása.  

- Kijavítva egy probléma, amelyben a felhasználói előfizetések törlése árva erőforrásokat eredményezett.

- Kijavított egy olyan problémát, amelyben a leírás mező nem lett mentve az ajánlat létrehozásakor.

- Kijavítva egy probléma, amelyben a **csak olvasási** jogosultsággal rendelkező felhasználók létrehozhatják, szerkeszthetik és törölhetik az erőforrásokat. Most a felhasználó csak akkor tud erőforrásokat létrehozni, ha a **közreműködői** engedély hozzá van rendelve. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a WMI-szolgáltató gazdagépe zárolta a DLL-fájlt.

- Kijavított egy problémát a frissítési szolgáltatásban, amely megakadályozta az elérhető frissítések megjelenítését a frissítés csempén vagy erőforrás-szolgáltatón. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511282](https://support.microsoft.com/help/4511282/).

- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a felügyeleti sík helytelen konfiguráció miatt nem megfelelő állapotba kerül. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/).

- Kijavított egy problémát, amely megakadályozza, hogy a felhasználók elvégezzék a harmadik féltől származó rendszerképek üzembe helyezését a piactéren. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511259](https://support.microsoft.com/help/4511259/).

- Kijavítva egy olyan problémát, amely miatt a virtuális gép létrehozása a felügyelt lemezképből sikertelen volt a felhasználói rendszerkép-kezelő szolgáltatás összeomlása miatt. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/)

- Kijavítva egy probléma, amelyben a virtuális gépek szifilisz-műveletei sikertelenek voltak, mert az App Gateway gyorsítótára nem a várt módon frissült. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

- Kijavított egy problémát az állapot-erőforrás-szolgáltatóban, amely hatással volt a régió és a riasztási panel elérhetőségére a felügyeleti portálon. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](../release-notes-security-updates.md).

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1907 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1906-es Azure Stack legújabb gyorsjavítást telepíti a 1907-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1907-update"></a>Az 1907-es frissítés alkalmazása előtt

A Azure Stack 1907-es kiadását a 1906-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Az 1907-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1907.17.54](https://support.microsoft.com/help/4523826)