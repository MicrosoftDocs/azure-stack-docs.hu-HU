---
title: Azure Stack 1807 frissítés | Microsoft Docs
description: Ismerkedjen meg az Azure Stack integrált rendszerek 1807-es frissítésével kapcsolatos újdonságokkal, beleértve az ismert problémákat és a frissítés letöltésének helyét.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 9dd3647f607dc743c6eff5907ae4a47fa097e764
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248197"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1807-es frissítési csomag tartalmát ismerteti. Ez a frissítés tartalmazza a Azure Stack ezen verziójának javításait, javításait és ismert problémáit, valamint a frissítés letöltésének helyét. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1807 frissítési Build száma **1.1807.0.76**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés a Azure Stack következő tökéletesítéseit tartalmazza.

<!-- 1658937 | ASDK, IS --> 
- A **biztonsági mentések elindítása előre definiált ütemezéssel** – a készülék Azure stack mostantól automatikusan kiválthatja az infrastruktúra biztonsági mentését az emberi beavatkozás megszüntetése érdekében. A Azure Stack automatikusan törli a külső megosztást a megadott megőrzési időtartamnál régebbi biztonsági másolatok esetében is. További információ: [Azure stack biztonsági mentésének engedélyezése a PowerShell](../azure-stack-backup-enable-backup-powershell.md)-lel.

<!-- 2496385 | ASDK, IS -->  
- **Adatátviteli idő hozzáadva a teljes biztonsági mentéshez.** További információ: [Azure stack biztonsági mentésének engedélyezése a PowerShell](../azure-stack-backup-enable-backup-powershell.md)-lel.

<!-- 1702130 | ASDK, IS -->  
- **A biztonsági mentés külső kapacitása mostantól a külső megosztás megfelelő kapacitását mutatja.** (Korábban ez a kód 10 GB volt.) További információ: [Azure stack biztonsági mentésének engedélyezése a PowerShell](../azure-stack-backup-enable-backup-powershell.md)-lel.

