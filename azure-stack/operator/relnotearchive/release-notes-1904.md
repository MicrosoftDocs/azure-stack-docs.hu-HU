---
title: Azure Stack 1904 kibocsátási megjegyzések | Microsoft Docs
description: Tudnivalók a Azure Stack integrált rendszerek 1904-es frissítéséről
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/30/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9ca014878021d0fbe02a8997e1436910557e93f5
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515923"
---
# <a name="azure-stack-1904-update"></a>Azure Stack 1904 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1904-es frissítési csomag tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza. Ez a cikk a következő információkat tartalmazza:

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1904 frissítési Build száma **1.1904.0.36**.

### <a name="update-type"></a>Frissítéstípus

A Azure Stack 1904 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](../azure-stack-updates.md) cikkben. Az 1904-es frissítés befejezéséhez szükséges idő körülbelül 16 óra, de a pontos idők eltérőek lehetnek. Ez a futásidejű közelítés az 1904-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Jelentős előrelépés történt a szoftveresen definiált hálózatkezelési (SDN) veremben, 1904-ben. Ezek a frissítések javítják az SDN-verem általános kiszolgálását és megbízhatóságát Azure Stackban.

- Értesítés a felügyeleti portálon, ha az aktuálisan bejelentkezett felhasználó nem rendelkezik a szükséges engedélyekkel, ami lehetővé teszi az irányítópult megfelelő betöltését. Emellett a dokumentációra mutató hivatkozást is tartalmaz, amely ismerteti, hogy mely fiókok rendelkeznek a megfelelő engedélyekkel az üzembe helyezés során használt identitás-szolgáltatótól függően.

- Javítottuk a virtuális gépek rugalmasságát és a rendelkezésre állást, ami megoldja azt a forgatókönyvet, amelyben az összes virtuális gép offline állapotba kerül, ha a virtuális gép konfigurációs fájljait tartalmazó tárolási kötet offline állapotba kerül.

<!-- 1901,2,3 related hotfix -->
- A virtuális gépek számának optimalizálása párhuzamosan, és a felhasznált sávszélességre vonatkozó korlátot is felvette, ha a hálózat nagy terhelés alatt van. Ez a változás növeli a virtuális gép üzemidőt a rendszer frissítésekor.

<!-- 1901,2,3 related hotfix -->
- Továbbfejlesztett erőforrás-szabályozás, ha a rendszer nagy léptékben fut, hogy védelmet nyújtson a belső folyamatoknak a platform erőforrásaihoz képest, ami a portálon meghiúsult műveleteket eredményez.

- A továbbfejlesztett szűrési funkciók lehetővé teszik, hogy a kezelők egyszerre több szűrőt alkalmazzanak. Csak az új felhasználói felület **Name (név** ) oszlopában lehet rendezni.

