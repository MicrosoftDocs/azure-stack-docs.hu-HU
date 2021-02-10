---
title: Azure Stack 1809 frissítés | Microsoft Docs
description: Ismerkedjen meg az Azure Stack integrált rendszerek 1809-es frissítésével kapcsolatos újdonságokkal, beleértve az ismert problémákat és a frissítés letöltésének helyét.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1548ee295eddd2b386f637301abef208918c21ad
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009213"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1809-es frissítési csomag tartalmát ismerteti. A frissítési csomag a Azure Stack jelen verziójának javításait, javításait és ismert problémáit tartalmazza. Ez a cikk egy hivatkozást is tartalmaz, amellyel letöltheti a frissítést. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1809 frissítési Build száma **1.1809.0.90**.  

### <a name="new-features"></a>Új funkciók

Ez a frissítés a Azure Stack alábbi tökéletesítéseit tartalmazza:

- Ebben a kiadásban Azure Stack integrált rendszerek támogatják a 4-16 csomópontok konfigurációit. A [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) segítségével megtervezheti Azure stack kapacitását és konfigurációját.

- <!--  2712869   | IS  ASDK -->  **Azure stack syslog-ügyfél (általánosan elérhető)**  Ez az ügyfél lehetővé teszi a Azure Stack infrastruktúrához kapcsolódó naplózási, riasztási és biztonsági naplók továbbítását a syslog-kiszolgáló, illetve a biztonsági információk és az Event Management (SIEM) szoftvereken kívül, Azure Stack. A syslog-ügyfél mostantól támogatja azt a portot, amelyen a syslog-kiszolgáló figyel.

   Ebben a kiadásban a syslog-ügyfél általánosan elérhető, és éles környezetben is használható.

   További információ: [Azure stack syslog továbbítása](../azure-stack-integrate-security.md).