<!-- 2508488 |  IS   -->
- **Bővítse a kapacitást** [további méretezési egység csomópontjainak hozzáadásával](../azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **A Azure Resource Manager-sablonok mostantól támogatják a feltétel elemet** – egy feltétel használatával üzembe helyezhet egy erőforrást egy Azure Resource Manger-sablonban. A sablon úgy is megtervezhető, hogy egy adott feltétel alapján telepítsen egy erőforrást, például ha van egy paraméter értéke. További információ a sablon feltételként való használatáról: Azure Resource Manager-sablonok [feltételes üzembe helyezése](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) [című rész](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) az Azure dokumentációjában. 

   A sablonok használatával [több előfizetéshez vagy erőforráscsoporthoz is telepíthet erőforrásokat](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **A Microsoft. Network API-erőforrás támogatott verziójának támogatása frissítve lett** , és támogatja az API 2017-10-01-es verzióját a 2015-06-15-es verzióról Azure stack hálózati erőforrásokhoz.  Ebben a kiadásban nem szerepel a 2017-10-01 és 2015-06-15 közötti erőforrás-verziók támogatása.  A funkcionalitással kapcsolatos különbségek [Azure stack hálózatkezelési szempontokat](../../user/azure-stack-network-differences.md) ismertető témakörben talál.

<!-- 2272116 | IS, ASDK   -->  
- **Azure stack támogatja a fordított DNS-kereséseket a külsőleg megjelenő Azure stack infrastruktúra-végpontok számára** (a portál, a adminportal, a felügyelet és a adminmanagement esetében). Ez lehetővé teszi Azure Stack külső végpontok nevének feloldását IP-címről.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack mostantól támogatja a további hálózati adapterek hozzáadását egy meglévő virtuális géphez.**  Ez a funkció a portál, a PowerShell és a parancssori felület használatával érhető el. További információ: [hálózati adapterek hozzáadása vagy eltávolítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) az Azure-dokumentációban. 

<!-- 2222444 | IS, ASDK   -->  
- A **hálózati használat mérőszámai között a pontosság és a rugalmasság fejlesztése** is megtörtént.  A hálózati használat mérőszámai pontosabbak, és figyelembe veszik a felfüggesztett előfizetéseket, a leállás időtartamát és a versenyhelyzet feltételeit.

<!-- 2753080 | IS -->  
- **A rendelkezésre álló értesítés frissítése.** A csatlakoztatott Azure Stack üzemelő példányok mostantól rendszeresen ellenőrzi a biztonságos végpontot, és megállapítja, hogy elérhető-e frissítés a felhőhöz. Ez az értesítés a frissítés csempén jelenik meg, mivel az új frissítés manuális ellenőrzése és importálása után is megjelenik. További információ a [Azure stack frissítéseinek kezeléséről](../azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **A Azure stack syslog-ügyfél (előzetes verzió) funkcióinak fejlesztése**. Ez az ügyfél lehetővé teszi, hogy a Azure Stack infrastruktúrához kapcsolódó naplózási és naplók továbbítását a syslog-kiszolgáló vagy a biztonsági információ és az Event Management (SIEM) szoftveren kívülről Azure Stack. A syslog-ügyfél mostantól támogatja a egyszerű szöveges vagy TLS 1,2 titkosítású TCP protokollt, amely az utóbbi az alapértelmezett konfiguráció. A TLS-kapcsolatokat a kiszolgáló vagy a kölcsönös hitelesítés szolgáltatással is konfigurálhatja.

  Annak konfigurálásához, hogy a syslog-ügyfél hogyan kommunikál (például protokoll, titkosítás és hitelesítés) a syslog-kiszolgálóval, használja a *set-SyslogServer* parancsmagot. Ez a parancsmag az emelt szintű végpontról (PEP) érhető el.

  Ha az ügyféloldali tanúsítványt hozzá szeretné adni a syslog-ügyfél TLS 1,2 kölcsönös hitelesítéséhez, használja a Set-SyslogClient parancsmagot a PEP-ben.

  Ebben az előzetes verzióban sokkal nagyobb számú naplózást és riasztást tekinthet meg. 

  Mivel ez a szolgáltatás még előzetes verzióban érhető el, nem támaszkodik éles környezetekben.

  További információ: [Azure stack syslog továbbítása](../azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager tartalmazza a régió nevét.** Ebben a kiadásban a Azure Resource Manager beolvasott objektumok mostantól a régió neve attribútumot is tartalmazzák. Ha egy meglévő PowerShell-parancsfájl közvetlenül átadja az objektumot egy másik parancsmagnak, a parancsfájl hibát eredményezhet, és sikertelen lesz. Ez Azure Resource Manager megfelelő viselkedés, és megköveteli, hogy a hívó ügyfél kivonja a régió attribútumot. További információ a Azure Resource Managerről: [Azure Resource Manager dokumentáció](https://docs.microsoft.com/azure/azure-resource-manager/). Ellenőrizze az 8-10-es mdb->

<!-- TBD | IS, ASDK -->  
- **A delegált szolgáltatók funkcióinak módosításai.** A 1807-től kezdődően a delegált szolgáltatók modellje egyszerűbbé válik az Azure-viszonteladói modell és a delegált szolgáltatók jobb összehangolása érdekében, és nem hozhat létre más delegált szolgáltatókat, lényegében egyengeti a modellt, és egyetlen szinten elérhetővé teszi a delegált szolgáltatói funkciót. Az új modellre való áttérés és az előfizetések kezelésének engedélyezéséhez a felhasználó-előfizetések mostantól áthelyezhetők az új vagy meglévő delegált szolgáltatói előfizetések között, amelyek ugyanahhoz a címtár-bérlőhöz tartoznak. Az alapértelmezett szolgáltatói előfizetéshez tartozó felhasználói előfizetések is áthelyezhetők a delegált szolgáltatói előfizetésekre ugyanabban a címtár-bérlőben.  További információ: [ajánlatok delegálása Azure Stackban](../azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Továbbfejlesztett virtuálisgép-létrehozási idő** az Azure Marketplace-ről letöltött rendszerképekkel létrehozott virtuális gépekhez.

<!-- TBD | IS, ASDK -->  
- **Azure Stack Capacity Planner használhatóságának fejlesztése**. A Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) mostantól egyszerűsített megoldást kínál a S2D gyorsítótár és a S2D kapacitásának megadására a megoldási SKU-ket definiálva. A 1000 virtuális gép korlátja el lett távolítva.


### <a name="fixed-issues"></a>Megoldott problémák

<!-- TBD | ASDK, IS --> 
- A frissítési folyamat számos előrelépést tett, hogy megbízhatóbb legyen. Emellett a mögöttes infrastruktúrához is javítások történtek, ami a frissítés során a számítási feladatok lehetséges leállását is csökkentheti.

<!--2292271 | ASDK, IS --> 
- Javítottunk egy problémát, amelyben a módosított kvóta korlátozása nem volt érvényes a meglévő előfizetésekre. Ha egy olyan hálózati erőforrásra vonatkozó kvóta-korlátozást hoz létre, amely egy felhasználói előfizetéshez társított ajánlat és csomag része, akkor az új korlát a meglévő előfizetésekre, valamint az új előfizetésekre is vonatkozik.

<!-- 448955 | IS ASDK --> 
- Mostantól sikeresen lekérdezheti az UTC + N időzónában üzembe helyezett rendszerek tevékenység-naplóit.    

<!-- 2319627 |  ASDK, IS --> 
- A biztonsági mentési konfiguráció paramétereinek előzetes keresése (elérési út/Felhasználónév/jelszó/titkosítási kulcs) már nem állítja be a helytelen beállításokat a biztonsági mentési konfigurációra. (Korábban helytelen beállítások lettek beállítva a biztonsági mentéshez, és a biztonsági mentés sikertelen lesz, ha aktiválódik.)

<!-- 2215948 |  ASDK, IS -->
- A biztonsági mentési lista mostantól frissül, amikor manuálisan törli a biztonsági másolatot a külső megosztásról.

<!-- 2332636 | IS -->  
- A frissítés erre a verzióra már nem állítja alaphelyzetbe az alapértelmezett szolgáltatói előfizetés alapértelmezett tulajdonosát a beépített **CloudAdmin** -felhasználó számára a AD FS-vel való üzembe helyezéskor.

<!-- 2360715 |  ASDK, IS -->  
- Az adatközpont-integráció beállításakor már nem fér hozzá a AD FS metaadat-fájlhoz egy Azure-fájlmegosztás alapján. További információ: [AD FS integráció beállítása az összevonási metaadat-fájl biztosításával](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Kijavítottunk egy problémát, amely megakadályozza, hogy a felhasználók olyan meglévő nyilvános IP-címet rendelnek hozzá, amely korábban egy hálózati adapterhez lett rendelve, vagy Load Balancer egy új hálózati adapterhez vagy Load Balancerhoz.  

<!-- 2551834 - IS, ASDK --> 
- Ha a felügyeleti vagy a felhasználói portálon a Storage-fiókok *áttekintését* választja, az *alapvető* erőforrások panel mostantól helyesen jeleníti meg az összes szükséges információt. 

<!-- 2551834 - IS, ASDK --> 
- Ha a rendszergazdák vagy a felhasználói portálok esetében kiválasztja az egyik Storage-fiók *címkéit* , az információ mostantól helyesen jelenik meg.

<!-- TBD - IS ASDK --> 
- A Azure Stack ezen verziója kijavítja azt a problémát, amely megakadályozta az illesztőprogram-frissítések alkalmazását az OEM-bővítmények csomagjaiból.

<!-- 2055809- IS ASDK --> 
- Rögzítettünk egy problémát, amely megakadályozta, hogy a virtuális gép létrehozásakor a számítási panelről törölje a virtuális gépeket.  

<!--  2643962 IS ASDK -->  
- Az *alacsony memória-kapacitásra* vonatkozó riasztás többé nem jelenik meg megfelelően.

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó **különféle javítások** .


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek
A Azure Stack a Windows Server 2016 Server Core telepítéseit használja a kulcs-infrastruktúra üzemeltetéséhez. Ez a kiadás a következő Windows Server 2016-frissítéseket telepíti a Azure Stack infrastruktúra-kiszolgálóin: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4338814](https://support.microsoft.com/help/4338814)  -es és [4345418](https://support.microsoft.com/help/4345418)-es cikkeiben tekintheti meg.



## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

- Telepítse a Azure Stack 1805 frissítést a Azure Stack 1807 frissítésének alkalmazása előtt.  Nem volt 1806-es frissítés.  

- Telepítse az 1805-es verzióhoz elérhető legújabb frissítést vagy gyorsjavítást.  

- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

<!-- 2468613 - IS --> 
- A frissítés telepítése során riasztások jelenhetnek meg a *hibatípushoz UserAccounts. New hiányzik.*  Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan lezárulnak a frissítés telepítése után.

<!-- 2489559 - IS --> 
- A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- Bizonyos esetekben, ha egy frissítésre figyelmet igényel, előfordulhat, hogy a vonatkozó riasztás nem hozható létre. A pontos állapot továbbra is megjelenik a portálon, és nem érinti.

### <a name="post-update-steps"></a>Frissítés utáni lépések
A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md). 
- [KB 4464231 Azure Stack gyorsjavítás Azure Stack gyorsjavítás 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 IS --> A frissítés telepítése után megtekintheti a **sikertelen frissítések telepítésének továbbfejlesztett állapotát.** Ez tartalmazhat olyan korábbi frissítési telepítési hibákkal kapcsolatos információkat, amelyeket a rendszer a két új állapot kategóriájának megfelelően módosított. Az új állapot Kategóriák: *PreparationFailed* és *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

- A Azure Stack műszaki dokumentáció a legújabb kiadásra koncentrál. A kiadások között a portálon megjelenő változások miatt a Azure Stack portálok használatakor megjelenő információk a dokumentációban láthatótól eltérőek lehetnek. 

- [Új támogatási kérést nem lehet megnyitni a legördülő menüből](../azure-stack-manage-portals.md#quick-access-to-help-and-support) a felügyeleti portálon belül. Ehelyett Azure Stack integrált rendszerek esetében használja a következő hivatkozást: [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest) .

<!-- 2931230 IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!--2760466 IS  ASDK --> 
- Ha olyan új Azure Stack környezetet telepít, amely ezen verzióját futtatja, előfordulhat, hogy az *aktiválást* jelző riasztás nem fog megjelenni. A Marketplace Syndication használata előtt [aktiválnia kell az aktiválást](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- 2403291 - IS ASDK --> 
- Előfordulhat, hogy nem használja a vízszintes görgetősávot a rendszergazda és a felhasználói portál alján. Ha nem fér hozzá a vízszintes görgetősávhoz, a navigációs sáv segítségével navigáljon egy korábbi panelre a portálon, és válassza ki annak a panelnek a nevét, amelyet a portál bal felső sarkában talál.

<!-- TBD - IS --> 
- Előfordulhat, hogy a felügyeleti portálon nem lehet megtekinteni a számítási és a tárolási erőforrásokat. A probléma oka a frissítés telepítése során felmerülő hiba, amelynek hatására a frissítés helytelenül lesz jelentve. Ha ez a probléma jelentkezik, kérjen segítséget a Microsoft ügyfélszolgálati szolgálatának.

<!-- TBD - IS --> 
- Előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához jelölje ki a fogaskerék ikont a portál jobb felső sarkában, majd válassza az **alapértelmezett beállítások visszaállítása** lehetőséget.

<!-- TBD - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- TBD - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja a PowerShellt az engedélyek ellenőrzéséhez.


### <a name="health-and-monitoring"></a>Állapot és monitorozás
<!-- 1264761 - IS ASDK -->  
- Előfordulhat, hogy az állapotfigyelő **vezérlő** összetevő riasztásai megjelennek a következő részletekkel:  

   Riasztás #1:
   - NÉV: az infrastruktúra szerepköre nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: állapotfigyelő vezérlő
   - Leírás: az állapotfigyelő vezérlő szívverésének képolvasója nem érhető el. Ez érintheti az állapotadatok jelentéseit és a metrikákat.  

  Riasztás #2:
   - NÉV: az infrastruktúra szerepköre nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: állapotfigyelő vezérlő
   - Leírás: az állapotfigyelő vezérlő hibás képolvasó nem érhető el. Ez érintheti az állapotadatok jelentéseit és a metrikákat.

  Mindkét riasztás nyugodtan figyelmen kívül hagyható, és az idő múlásával automatikusan lezárul.  


<!-- 2812138 | IS --> 
- Előfordulhat, hogy a **tárolási** összetevőre vonatkozóan riasztás jelenik meg, amely a következő adatokat tartalmazza:

   - NAME: a Storage szolgáltatás belső kommunikációs hibája  
   - SÚLYOSSÁG: kritikus  
   - ÖSSZETEVŐ: tároló  
   - Leírás: a Storage szolgáltatás belső kommunikációs hibája a kérelmeknek a következő csomópontokra való küldésekor fordul elő.  

    A riasztás nyugodtan figyelmen kívül hagyható, de manuálisan le kell állítania a riasztást.

<!-- 2368581 - IS. ASDK --> 
- Egy Azure Stack operátor, ha alacsony memória-riasztást kap, és a bérlői virtuális gépek nem tudnak üzembe helyezni **háló VM-létrehozási hiba** miatt, előfordulhat, hogy az Azure stack Stamp kívül esik a rendelkezésre álló memórián. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) a számítási feladatokhoz rendelkezésre álló kapacitás megismeréséhez.


### <a name="compute"></a>Compute

<!-- 2724873 - IS --> 
- Ha a **Start-AzsScaleUnitNode** vagy a  **stop-AzsScaleUnitNode** PowerShell-parancsmagok használatával kezeli a méretezési egységeket, akkor a méretezési egység indításának vagy leállításának első kísérlete sikertelen lehet. Ha a parancsmag az első futtatáskor meghiúsul, futtassa a parancsmagot második alkalommal. A második futtatásnak sikeresnek kell lennie a művelet befejezéséhez. 

<!-- 2494144 - IS, ASDK --> 
- A virtuálisgép-telepítés virtuálisgép-méretének kiválasztásakor egyes F sorozatú virtuális gépek méretei nem láthatók a méret kiválasztásának részeként a virtuális gép létrehozásakor. A következő virtuálisgép-méretek nem jelennek meg a választóban: *F8s_v2*, *F16s_v2*, *F32s_v2* és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét egy virtuális gép üzembe helyezéséhez. Minden metódusban meg kell adnia a használni kívánt virtuálisgép-méretet.

  - **Azure Resource Manager sablon:** Ha sablont használ, állítsa a sablonban lévő *vmSize* a használni kívánt virtuálisgép-mérettel egyenlőre. A következő bejegyzés például a *F32s_v2* méretet használó virtuális gépek üzembe helyezésére használható:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** Használhatja az az [VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) parancsot, és megadhatja a virtuális gép méretét paraméterként, hasonlóan a következőhöz: `--size "Standard_F32s_v2"` .

  - **PowerShell:** A PowerShell használatával a [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) is használható a virtuális gép méretét megadó paraméterrel, amely a következőhöz hasonló: `-VMSize "Standard_F32s_v2"` .


<!-- TBD - IS ASDK --> 
- A virtuálisgép-méretezési csoportokra vonatkozó méretezési beállítások nem érhetők el a portálon. Megkerülő megoldásként használhatja a [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A PowerShell verziójának eltérései miatt a helyett a `-Name` paramétert kell használnia `-VMScaleSetName` .

<!-- TBD - IS --> 
- Amikor **új** számítási rendelkezésre állási készlettel hoz létre egy rendelkezésre állási készletet a portálon  >    >  , csak egy tartalék tartományt és 1. frissítési tartományt tartalmazó rendelkezésre állási csoport hozható létre. Megkerülő megoldásként új virtuális gép létrehozásakor hozza létre a rendelkezésre állási készletet a PowerShell, a parancssori felület vagy a portálon belül.

<!-- TBD - IS ASDK --> 
- Ha virtuális gépeket hoz létre a Azure Stack felhasználói portálon, a portálon helytelen számú adatlemez fog megjelenni, amelyek egy DS sorozatú virtuális géphez csatolhatók. A DS sorozatú virtuális gépek annyi adatlemezt tudnak fogadni, mint az Azure-konfiguráció.

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  

<!-- 2724961- IS ASDK --> 
- Ha regisztrálja a **Microsoft. Insight** erőforrás-szolgáltatót az előfizetési beállításokban, és létrehoz egy Windows rendszerű virtuális gépet, amelyen engedélyezve van a vendég operációs rendszer diagnosztika, a virtuális gép áttekintő lapja nem jeleníti meg a metriká 

   A metrikák adatai, például a virtuális gép CPU-százalékos diagramjának megjelenítéséhez nyissa meg a **metrikák** panelt, és jelenítse meg az összes támogatott WINDOWSOS virtuális gép vendég metrikáját.

### <a name="networking"></a>Hálózatkezelés  

<!-- 1766332 - IS ASDK --> 
- Ha VPN-kapcsolat beállításához a **létrehozás VPN Gateway** elemre kattint, a **hálózat** területen a **házirend-alapú** beállítás VPN-típusként jelenik meg. Ne jelölje be ezt a beállítást. Azure Stack csak az **útvonalon alapuló** beállítást támogatja.

<!-- 1902460 - IS ASDK --> 
- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Miután létrehozta az első helyi hálózati átjáró-kapcsolódást, a rendszer letiltja a helyi hálózati átjáró erőforrásának egyazon IP-címmel történő létrehozását.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló *automatikus* beállításával létrehozott Virtual Network az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

<!-- 2702741 -  IS ASDK --> 
- A dinamikus kiosztási módszer használatával üzembe helyezett nyilvános IP-címek nem garantáltak a Stop-Deallocate kiállítása után.

<!-- 2529607 - IS ASDK --> 
- Azure Stack *titkos kód elforgatása* során van olyan időszak, amelyben a nyilvános IP-címek két-öt percig nem érhető el.

<!-- 2664148 - IS ASDK --> 
- Olyan esetekben, amikor a bérlő egy S2S VPN-alagút használatával éri el a virtuális gépeket, előfordulhat, hogy a kapcsolódási kísérletek sikertelenek lesznek, ha a helyszíni alhálózat hozzá lett adva a helyi hálózati átjáróhoz, miután az átjáró már létrejött. 


### <a name="sql-and-mysql"></a>SQL és MySQL



<!-- No Number - IS, ASDK -->  
- A speciális karakterek, beleértve a szóközöket és az időszakokat, nem támogatottak a **család** nevében, ha az SQL-és a MySQL erőforrás-szolgáltatóhoz hoz létre SKU-t. 

<!-- TBD - IS --> 
- Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.  

<!-- TBD - IS -->
> [!NOTE]   
> A Azure Stack ezen verziójára való frissítés után továbbra is használhatja a korábban telepített SQL-és MySQL-erőforrás-szolgáltatókat.  Javasoljuk, hogy frissítse az SQL-t és a MySQL-t, ha új kiadás válik elérhetővé. A Azure Stackhoz hasonlóan az SQL-és a MySQL-erőforrás-szolgáltatókon is alkalmazhatja a frissítéseket. Ha például az 1804-es verziót használja, először alkalmazza a 1805-es verziót, majd frissítsen a 1807-ra.  
>  
> A frissítés telepítése nem befolyásolja az SQL vagy a MySQL erőforrás-szolgáltatók aktuális használatát a felhasználók számára. 
> A használt erőforrás-szolgáltatók verziójától függetlenül az adatbázisaiban lévő felhasználói adatai nincsenek megérintve, és továbbra is elérhetők maradnak.  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.

<!-- 2489178 - IS ASDK --> 
- Az infrastruktúra (a feldolgozók, a felügyelet, az előtéri szerepkörök) felskálázásához a PowerShellt kell használnia a számításhoz szükséges kibocsátási megjegyzésekben leírtak szerint.

<!-- TBD - IS ASDK --> 
- A App Service jelenleg csak az *alapértelmezett szolgáltatói előfizetésbe* helyezhető üzembe. 


### <a name="usage"></a>Használat  
<!-- TBD - IS ASDK --> 
- A használati nyilvános IP-cím használati mérőszáma a rekord létrehozásakor megjelenő *timedate* -bélyegző helyett ugyanazt a *EventDateTime* -értéket jeleníti meg az egyes rekordoknál. Jelenleg nem használhatja ezeket az adatokat a nyilvános IP-címek pontos nyilvántartásának elvégzéséhez.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése
A Azure Stack 1807 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)


## <a name="next-steps"></a>Következő lépések
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).  
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).  