- Az ajánlatok, csomagok, kvóták és előfizetések törlési folyamatának fejlesztése. Mostantól sikeresen törölheti az ajánlatokat, kvótákat, csomagokat és előfizetéseket a felügyeleti portálról, ha a törölni kívánt objektum nem rendelkezik függőségekkel. További információkért tekintse meg [ezt a cikket](../azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Javított syslog-üzenetek mennyisége a szükségtelen események kiszűrésével és egy konfigurációs paraméter megadásával a továbbított üzenetek kívánt súlyossági szintjének kiválasztásához. További információ a súlyossági szint konfigurálásáról: [Azure stack Datacenter Integration – syslog forwarding](../azure-stack-integrate-security.md).

<!--this applied to Bug 1473487 -->
- Új képességet adott hozzá a **Get-AzureStackLog** parancsmaghoz egy további paraméter beépítésével `-OutputSASUri` . Mostantól összegyűjtheti Azure Stack naplókat a környezetből, és tárolhatja azokat a megadott Azure Storage blob-tárolóban. További információ: [Azure stack diagnosztika](/azure-stack/operator/diagnostic-log-collection).

- Új memória-ellenőrzés hozzáadva a **test-AzureStack** `UpdateReadiness` csoportban, amely ellenőrzi, hogy van-e elegendő memória a veremben a frissítés sikeres befejezéséhez.

<!-- Bug/Task 4311058 -->
- A **tesztelési AzureStack** fejlesztései Service Fabric állapotának kiértékeléséhez.

<!-- feature: 2976966 -->
- A hardveres frissítések fejlesztése, ami csökkenti a belső vezérlőprogram frissítésének 2-4 órán át történő elvégzéséhez szükséges időt. A frissítési motor dinamikusan meghatározza, hogy a frissítés mely részeit kell végrehajtani, a csomag tartalma alapján.

<!-- Feature 3906611 -->
- A robusztus működés elővizsgálatai meggátolják a rendelkezésre állást befolyásoló zavaró infrastruktúra-szerepkör-példányok működését.

<!-- Feature 3780326 -->
- Az infrastruktúra biztonsági mentési műveleti tervének idempotencia fejlesztései.

<!--Bug/Task 3139609 -->
- A Azure Stack naplózási gyűjteményének fejlesztése. Ezek a tökéletesítések csökkentik a naplók készletének lekéréséhez szükséges időt. Emellett a [Get-AzureStackLog](/azure-stack/operator/relnotearchive/azure-stack-configure-on-demand-diagnostic-log-collection-portal#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) parancsmag már nem hozza létre az alapértelmezett naplókat az OEM szerepkörhöz. Végre kell hajtania a [Meghívási-AzureStackOnDemandLog](/azure-stack/operator/relnotearchive/azure-stack-configure-on-demand-diagnostic-log-collection-portal#invoke-azurestackondemandlog) parancsmagot, és meg kell adnia a szerepkört az OEM-naplók lekéréséhez. További információ: [Azure stack diagnosztika](/azure-stack/operator/relnotearchive/azure-stack-configure-on-demand-diagnostic-log-collection-portal#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Azure Stack most az ADFS-vel való adatközpont-integrációhoz megadott összevonási adaturl-címet figyeli. Ez növeli a megbízhatóságot az ügyfél ADFS-példányának vagy-farmjának titkos forgása során.

### <a name="changes"></a>Módosítások

<!-- Feature 3906611 -->
- A felügyeleti portál infrastruktúra-szerepköri példányainak leállításához a Azure Stack operátorok beállítását eltávolították. Az újraindítási funkció biztosítja az infrastruktúra-szerepkör-példány újraindítása előtt a tiszta leállítási kísérletet. A speciális forgatókönyvek esetében az API és a PowerShell funkció továbbra is elérhető marad.

<!-- Feature ## 4199257 -->
- Új piactér-felügyeleti felülettel rendelkezik, amely külön képernyőket biztosít a Piactéri lemezképek és az erőforrás-szolgáltatók számára. Egyelőre az **erőforrás-szolgáltatók** ablak üres, de a későbbi kiadásokban az új Pásti szolgáltatási ajánlatok jelennek meg, és az **erőforrás-szolgáltatók** ablakban lesznek kezelve.

<!-- Feature ## 4199257 -->
- A frissítési élmény változásai a kezelő portálon. Az erőforrás-szolgáltató frissítéseinek új rácsa van. Az erőforrás-szolgáltatók frissítésének lehetősége még nem érhető el.

<!-- Task ## 3748423  -->
- A frissítés telepítési felületének változásai a kezelő portálon. Ahhoz, hogy Azure Stack operátorok megfelelően válaszoljanak a frissítési problémákra, a portál a méretezési egység állapotán alapuló, a **AzureStack** és az eredmények elemzésével automatikusan meghatározott javaslatokat is biztosít. Az eredmény alapján a kezelő értesíti az operátort, hogy elvégezze a következő két művelet valamelyikét:

  - A "Soft" figyelmeztető riasztás jelenik meg a portálon, amely beolvassa a következőt: "a legutóbbi frissítés figyelmet igényel. A Microsoft azt javasolja, hogy a szokásos munkaidő alatt nyisson meg egy szolgáltatási kérelmet. A frissítési folyamat részeként a rendszer elvégzi a Test-AzureStack, és a kimenet alapján a legmegfelelőbb riasztást eredményezi. Ebben az esetben Test-AzureStack átadott. "

  - A "Hard" kritikus riasztás jelenik meg a portálon, amely a következőt olvasta: "a legutóbbi frissítés sikertelen volt. A Microsoft a lehető leghamarabb javasolja a szolgáltatási kérelem megnyitását. A frissítési folyamat részeként a rendszer elvégzi a Test-AzureStack, és a kimenet alapján a legmegfelelőbb riasztást eredményezi. Ebben az esetben a Test-AzureStack is meghiúsult. "

- Frissítettük az Azure Linux Agent 2.2.38.0 verzióját. Ez a támogatás lehetővé teszi, hogy az ügyfelek konzisztens Linux-rendszerképeket tartsanak az Azure és a Azure Stack között.

- A frissítési naplók változásai a kezelő portálon. A sikeres frissítési naplók beolvasására irányuló kérések már nem érhetők el. Nem sikerült frissíteni a naplókat, mert ezek a diagnosztika számára elérhetők, továbbra is letölthetők.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavítva egy olyan problémát, amely során a syslog-konfigurációt nem tartották meg egy frissítési cikluson keresztül, így a syslog-ügyfél elveszíti a konfigurációját, és a syslog-üzenetek továbbítása nem történik meg. A rendszernapló konfigurációja most már megmarad.

- Kijavított egy olyan problémát a CRP-ban, amely letiltotta a virtuális gépek felszabadítását. Korábban, ha egy virtuális gép több nagyméretű felügyelt lemezt tartalmaz, előfordulhat, hogy a virtuális gép felszabadítása időtúllépési hiba miatt meghiúsult.

- Kijavítottuk a Windows Defender motorral kapcsolatos problémát, amely hatással van a méretezési egységhez való hozzáférésre.

- Kijavított egy felhasználói portált érintő problémát, amelyben a blob Storage-fiókok hozzáférési szabályzatának ablaka nem tölthető be.

- Kijavított egy problémát a rendszergazda és a felhasználói portálon is, amelyben a globális Azure Portalokkal kapcsolatos téves értesítések jelentek meg.

- Kijavított egy felhasználói portált érintő problémát, amelyben a **visszajelzés** csempére kattintva megnyithatja az üres böngésző fület.

- Kijavított egy olyan portált, amely egy virtuálisgép-példányhoz csatlakoztatott hálózati adapterhez kötött IP-konfiguráció statikus IP-címének módosítását eredményezte. a rendszer hibaüzenetet jelenít meg.

- Rögzített egy felhasználói portált érintő probléma, amelyben a **hálózati adapter csatlakoztatása** egy meglévő virtuális géphez a **hálózati** ablakon keresztül megtörtént, hogy a művelet hibaüzenettel meghiúsult.

- Kijavítva egy probléma, amelyben a Azure Stack nem támogatja több mint 4 hálózati adapter (NIC) csatolását egy virtuálisgép-példányhoz.

- Kijavított egy olyan portált, amelyben egy bejövő biztonsági szabály hozzáadása és a **szolgáltatás címkéjének** kiválasztása a forrásként történik, és több olyan lehetőség jelenik meg, amelyek nem érhetők el a Azure stack.

- Kijavítva azt a problémát, amelyben a hálózati biztonsági csoportok (NSG-EK) nem Azure Stack ugyanúgy működnek, mint a globális Azure.

- Kijavított egy problémát a piactér-kezelésben, amely elrejti az összes letöltött terméket, ha a regisztráció lejár vagy el lett távolítva.

- Kijavítva egy probléma, amelyben a **set-AzureRmVirtualNetworkGatewayConnection** parancs a PowerShellben meglévő virtuális hálózati átjáróhoz való csatlakoztatása sikertelen volt, mert a hibaüzenet **érvénytelen megosztott kulccsal van konfigurálva...**

- Kijavítva egy olyan problémát, amely miatt a hálózati erőforrás-szolgáltató (NRP) nem szinkronizálható a hálózati vezérlővel, így a rendszer duplikált erőforrásokat kér. Bizonyos esetekben ez a szülő erőforrás hibás állapotba való elhagyását eredményezte.

- Kijavítva egy probléma, amelyben egy olyan felhasználó, aki közreműködői szerepkört rendelt egy előfizetéshez, de explicit módon nem adta meg az olvasási engedélyeket, a rendszer hibát generált a következő üzenet olvasásakor:.. **. A (z) somelogonaccount@domain.com {GUID} azonosítójú ügyfél nem rendelkezik a művelet végrehajtásához szükséges engedéllyel...** amikor egy erőforrás módosítását kísérli meg menteni.

- Kijavított egy olyan problémát, amelyben a piactér-felügyeleti képernyő üres volt, ha az offline szindikált eszköz lemezképek feltöltésére lett használva, és ezek közül bármelyikből hiányzik az ikon URI-ja.

- Kijavítva egy olyan hibát, amely megakadályozta, hogy a rendszer nem tudta letölteni a piactér-felügyeletet.

### <a name="security-updates"></a>Biztonsági frissítések

Azure Stack ezen frissítése nem tartalmazza a Azure Stack futtató operációs rendszer biztonsági frissítéseit.

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues-1904.md)
- [Biztonsági frissítések](../release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](../release-notes-checklist.md)

> [!NOTE]
> Ügyeljen arra, hogy az [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) eszköz legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és új funkciókat biztosít az egyes Azure Stack frissítésekhez.

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1904 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1903-es Azure Stack legújabb gyorsjavítást telepíti a 1904-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1904-update"></a>Az 1904-es frissítés alkalmazása előtt

A Azure Stack 1904-es kiadását a 1903-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Az 1904-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

- [Azure Stack gyorsjavítás 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerekkel rendelkező ügyfelek az **elérhető frissítés** üzenet jelenik meg az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).