- Most már [áthelyezheti az Azure-beli regisztrációs erőforrást az](../azure-stack-registration.md#move-a-registration-resource) erőforráscsoportok között anélkül, hogy újra kellene regisztrálnia. A felhőalapú megoldások szolgáltatói (CSP-ket) is áthelyezhetik a regisztrációs erőforrást az előfizetések között, feltéve, hogy az új és a régi előfizetések is ugyanahhoz a CSP-partneri AZONOSÍTÓhoz vannak rendelve. Ez nem befolyásolja a meglévő ügyfél-bérlői leképezéseket. 

- Több IP-cím hozzárendelésének támogatása hálózati adapterek esetén.  További részletekért lásd: [több IP-cím társítása virtuális gépekhez a PowerShell használatával](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Megoldott problémák

<!-- TBD - IS ASDK -->
- A portálon a memória diagramja az ingyenes/felhasznált kapacitást mostantól pontosnak fogja jelenteni. Mostantól megbízhatóbban megjósolhatja, hogy hány virtuális gépet tud létrehozni.

<!-- TBD - IS ASDK --> 
- Kijavított egy problémát, amelyben virtuális gépeket hozott létre a Azure Stack felhasználói portálon, és a portálon helytelen számú adatlemez jelenik meg, amelyek egy DS sorozatú virtuális géphez csatolhatók. A DS sorozatú virtuális gépek annyi adatlemezt tudnak fogadni, mint az Azure-konfiguráció.

- A következő felügyelt lemezekkel kapcsolatos problémák a 1809-es verzióban vannak kijavítva, és a 1808 [Azure stack gyorsjavítási 1.1808.9.117](https://support.microsoft.com/help/4481066/)is kijavítva vannak: 

   <!--  2966665 IS, ASDK --> 
  - Kijavítva a probléma, amelyben az SSD-adatlemezeket a prémium szintű felügyelt lemezes virtuális gépekhez (DS, DSv2, FS, Fs_V2) csatolva hiba történt: nem sikerült  *frissíteni a virtuális gép Vmname hibáját: a kért művelet nem hajtható végre, mert a (z) Premium_LRS nem támogatott a virtuálisgép-méret Standard_DS/Ds_V2/fs/Fs_v2)*. 
   
  - Felügyelt lemezes virtuális gép létrehozása a **createOption** használatával: a **csatolás** a következő hibával meghiúsul: a *hosszú ideig futó művelet sikertelen volt, mert a hiba állapota sikertelen. További információ: "belső végrehajtási hiba történt."*
    ErrorCode: InternalExecutionError ErrorMessage: belső végrehajtási hiba történt.
   
    Ez a probléma már kijavítva lett.

- <!-- 2702741 -  IS, ASDK --> Kijavítottuk a problémát, hogy a dinamikus kiosztási módszer használatával üzembe helyezett nyilvános IP-címek nem garantáltak a Stop-Deallocate kiállítása után. Most már megmaradnak.

- <!-- 3078022 - IS, ASDK --> Ha a virtuális gép leállítása megszakadt, a 1808 előtt nem lehet újra kiosztani a 1808-es frissítés után.  Ez a probléma a 1809-es verzióban van kijavítva. Az ebben az állapotban lévő példányok, amelyek nem indíthatók el, 1809-ben indíthatók el ezzel a javítással. A javítás a probléma ismétlődését is megakadályozza.

### <a name="changes"></a>Módosítások

<!-- 2635202 - IS, ASDK -->
- Az infrastruktúra-biztonsági mentési szolgáltatás a [nyilvános infrastruktúra-hálózatról](https://docs.microsoft.com/azure/azure-stack/azure-stack-network) a [nyilvános VIP-hálózatra](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network)költözik. Az ügyfeleknek biztosítaniuk kell, hogy a szolgáltatás hozzáférhessen a biztonsági mentési tárhelyhez a nyilvános VIP-hálózatról.  

> [!IMPORTANT]  
> Ha olyan tűzfallal rendelkezik, amely nem engedélyezi a nyilvános VIP-hálózatról a fájlkiszolgálóra való csatlakozást, ez a változás azt eredményezi, hogy az infrastruktúra biztonsági mentései sikertelenek lesznek a "hiba 53 a hálózati elérési út nem található". Ez egy olyan feltörési változás, amely nem rendelkezik ésszerű megkerülő megoldással. Az ügyfelek visszajelzései alapján a Microsoft egy gyorsjavításban visszaállíthatja ezt a változást. Tekintse át a [frissítés utáni lépések szakaszt](#post-update-steps) a 1809-es rendelkezésre álló gyorsjavításokkal kapcsolatos további információkért. Ha a gyorsjavítás elérhetővé válik, ügyeljen arra, hogy csak a 1809-es frissítés után alkalmazza azt, ha a hálózati házirendek nem teszik lehetővé a nyilvános VIP-hálózat számára az infrastruktúra-erőforrások elérését. 1811-ben ez a változás minden rendszerre vonatkozik. Ha a gyorsjavítást a 1809-es verzióban telepítette, nincs szükség további műveletre.  

### <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek

Ez a frissítés a következő biztonsági frissítéseket telepíti:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4457131](https://support.microsoft.com/help/4457131) -es és [4462917](https://support.microsoft.com/help/4462917)-es cikkeiben tekintheti meg.

### <a name="prerequisites"></a>Előfeltételek

- Telepítse a 1808-es Azure Stack legújabb gyorsjavítást a-es kiadáshoz a 1809 alkalmazása előtt. További információ: [KB 4481066 – Azure stack gyorsjavítás Azure stack gyorsjavítások 1.1808.9.117](https://support.microsoft.com/help/4481066/). Míg a Microsoft a rendelkezésre álló legújabb gyorsjavítást javasolja, az 1809 telepítéséhez szükséges minimális verzió a 1.1808.5.110.

  > [!TIP]  
  > Fizessen elő a következő *erőforrásrekordok* vagy *Atom* -hírcsatornák Azure stack gyorsjavítások megtartásához:
  > - RRS https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss 
  > - Atom https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom


- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket.  

  ```powershell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Ha a Azure Stack System Center Operations Manager (SCOM) felügyelete alatt áll, akkor a 1809-es verzió alkalmazása előtt ne felejtse el frissíteni a Microsoft Azure Stack verziójának felügyeleti csomagját.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Amikor a 1809-es frissítés után futtatja a [test-AzureStack](../azure-stack-diagnostic-test.md) , megjelenik egy figyelmeztető üzenet a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

- <!-- 2468613 - IS --> A frissítés telepítése során előfordulhat, hogy a riasztások a *hibatípushoz UserAccounts. New* hiányzik.  Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan lezárulnak a frissítés telepítése után.

- <!-- 2489559 - IS --> A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Ha az OEM-ben Azure Stack frissítést alkalmazott, előfordulhat, hogy az **elérhető frissítés** nem jelenik meg a Azure stack felügyeleti portálon. A Microsoft Update telepítéséhez töltse le és importálja manuálisan az itt található útmutatások [alkalmazásával Azure stack](../azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Frissítés utáni lépések

> [!Important]  
> Szerezze be a Azure Stack üzembe helyezését azon a bővítmény-állomáson, amelyet a következő frissítési csomag engedélyez. Készítse elő a rendszerét az alábbi útmutatás alapján, és [készítse elő a Azure Stackhez készült bővítmény-gazdagépet](../azure-stack-extension-host-prepare.md).

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).  
- [KB 4481548 – Azure Stack gyorsjavítás Azure Stack gyorsjavítás 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

- A Azure Stack műszaki dokumentáció a legújabb kiadásra koncentrál. A kiadások között a portálon megjelenő változások miatt a Azure Stack portálok használatakor megjelenő információk a dokumentációban láthatótól eltérőek lehetnek.

<!-- 2930718 - IS ASDK --> 
- A felügyeleti portálon, amikor a felhasználói előfizetés részleteihez fér hozzá, a panel bezárása és a **legutóbbi** kattintás után a felhasználói előfizetés neve nem jelenik meg.

<!-- 3060156 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon a portál beállításaira, majd az **összes beállítás törlése és a privát irányítópultok** lehetőségre kattintva nem a várt módon működik. Hibaüzenet jelenik meg. 

<!-- 2930799 - IS ASDK --> 
- Az **összes szolgáltatásban** a rendszergazda és a felhasználói portálon is helytelenül jelenik meg a **DDoS Protection-csomagok** listája. Azure Stack nem érhető el. Ha megpróbálja létrehozni, a rendszer hibaüzenetet jelenít meg arról, hogy a portál nem tudja létrehozni a Piactéri elemeket. 

<!-- 2930820 - IS ASDK --> 
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg. 

<!-- 2967387 - IS, ASDK --> 
- A Azure Stack rendszergazdai vagy felhasználói portálra való bejelentkezéshez használt fiók **azonosítatlan felhasználóként** jelenik meg. Ez az üzenet akkor jelenik meg, ha a fiók nem rendelkezik *Utónév* vagy *vezetéknév* névvel. A probléma megkerüléséhez szerkessze a felhasználói fiókot, és adja meg az utónév vagy az utónév nevét. Ezután ki kell jelentkeznie, majd újra be kell jelentkeznie a portálra.  

<!--  2873083 - IS ASDK --> 
-  Ha a portál használatával hoz létre virtuálisgép-méretezési készletet (VMSS), a *példány mérete* legördülő menü nem töltődik be megfelelően az Internet Explorer használatakor. A probléma megkerüléséhez használjon egy másik böngészőt, miközben a portál használatával VMSS hoz létre.  

<!-- 2931230 - IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!--2760466 - IS  ASDK --> 
- Ha olyan új Azure Stack környezetet telepít, amely ezen verzióját futtatja, előfordulhat, hogy az *aktiválást* jelző riasztás nem fog megjelenni. A Marketplace Syndication használata előtt [aktiválnia kell az aktiválást](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- TBD - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- TBD - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja a PowerShellt az engedélyek ellenőrzéséhez.


### <a name="health-and-monitoring"></a>Állapot és monitorozás

<!-- TBD - IS -->
- Előfordulhat, hogy a következő riasztások többször is megjelennek, majd eltűnik a Azure Stack rendszeren:
   - *Az infrastruktúra-szerepkör példánya nem érhető el*
   - *A skálázásiegység-csomópont offline állapotban van*
   
  Futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsmagot az infrastruktúra-szerepkör példányai és a skálázási egység csomópontjai állapotának ellenőrzéséhez. Ha a [test-AzureStack](../azure-stack-diagnostic-test.md)nem észlel problémát, figyelmen kívül hagyhatjuk ezeket a riasztásokat. Ha problémát észlel, megkísérelheti elindítani az infrastruktúra szerepkör-példányát vagy a csomópontot a felügyeleti portál vagy a PowerShell használatával.

  Ez a probléma a legújabb [1809 gyorsjavítási kiadásban](https://support.microsoft.com/help/4481548/)van kijavítva, ezért telepítse ezt a gyorsjavítást, ha a problémát tapasztalja. 

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

<!-- 2368581 - IS, ASDK --> 
- Egy Azure Stack operátor, ha alacsony memória-riasztást kap, és a bérlői virtuális gépek nem tudnak üzembe helyezni **háló VM-létrehozási hiba** miatt, előfordulhat, hogy az Azure stack Stamp kívül esik a rendelkezésre álló memórián. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) a számítási feladatokhoz rendelkezésre álló kapacitás megismeréséhez.

### <a name="compute"></a>Compute

- [Dv2 sorozatú virtuális gép](../../user/azure-stack-vm-considerations.md#vm-sizes)létrehozásakor a D11-14V2 virtuális gépek lehetővé teszik, hogy 4, 8, 16 és 32 adatlemezt hozzon létre. A virtuális gép létrehozása ablaktábla azonban 8, 16, 32 és 64 adatlemezt jelenít meg.

<!-- 3235634 - IS, ASDK -->
- A **v2** utótagot tartalmazó méretű virtuális gépek üzembe helyezése; **Standard_A2_v2** például az utótagot **Standard_A2_v2ként** (kisbetűs v) adja meg. Ne használjon **Standard_A2_V2** (nagybetűs V). Ez a globális Azure-ban működik, és inkonzisztens a Azure Stack.

<!-- 3099544 - IS, ASDK --> 
- Amikor létrehoz egy új virtuális gépet (VM) a Azure Stack-portálon, és kiválasztja a virtuális gép méretét, az USD/hónap oszlop egy nem **elérhető** üzenettel jelenik meg. Ez az oszlop nem szerepelhet; a virtuális gép díjszabási oszlopának megjelenítése Azure Stackban nem támogatott.

<!-- 2869209 - IS, ASDK --> 
- Az [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true)használatakor a **-OsUri** paramétert kell használnia a lemez FELTÖLTÉSÉHEZ használt Storage-fiók URI azonosítójaként. Ha a lemez helyi elérési útját használja, a parancsmag a következő hibával meghiúsul: a *hosszú ideig futó művelet sikertelen volt, mert az állapot meghiúsult*. 

<!--  2795678 - IS, ASDK --> 
- Ha a portál használatával hoz létre virtuális gépeket (VM) a prémium szintű virtuálisgép-méretekben (DS, Ds_v2, FS, FSv2), a virtuális gép egy standard Storage-fiókban jön létre. A standard szintű Storage-fiókban való létrehozás nem befolyásolja a működést, a IOPs vagy a számlázást. 

   Nyugodtan figyelmen kívül hagyhatja az azt jelző figyelmeztetést: a *prémium szintű lemezeket támogató standard lemez használatát választotta. Ez hatással lehet az operációs rendszer teljesítményére, és nem ajánlott. Ehelyett használjon Premium Storage-t (SSD-t).*

<!-- 2967447 - IS, ASDK --> 
- A virtuálisgép-méretezési csoport (VMSS) létrehozási élménye a CentOS-alapú 7,2-es verzióként szolgál a telepítéshez. Mivel ez a rendszerkép nem érhető el a Azure Stackon, válasszon másik operációs rendszert a központi telepítéshez, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.  

<!-- 2724873 - IS --> 
- Ha a **Start-AzsScaleUnitNode** vagy a  **stop-AzsScaleUnitNode** PowerShell-parancsmagok használatával kezeli a méretezési egységeket, akkor a méretezési egység indításának vagy leállításának első kísérlete sikertelen lehet. Ha a parancsmag az első futtatáskor meghiúsul, futtassa a parancsmagot második alkalommal. A második futtatásnak sikeresnek kell lennie a művelet befejezéséhez. 

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  

<!-- 2724961- IS ASDK --> 
- Ha regisztrálja a **Microsoft. Insight** erőforrás-szolgáltatót az előfizetési beállításokban, és létrehoz egy Windows rendszerű virtuális gépet, amelyen engedélyezve van a vendég operációs rendszer diagnosztika, a virtuális gép áttekintő oldalának CPU százalékos diagramja nem jeleníti meg a metrikák

   A metrikai adatok, például a virtuális gép CPU-százalékos diagramjának megkereséséhez nyissa meg a metrikák ablakot, és jelenítse meg az összes támogatott Windowsos virtuális gép vendég metrikáját.

<!-- 3507629 - IS, ASDK --> 
- Managed Disks két új [számítási kvótát](../azure-stack-quota-types.md#compute-quota-types) hoz létre az üzembe helyezhető felügyelt lemezek maximális kapacitásának korlátozására. Alapértelmezés szerint a rendszer minden egyes felügyelt lemez típusú kvóta esetében 2048 GiB-t foglal le. Azonban a következő problémák merülhetnek fel:

   - Az 1808-es frissítés előtt létrehozott kvóták esetében a Managed Disks kvóta 0 értéket fog megjeleníteni a felügyeleti portálon, bár a 2048 GiB le van foglalva. A tényleges igények alapján növelheti vagy csökkentheti az értéket, az újonnan beállított kvóta pedig felülbírálja a 2048 GiB alapértelmezett értékét.
   - Ha a kvóta értékét 0-ra módosítja, az az alapértelmezett 2048 GiB értékkel egyenlő. Áthidaló megoldásként állítsa a kvóta értékét 1-re.

<!-- TBD - IS ASDK --> 
- Az 1809-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

  - Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a Managed Disks-t tartalmazó virtuális gép üzembe helyezése belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
     1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Kattintson az **erőforrás-szolgáltatók** elemre, majd a **Microsoft. számítás** elemre, majd az **újbóli regisztrálás** elemre.
     2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy szerepel-e a **AzureStack-DiskRP-Client** szerepkör.
  - Ha több-bérlős környezetet konfigurált, akkor előfordulhat, hogy a virtuális gépeknek a vendég címtárhoz társított előfizetésben való telepítése belső hibaüzenettel meghiúsul. A hiba elhárításához kövesse a [cikk](../azure-stack-enable-multitenancy.md#register-a-guest-directory) következő lépéseit az egyes vendég-címtárak újrakonfigurálásához.

- Az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. Megkerülő megoldásként használja a virtuális gép hozzáférését a Linux-bővítményhez az SSH-kulcsok kiépítés után való megvalósításához, vagy használjon jelszó alapú hitelesítést.

### <a name="networking"></a>Hálózatkezelés  

<!-- 1766332 - IS ASDK --> 
- Ha VPN-kapcsolat beállításához a **létrehozás VPN Gateway** elemre kattint, a **hálózat** területen a **házirend-alapú** beállítás VPN-típusként jelenik meg. Ne jelölje be ezt a beállítást. Azure Stack csak az **útvonalon alapuló** beállítást támogatja.

<!-- 1902460 - IS ASDK --> 
- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Miután létrehozta az első helyi hálózati átjáró-kapcsolódást, a rendszer letiltja a helyi hálózati átjáró erőforrásának egyazon IP-címmel történő létrehozását.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló *automatikus* beállításával létrehozott Virtual Network az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

<!-- 2529607 - IS ASDK --> 
- Azure Stack *titkos kód elforgatása* során van olyan időszak, amelyben a nyilvános IP-címek két-öt percig nem érhető el.

<!-- 2664148 - IS ASDK --> 
-   Olyan esetekben, amikor a bérlő egy S2S VPN-alagút használatával éri el a virtuális gépeket, előfordulhat, hogy a kapcsolódási kísérletek sikertelenek lesznek, ha a helyszíni alhálózat hozzá lett adva a helyi hálózati átjáróhoz, miután az átjáró már létrejött. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.


### <a name="usage"></a>Használat  

<!-- TBD - IS ASDK --> 
- A nyilvános IP-cím használati mérőszáma a rekord létrehozásakor megjelenő *timedate* -bélyegző helyett ugyanazt a *EventDateTime* -értéket jeleníti meg az egyes rekordoknál. Jelenleg ezek az adatok nem használhatók a nyilvános IP-címek pontos nyilvántartásának elvégzéséhez.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1809 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)
  

## <a name="next-steps"></a>Következő lépések

- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).  
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).  