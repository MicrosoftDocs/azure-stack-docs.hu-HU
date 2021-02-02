---
title: Azure Stack 1808 frissítés | Microsoft Docs
description: Ismerkedjen meg az Azure Stack integrált rendszerek 1808-es frissítésével kapcsolatos újdonságokkal, beleértve az ismert problémákat és a frissítés letöltésének helyét.
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
ms.date: 10/02/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 7d7ba1e7e3b6686002d24df2e286af81ddda5ce7
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248187"
---
# <a name="azure-stack-1808-update"></a>Azure Stack 1808 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1808-es frissítési csomag tartalmát ismerteti. A frissítési csomag a Azure Stack jelen verziójának javításait, javításait és ismert problémáit tartalmazza. Ez a cikk egy hivatkozást is tartalmaz, amellyel letöltheti a frissítést. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1808 frissítési Build száma **1.1808.0.97**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés a Azure Stack következő tökéletesítéseit tartalmazza.

<!--  2682594   | IS  --> 
- **Az összes Azure Stack környezet mostantól az egyezményes világidő (UTC) időzóna-formátumát használja.**  Az összes naplózási adat és kapcsolódó információ mostantól UTC formátumban jelenik meg. Ha egy korábbi verzióról frissít, amely nem az UTC használatával lett telepítve, a környezet az UTC használatára frissül. 

<!-- 2437250  | IS  ASDK --> 
- **A Managed Disks támogatottak.** Mostantól Azure Stack virtuális gépeken és virtuálisgép-méretezési csoportokban is használhatja a Managed Disks. További információ [: Azure Stack Managed Disks: különbségek és szempontok](../../user/azure-stack-managed-disk-considerations.md).

<!-- 2563799  | IS  ASDK --> 
- **Azure monitor**. Az Azure-beli Azure Monitorekhez hasonlóan a Azure Stack Azure Monitor is biztosít alapszintű infrastruktúra-metrikákat és-naplókat a legtöbb szolgáltatáshoz. További információ: [Azure monitor on Azure stack](../../user/azure-stack-metrics-azure-data.md).

<!-- 2487932| IS --> 
- **Készítse elő a bővítmény gazdagépét**. A bővítmény-gazdagép segítségével biztonságossá teheti Azure Stack a szükséges TCP/IP-portok számának csökkentésével. Az 1808-es frissítéssel előkészítheti a bővítmény-gazdagépre Azure Stack készen áll. További információ: [felkészülés a bővítmény-gazdagépre a Azure stack](../azure-stack-extension-host-prepare.md)számára.

<!-- IS --> 
- A **Virtual Machine Scale sets gyűjteményéhez tartozó elemek már beépítettek**. A virtuálisgép-méretezési csoport katalógusának eleme mostantól elérhetővé válik a felhasználói és rendszergazdai portálon anélkül, hogy le kellene töltenie.  Ha 1808-re frissít, akkor a frissítés befejezése után elérhetővé válik.  

