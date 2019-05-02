---
title: Azure Stack 1904 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1904 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 05/02/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 05/02/2019
ms.openlocfilehash: fd93ba34b712d5f6c4dc71c226e176f44a7bc6dc
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987628"
---
# <a name="azure-stack-1904-update"></a>Azure Stack 1904 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1904 csomag tartalmát. A frissítés magában foglalja az új fejlesztések és javítások ebben a kiadásban az Azure Stack. Ez a cikk a következő információkat tartalmazza:

- [What's new leírása fejlesztései, a javításokat és a biztonsági frissítések](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Tervezés módosítása](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1904 frissítés buildszáma **1.1904.0.36**.

## <a name="whats-in-this-update"></a>Mi az a frissítés

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- 1904 jelentős fejlesztések történtek a szoftveralapú hálózatkezelés (SDN) stack. Ezek a fejlesztések általános karbantartási és az SDN-verem az Azure Stackben megbízhatóságának növelése érdekében.

- Hozzá a felügyeleti portálon, egy értesítés, amikor a bejelentkezett felhasználó nem rendelkezik a szükséges engedélyekkel, amely lehetővé teszi az irányítópult megfelelően betölteni. A dokumentáció, amely azt ismerteti, hogy mely fiókok rendelkezik a megfelelő engedélyekkel, attól függően, a telepítés során használt identitásszolgáltató mutató hivatkozást is tartalmaz.

- A hozzáadott fejlesztései a virtuális gép rugalmasságát és hasznos üzemidőt garantál, amely kiküszöböli a forgatókönyvet, amelyben minden virtuális gép go offline állapotban van, ha a virtuális gép konfigurációs fájljait tartalmazó tároló kötet offline állapotba kerül.

<!-- 1901,2,3 related hotfix -->
- A virtuális gépek száma a hozzáadott optimalizálás evakuálni egyidejűleg, és elhelyezett egy korlát, cím VM túlterhelődések vagy blackouts, ha a hálózat nagy terhelés alatt felhasznált sávszélesség. Ez a változás növeli a virtuális gép hasznos, a rendszer frissítésekor.

<!-- 1901,2,3 related hotfix -->
- Továbbfejlesztett erőforrás-szabályozás egyszerre több platform erőforrásokat, a portálon a sikertelen műveleteket eredményez kimerítsék belső folyamatok elleni védelem érdekében a rendszer futtatásakor.

- Továbbfejlesztett szűrési képességek lehetővé teszi a kezelők egyszerre több szűrőket alkalmaz. A csak rendezheti a **neve** az új felhasználói felület az oszlopban.

- Fejlesztések a folyamatot, az ajánlatok, tervek, kvóták és előfizetések törlése. Most már sikeresen törölheti ajánlatok, kvóták, tervek és előfizetések a felügyeleti portálról, ha a törölni kívánt objektum egy csoporttól sem. További információkért tekintse meg [ezt a cikket](azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Javult a syslog-üzenetek mennyisége szűri ki a szükségtelen eseményeket, és válassza ki a kívánt súlyossági szinttel továbbított üzenetek egy konfigurációs paraméter megadásával. A súlyossági szintet konfigurálásával kapcsolatos további információkért lásd: [adatközpont integrációja az Azure Stack - syslog-továbbítás](azure-stack-integrate-security.md).

- Az Azure Stack-infrastruktúra használ fel további 12 GB + (4 GB-os * állomások számát az Azure Stack) a 1904 frissítés és újabb verziók esetében. Ez azt jelenti, hogy a 4 csomópont bélyeggel lesz egy további kapacitás felhasználását, 28 GB-os (12 GB + 4 GB-os * 4) az Azure Stack rendszergazdai portálon kapacitás képernyőjén megjelennek. A frissítés 1904 kiadására sikeres legyen, akkor is, ha további csökkenhet az Azure Stack-blokk helyezi a kapacitás fölé. Ha az Azure Stack-blokk feletti memóriahasználat a frissítés befejezése után, ebben az állapotban szervizelési teendőkről megszüntetéséhez lefoglalni az egyes virtuális gépek tükröző riasztás megjelenik.

<!--this applied to Bug 1473487 -->
- Az egyik új funkciója a **Get-AzureStackLog** parancsmag egy további paraméter beágyazza `-OutputSASUri`. Most az Azure Stack gyűjteni a környezetben, és tárolja őket a megadott Azure Storage blob-tárolóban. További információkért lásd: [Azure Stack-diagnosztika](azure-stack-diagnostics.md#examples).

- Egy új, a memória-ellenőrzés hozzáadva a **Test-AzureStack** `UpdateReadiness` csoport, amely ellenőrzi, hogy van-e elég szabad memória a veremben a frissítés sikeresen befejeződik.

<!-- Bug/Task 4311058 -->
- Továbbfejlesztett **Test-AzureStack** a Service Fabric állapotának értékeléséhez.

<!-- feature: 2976966 -->
- 2 – 4 óra frissítési fejlesztései hardverfrissítések, ami csökkenti a meghajtó belső vezérlőprogramjának végrehajtásához szükséges időt. A frissítési motorral dinamikusan határozza meg, mely részeit a frissítést végre kell hajtani, a csomag tartalmához alapján.

<!-- Feature 3906611 -->
- A hozzáadott robusztus művelet Eszközfrissítések zavart okozó infrastruktúra szerepkör példány műveletek rendelkezésre állást befolyásoló elkerülése érdekében.

<!-- Feature 3780326 -->
- Tervezze meg az infrastruktúra biztonsági mentési művelet idempotens fejlesztései.

<!--Bug/Task 3139609 -->
- Naplók gyűjtése az Azure Stack fejlesztései. Ezek a fejlesztések a set-naplók lekéréséhez szükséges idő csökkentése. Emellett a [Get-AzureStackLog](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) parancsmag már nem állít elő, az OEM szerepkör alapértelmezett naplókat. Végre kell hajtani a [Invoke-AzureStackOnDemandLog](azure-stack-diagnostics.md#invoke-azurestackondemandlog) parancsmagot, adja meg a szerepkört a OEM naplók begyűjtéséről. További információkért lásd: [Azure Stack-diagnosztika](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Az Azure Stack most figyeli az összevonási adatok megadott URL-cím az AD FS-adatközpont integrációja. Ez növeli a megbízhatóságot az ügyfél AD FS-példányt, vagy a farm titkos rotációját során.

### <a name="changes"></a>Módosítások

<!-- Feature 3906611 -->
- Eltávolítja a beállítást, állítsa le a felügyeleti portálon infrastruktúra-szerepkör példányai az Azure Stack-operátorok számára. Az újraindítás funkciókat biztosítja, hogy egy tiszta Leállítás utáni állapotba kísérlet az infrastruktúra szerepkörpéldány újraindítása előtt. Speciális forgatókönyvek esetén az API és PowerShell funkciót elérhető marad.

<!-- Feature ## 4199257 -->
- Egy új piactér megoldást, a Marketplace-rendszerképek és erőforrás-szolgáltatókat külön képernyők van. Most a **erőforrás-szolgáltatók** ablak üres, de a jövőbeli kiadásokban új PaaS szolgáltatásajánlatok fognak megjelenni, és kezelni a **erőforrás-szolgáltatók** ablak.

<!-- Feature ## 4199257 -->
- A frissítési élmény az operátor Portalon módosításait. Van egy új grid erőforrás-szolgáltató frissítésekhez. Erőforrás-szolgáltatók frissíthetik még nem érhető el.

<!-- Task ## 3748423  -->
- A frissítés telepítési élmény, a kezelő portálon módosításait. Azure Stack segítségével operátorok megfelelő választ egy frissítési probléma a portál most nyújt több konkrét javaslatokért állapotát, a skálázási egység alapján automatikusan származtatott futtatásával **Test-AzureStack** és -elemzés az eredményeket. Az eredmény alapján, tájékoztatja az operátor két műveletek egyikét:

  - A "soft" figyelmeztetés jelenik meg a portálon, amely beolvassa az "a legutóbbi frissítés igények figyelmet. A Microsoft javasolja, hogy egy szolgáltatási kérelem megnyitása a szokásos munkaidejében. A frissítési folyamat részeként a Test-AzureStack végrehajtani, és alapú kimenetén azt a leginkább megfelelő riasztás létrehozása. Ebben az esetben Test-AzureStack átadott."

  - Egy "rögzített" kritikus riasztások jelennek meg a portálon, amely beolvassa az "a legújabb frissítés nem sikerült. A Microsoft javasolja, hogy minél hamarabb megnyitása egy szolgáltatási kérelmet. A frissítési folyamat részeként a Test-AzureStack végrehajtani, és alapú kimenetén azt a leginkább megfelelő riasztás létrehozása. Ebben az esetben Test-AzureStack is nem sikerült."

- Frissített Azure Linux-ügynök verziója 2.2.38.0. Ez a támogatás lehetővé teszi az egységes Linux-rendszerképeket az Azure és az Azure Stack közötti fenntartásához.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Rögzített, amelyben a syslog-konfiguráció nem fájladatot folyamata alatt tartózkodnak egy frissítést, a syslog-ügyfél elveszíti a konfigurációját, és leállítja a továbbított syslog-üzeneteket okoz problémát. Syslog konfigurálása most megőrződik.

- Javítva lett egy probléma, amely blokkolja a virtuális gépek felszabadítási CRP. Korábban Ha egy virtuális gép több nagy méretű felügyelt lemezek, a virtuális gép felszabadítása előfordulhat, hogy sikertelen volt az időtúllépési hiba.

- Kijavítva a hiba a Windows Defender motor érintő bővítőegysége tárak elérésére.

- Kijavítottuk a felhasználói portál, amelyben a hozzáférési házirend ablakot a blob storage-fiókok betöltése nem sikerült.

- Javítva lett egy probléma rendszergazdai és felhasználói portálok, amelyben a globális Azure portállal kapcsolatos téves értesítések megjelentek.

- A felhasználói portál javítva melyik kiválasztásakor a **visszajelzés** csempe okozott az üres böngésző elemére kattintva nyissa meg.

- Kijavítottuk a portál melyik módosításával a statikus IP-címet az IP-konfiguráció lett kötve egy hálózati adapter egy Virtuálisgép-példányhoz csatolt okozott megjelenítendő hibaüzenetet.

- Kijavítva a felhasználói portál melyik tett kísérlet során **hálózati adapter csatolása** keresztül meglévő virtuális géphez a **hálózatkezelés** ablak az okozza, a művelet egy hibaüzenettel meghiúsul.

- Kijavítva, amelyben az Azure Stack nem támogat több mint 4 hálózati adapter (NIC) csatolása a VM-példányra.

- Melyik hozzáadása egy bejövő biztonsági szabályt, majd válassza a portál javítva **Szolgáltatáscímke** a forrásaként jelenik meg, amelyek nem érhetők el az Azure Stack több lehetőség.

- A probléma, amelyben a hálózati biztonsági csoportok (NSG-k) nem működött az Azure Stackben ugyanúgy, mint az Azure globális a rögzített.

- Kijavítva a Marketplace-kezelés minden letöltött termékek eltűnik, ha a regisztráció jár, vagy a rendszer eltávolítja.

- Kijavítva a melyik kiadása egy **Set-AzureRmVirtualNetworkGatewayConnection** egy meglévő virtuális hálózati átjáró kapcsolatának parancsot a PowerShellben a hiba miatt nem sikerült **érvénytelen megosztott kulcs van konfigurálva ...** .

- A hibát, amely miatt a hálózati erőforrás szolgáltató (NRP) nincs szinkronban a hálózati vezérlő, ismétlődő erőforrások a kért eredményez kell rögzíteni. Bizonyos esetekben ez eredményezett és a szülő erőforrás hiba állapotban.

- Javítva lett egy probléma, amelyben Ha egy előfizetésre, a közreműködő szerepkört rendeltek, de nem kifejezetten adott felhasználó olvasási engedéllyel, hiba jött létre, amely **... Az ügyfél "somelogonaccount@domain.com" objektum azonosítója {GUID} nem rendelkezik a művelet végrehajtására...**  megkísérlésekor. a módosítás mentéséhez egy erőforráshoz.

- Kijavítva, amelyben a Marketplace-en felügyeleti képernyő üres volt a kapcsolat nélküli szindikálási eszköz használt tölthet fel képeket, és ezek közül bármelyik hiányzik a URI(s) ikonra.

### <a name="security-updates"></a>Biztonsági frissítések

Ez a frissítés az Azure Stack nem tartalmazza az alapul szolgáló operációs rendszer, amely az Azure Stack biztonsági frissítéseket. További információ: [Azure Stack biztonsági frissítések.](azure-stack-release-notes-security-updates-1904.md)

## <a name="update-planning"></a>Tervezés módosítása

A frissítés alkalmazásához, mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1904.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates-1904.md)
- [Alkalmazása a frissítés előtti és utáni tevékenységek ellenőrzőlista](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1904 frissítési csomagot [az Azure Stack letöltés oldal](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Mindenképpen a legújabb Azure Stack gyorsjavítás telepítése 1903 az Azure Stack 1904 frissítése előtt.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

### <a name="before-applying-the-1904-update"></a>A 1904 alkalmazása előtt frissítése

Az Azure Stack 1904 kiadását kell alkalmazni az a következő gyorsjavításokat 1903 kiadására:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Az Azure Stack gyorsjavítás 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Frissítse a 1904 sikeres alkalmazása után

Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: a [karbantartási szabályzat](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Nincs elérhető 1904 gyorsjavítások.

## <a name="automatic-update-notifications"></a>Az automatikus frissítési értesítések

Ügyfelek, a rendszerek, amelyek hozzáférnek az internethez, az infrastruktúra-hálózaton fogják látni a **frissítés érhető el** az operátor Portal webhelyen megjelenő. Internet-hozzáférés nélküli rendszerekhez letöltheti, és importálja a megfelelő .xml a .zip fájlt.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
- Adja meg a felmérés kitöltése [kibocsátási megjegyzések a visszajelzések](https://forms.microsoft.com).