<!-- IS, ASDK --> 
- **Virtuálisgép-méretezési csoport skálázása**. A portál használatával [méretezhető a virtuálisgép-méretezési csoport](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Egyéni IPSec/IKE-házirend-konfigurációk támogatása** a [Azure stack VPN-átjárók](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md)esetében.

<!-- | IS ASDK--> 
- **Kubernetes Marketplace-eleme**. Most már üzembe helyezhet Kubernetes-fürtöket a [Kubernetes Marketplace-elemmel](../azure-stack-solution-template-kubernetes-cluster-add.md). A felhasználók kiválaszthatják a Kubernetes elemet, és kitölthetnek néhány paramétert egy Kubernetes-fürt üzembe helyezéséhez Azure Stack. A sablonok célja, hogy egyszerűvé tegye a felhasználók számára, hogy néhány lépésben Kubernetes a fejlesztési/tesztelési célú telepítéseket.

<!-- | IS ASDK--> 
- **Blockchain-sablonok**. Most már végrehajthatja a [Ethereum Consortium üzemelő példányait](../../user/azure-stack-ethereum.md) Azure stackon. A [Azure Stack Gyorskonfigurálás sablonokban](https://github.com/Azure/AzureStack-QuickStart-Templates)három új sablon található. Lehetővé teszik a felhasználók számára, hogy egy többtagú konzorcium Ethereum hálózatot telepítsenek és konfiguráljanak minimális Azure-és Ethereum-ismerettel. A sablonok célja, hogy egyszerűvé tegye a felhasználók számára, hogy néhány lépésben Blockchain a fejlesztési/tesztelési célú telepítéseket.

<!-- | IS ASDK--> 
- **Az API Version profile 2017-03-09-profil frissítve lett a 2018-03-01-Hybrid verzióra**. Az API-profilok megadják az Azure erőforrás-szolgáltatót és az API-verziót az Azure REST-végpontokhoz. A profilokkal kapcsolatos további információkért lásd: [az API-verziók profiljainak kezelése Azure Stackban](../../user/azure-stack-version-profiles.md).

 ### <a name="fixed-issues"></a>Megoldott problémák
<!-- IS ASDK--> 
- Kijavítottuk a rendelkezésre állási csoport létrehozásával kapcsolatos problémát a portálon, amely a tartalék tartomány és az 1. frissítés tartományát eredményezte. 

<!-- IS ASDK --> 
- A virtuálisgép-méretezési csoportok méretezési beállításai mostantól elérhetők a portálon.  

<!-- 2494144- IS, ASDK --> 
- A virtuális gép méretének kiválasztásakor megjelenő, az F sorozatú virtuálisgép-méreteket megakadályozó probléma megoldódott. 

<!-- IS, ASDK --> 
- A teljesítmény fejlesztése a virtuális gépek létrehozásakor és a mögöttes tárolók optimalizált használatakor.

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó **különféle javítások** .


### <a name="changes"></a>Módosítások
<!-- 1697698  | IS, ASDK --> 
- A felhasználói portál irányítópultján elérhető gyors *útmutatók* az online Azure stack dokumentációjának megfelelő cikkeire mutatnak.

<!-- 2515955   | IS ,ASDK--> 
- *Minden szolgáltatás* a Azure stack rendszergazdai és felhasználói portálok *további szolgáltatásait* váltja fel. Mostantól az *összes szolgáltatást* használhatja Alternatív megoldásként, ha az Azure Portalon ugyanúgy navigál a Azure stack-portálon.

<!-- TBD | IS, ASDK --> 
- *+ Hozzon létre egy erőforrást* *a Azure stack* rendszergazdai és felhasználói portálon.  Mostantól használhatja az *+ erőforrás létrehozása* lehetőséget arra, hogy a Azure stack-portálon ugyanúgy navigáljon, mint az Azure-portálokon.  

<!--  TBD | IS, ASDK --> 
- A virtuálisgép-méretezési csoportok (VMSS-EK) a portálon keresztüli [létrehozásához](../azure-stack-compute-add-scalesets.md) a virtuális gépek *alapszintű* mérete megszűnik. Ezzel a mérettel rendelkező VMSS létrehozásához használja a PowerShellt vagy a sablont.  

### <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek

Ez a frissítés a következő frissítéseket telepíti:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4343887](https://support.microsoft.com/help/4343887)-es cikkében találja.

Ez a frissítés a [Microsoft biztonsági tanácsadó ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018)című cikkben ismertetett, a spekulatív végrehajtó oldali csatorna biztonsági RÉSÉNEK (L1TF) által okozott, a spekulációs modulra vonatkozó sebezhetőségét is tartalmazza.  

### <a name="prerequisites"></a>Előfeltételek

- Telepítse a Azure Stack 1807 frissítést a Azure Stack 1808 frissítésének alkalmazása előtt. 
- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Amikor a 1808-es frissítés után futtatja a [test-AzureStack](../azure-stack-diagnostic-test.md) , megjelenik egy figyelmeztető üzenet a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

<!-- 2468613 - IS --> 
- A frissítés telepítése során riasztások jelenhetnek meg a *hibatípushoz UserAccounts. New hiányzik.*  Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan lezárulnak a frissítés telepítése után.

<!-- 2489559 - IS --> 
- A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- Bizonyos esetekben, ha egy frissítésre figyelmet igényel, előfordulhat, hogy a vonatkozó riasztás nem hozható létre. A pontos állapot továbbra is megjelenik a portálon, és nem érinti.

### <a name="post-update-steps"></a>Frissítés utáni lépések
A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md). 
- [KB 4465859 Azure Stack gyorsjavítás Azure Stack gyorsjavítás 1.1808.2.104](https://support.microsoft.com/help/4465859/)

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

- A Azure Stack műszaki dokumentáció a legújabb kiadásra koncentrál. A kiadások között a portálon megjelenő változások miatt a Azure Stack portálok használatakor megjelenő információk a dokumentációban láthatótól eltérőek lehetnek. 

<!-- TBD - IS ASDK --> 
- Előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához kattintson az **irányítópult szerkesztése** lehetőségre, majd kattintson a jobb gombbal, és válassza **a visszaállítás az alapértelmezett állapotba** lehetőséget.

<!-- 2930718 - IS ASDK --> 
- A felügyeleti portálon, amikor a felhasználói előfizetés részleteihez fér hozzá, a panel bezárása és a **legutóbbi** kattintás után a felhasználói előfizetés neve nem jelenik meg.

<!-- 3060156 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon a portál beállításaira, majd az **összes beállítás törlése és a privát irányítópultok** lehetőségre kattintva nem a várt módon működik. Hibaüzenet jelenik meg. 

<!-- 2930799 - IS ASDK --> 
- Az **összes szolgáltatásban** a rendszergazda és a felhasználói portálon is helytelenül jelenik meg a **DDoS Protection-csomagok** listája. Azure Stack nem érhető el ténylegesen. Ha megpróbálja létrehozni, a rendszer hibaüzenetet jelenít meg arról, hogy a portál nem tudja létrehozni a Piactéri elemeket. 

<!-- 2930820 - IS ASDK --> 
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el ténylegesen. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg. 

<!-- 2967387 IS, ASDK --> 
- A Azure Stack rendszergazdai vagy felhasználói portálra való bejelentkezéshez használt fiók **azonosítatlan felhasználóként** jelenik meg. Ez akkor fordul *elő, ha* a fiók nem rendelkezik a megadott *utónévkel* vagy névvel. A probléma megkerüléséhez szerkessze a felhasználói fiókot, és adja meg az utónév vagy az utónév nevét. Ezután ki kell jelentkeznie, majd újra be kell jelentkeznie a portálra. 

<!--  2873083 - IS ASDK --> 
-  Ha a portál használatával hoz létre virtuálisgép-méretezési készletet (VMSS), a *példány mérete* legördülő menü nem töltődik be megfelelően az Internet Explorer használatakor. A probléma megkerüléséhez használjon egy másik böngészőt, miközben a portál használatával VMSS hoz létre.  

<!-- 2931230 IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!--2760466 IS  ASDK --> 
- Ha olyan új Azure Stack környezetet telepít, amely ezen verzióját futtatja, előfordulhat, hogy az *aktiválást* jelző riasztás nem fog megjelenni. A Marketplace Syndication használata előtt [aktiválnia kell az aktiválást](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

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

<!-- 3099544 IS, ASDK --> 
- Amikor létrehoz egy új virtuális gépet (VM) a Azure Stack-portálon, és kiválasztja a virtuális gép méretét, az USD/hónap oszlop egy nem **elérhető** üzenettel jelenik meg. Ez az oszlop nem szerepelhet; a virtuális gép díjszabási oszlopának megjelenítése Azure Stackban nem támogatott.

<!-- 3090289 IS, ASDK --> 
- Az 1808-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

   1. Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a virtuális gép üzembe helyezése Managed Disks a belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
      1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Kattintson az **erőforrás-szolgáltatók** elemre, majd a **Microsoft. számítás** elemre, majd az **újbóli regisztrálás** elemre.
      2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy a **Azure stack felügyelt lemez** szerepel-e a listáján.
   2. Ha több-bérlős környezetet konfigurált, a virtuális gépek üzembe helyezése egy vendég címtárral társított előfizetésben meghiúsulhat, és belső hibaüzenet jelenhet meg. A hiba elhárításához kövesse az alábbi lépéseket:
      1. Alkalmazza a [1808 Azure stack gyorsjavítást](https://support.microsoft.com/help/4465859).
      2. A [cikk](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) lépéseit követve konfigurálja újra az egyes vendég-címtárakat.

<!-- 2869209 IS, ASDK --> 
- Az [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true)használatakor a **-OsUri** paramétert kell használnia a lemez FELTÖLTÉSÉHEZ használt Storage-fiók URI azonosítójaként. Ha a lemez helyi elérési útját használja, a parancsmag a következő hibával meghiúsul: a *hosszú ideig futó művelet sikertelen volt, mert az állapot meghiúsult*. 

<!--  2966665 IS, ASDK --> 
- SSD-adatlemezek csatlakoztatása a prémium szintű felügyelt lemezes virtuális gépekhez (DS, DSv2, FS, Fs_V2) hiba miatt meghiúsul:  *nem sikerült frissíteni a virtuális gép lemezeit a következő hiba miatt: a kért művelet nem hajtható végre, mert a Premium_LRS nem támogatott a virtuálisgép-méret Standard_DS/Ds_V2/fs/Fs_v2)*

   A probléma megkerüléséhez *Premium_LRS lemezek* helyett *Standard_LRS* adatlemezeket használjon. *Standard_LRS* adatlemezek használata nem változtatja meg a IOPs vagy a számlázási költségeket. 

<!--  2795678 IS, ASDK --> 
- Ha a portál használatával hoz létre virtuális gépeket (VM) a prémium szintű virtuálisgép-méretekben (DS, Ds_v2, FS, FSv2), a virtuális gép egy standard Storage-fiókban jön létre. A standard szintű Storage-fiókban való létrehozás nem befolyásolja a működést, a IOPs vagy a számlázást. 

   Nyugodtan figyelmen kívül hagyhatja az azt jelző figyelmeztetést: a *prémium szintű lemezeket támogató standard lemez használatát választotta. Ez hatással lehet az operációs rendszer teljesítményére, és nem ajánlott. Ehelyett használjon Premium Storage-t (SSD-t).*

<!-- 2967447 - IS, ASDK --> 
- A virtuálisgép-méretezési csoport (VMSS) létrehozása lehetővé teszi a CentOS-alapú 7,2 üzembe helyezés lehetőségét. Mivel ez a rendszerkép nem érhető el a Azure Stackon, válasszon másik operációs rendszert a központi telepítéshez, vagy használjon egy Azure Resource Manager sablont, amely egy másik CentOS-rendszerképet ad meg, amelyet a kezelő által a piactéren való üzembe helyezés előtt töltöttek le.  

<!-- 2724873 - IS --> 
- Ha a **Start-AzsScaleUnitNode** vagy a  **stop-AzsScaleUnitNode** PowerShell-parancsmagok használatával kezeli a méretezési egységeket, akkor a méretezési egység indításának vagy leállításának első kísérlete sikertelen lehet. Ha a parancsmag az első futtatáskor meghiúsul, futtassa a parancsmagot második alkalommal. A második futtatásnak sikeresnek kell lennie a művelet befejezéséhez. 

<!-- TBD - IS ASDK --> 
- Ha virtuális gépeket hoz létre a Azure Stack felhasználói portálon, a portálon helytelen számú adatlemez fog megjelenni, amelyek egy DS sorozatú virtuális géphez csatolhatók. A DS sorozatú virtuális gépek annyi adatlemezt tudnak fogadni, mint az Azure-konfiguráció.

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  

<!-- 2724961- IS ASDK --> 
- Ha regisztrálja a **Microsoft. Insight** erőforrás-szolgáltatót az előfizetési beállításokban, és létrehoz egy Windows rendszerű virtuális gépet, amelyen engedélyezve van a vendég operációs rendszer diagnosztika, a virtuális gép áttekintő oldalának CPU-százalékos diagramja nem fog tudni megjeleníteni metrikus adatokat.

   A virtuális gép CPU százalékos diagramjának megkereséséhez nyissa meg a **metrikák** panelt, és jelenítse meg az összes támogatott WINDOWSOS virtuális gép vendég metrikáját.



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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.

<!-- 2489178 - IS ASDK --> 
- Az infrastruktúra (a feldolgozók, a felügyelet, az előtéri szerepkörök) felskálázásához a PowerShellt kell használnia a számításhoz szükséges kibocsátási megjegyzésekben leírtak szerint.



### <a name="usage"></a>Használat  
<!-- TBD - IS ASDK --> 
- A használati nyilvános IP-cím használati mérőszáma a rekord létrehozásakor megjelenő *timedate* -bélyegző helyett ugyanazt a *EventDateTime* -értéket jeleníti meg az egyes rekordoknál. Jelenleg nem használhatja ezeket az adatokat a nyilvános IP-címek pontos nyilvántartásának elvégzéséhez.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése
A Azure Stack 1808 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)
  

## <a name="next-steps"></a>Következő lépések
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).  
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).  