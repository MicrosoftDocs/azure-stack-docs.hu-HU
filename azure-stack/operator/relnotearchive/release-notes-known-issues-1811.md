---
title: Azure Stack 1811 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1811-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 3c852102b386cc0a625f628ed8c75319b112dfbd
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009264"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1811-es frissítési csomag tartalmát ismerteti. A frissítési csomag a Azure Stack ezen verziójának javításait, javításait és új funkcióit tartalmazza. Ez a cikk a kiadás ismert problémáit is ismerteti, és tartalmaz egy hivatkozást, amellyel letöltheti a frissítést. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra vannak osztva, és a build (telepítés után) problémái vannak.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1811 frissítési Build száma **1.1811.0.101**.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1809-es [Azure stack legújabb gyorsjavítást](#azure-stack-hotfixes) telepíti a 1811-es Azure stack frissítése előtt.

### <a name="azure-stack-hotfixes"></a>Azure Stack gyorsjavítások

- **1809**: [KB 4481548 – Azure stack gyorsjavítás 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: nem érhető el aktuális gyorsjavítás.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az 1811-es frissítés telepítése során győződjön meg arról, hogy a felügyeleti portál összes példánya le van zárva. A felhasználói portál nyitva maradhat, de a felügyeleti portált be kell zárni.

- A Azure Stack üzembe helyezése készen áll a Azure Stack Extension-gazdagépre. Készítse elő a rendszerét a következő útmutató segítségével: [felkészülés a bővítmény-gazdagépre a Azure stack számára](../azure-stack-extension-host-prepare.md). 
 
- Telepítse a 1809-es [Azure stack legújabb gyorsjavítást](#azure-stack-hotfixes) a 1811-es frissítés előtt.

- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Ha nem teljesülnek a kiterjesztésű gazdagépre vonatkozó követelmények, a `Test-AzureStack` kimenet a következő üzenetet jeleníti meg: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- A Azure Stack 1811 frissítéshez szükséges, hogy megfelelően importálta a kötelező kiterjesztésű gazdagép-tanúsítványokat a Azure Stack-környezetbe. Az 1811-es frissítés telepítésének folytatásához importálnia kell a bővítmény gazdagépéhez szükséges SSL-tanúsítványokat. A tanúsítványok importálásához tekintse meg [ezt a szakaszt](../azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Ha figyelmen kívül hagyja az összes figyelmeztetést, és továbbra is az 1811-es frissítés telepítését választja, a frissítés körülbelül 1 órával meghiúsul, és a következő üzenet jelenik meg:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Ha megfelelően importálta a kötelező kiterjesztésű gazdagép tanúsítványait, folytassa a 1811 frissítést a felügyeleti portálról. Míg a Microsoft azt tanácsolja Azure Stack operátoroknak, hogy a frissítési folyamat során ütemezzen egy karbantartási időszakot, a hiányzó kiterjesztésű gazdagépek tanúsítványainak miatti meghibásodások nem befolyásolhatják a meglévő számítási feladatokat vagy szolgáltatásokat.  

    A frissítés telepítése során a Azure Stack felhasználói portál nem érhető el, amíg a bővítmény-gazdagép konfigurálva van. A bővítmény gazdagépének konfigurációja akár 5 órát is igénybe vehet. Ez idő alatt ellenőrizheti a frissítés állapotát, vagy folytathatja egy frissítés sikertelen telepítését az [Azure Stack rendszergazdai PowerShell vagy a kiemelt végpont](../azure-stack-monitor-update.md) használatával.

- Ha a Azure Stack System Center Operations Manager (SCOM) felügyelete alatt áll, akkor a 1811-es verzió alkalmazása előtt ne felejtse el frissíteni a Microsoft Azure Stack verziójának felügyeleti csomagját.

## <a name="new-features"></a>Új funkciók

Ez a frissítés a Azure Stack alábbi új szolgáltatásait és tökéletesítéseit tartalmazza:

- Ezzel a kiadással a [bővítmény gazdagépe](../azure-stack-extension-host-prepare.md) engedélyezve van. A bővítmény-gazdagép leegyszerűsíti a hálózati integrációt, és javítja Azure Stack biztonsági állapotát.

- Az eszközök hitelesítésének támogatása Active Directory összevont szolgáltatásokkal (AD FS), különösen az Azure CLI használata esetén. További információ: [az API-verziók profiljainak használata az Azure CLI-vel Azure stack](../../user/azure-stack-version-profiles-azurecli2.md)

- Az egyszerű szolgáltatások támogatása Active Directory összevont szolgáltatásokkal (AD FS) rendelkező ügyfél-titok használatával. További információ: [egyszerű szolgáltatásnév létrehozása ad FShoz](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Ez a kiadás a következő Azure Storage Service API-verziókhoz nyújt támogatást: **2017-07-29**, **2017-11-09**. A támogatás a következő Azure Storage erőforrás-szolgáltató API-verziókhoz is hozzá van adva: **2016-05-01**, **2016-12-01**, **2017-06-01** és **2017-10-01**. További információt a [Azure stack Storage: különbségek és megfontolások](../../user/azure-stack-acs-differences.md)című témakörben talál.

- Új emelt szintű végponti parancsokat adott hozzá az ADFS szolgáltatás alapelveinek frissítéséhez és eltávolításához. További információ: [egyszerű szolgáltatásnév létrehozása ad FShoz](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Új méretezési egység csomópont-műveletekkel bővült, amelyek lehetővé teszik egy Azure Stack operátor számára a méretezési egység csomópontjának elindítását, leállítását és leállítását. További információ: [skálázási egység csomópontjainak műveletei Azure Stackban](../azure-stack-node-actions.md).

- Új régió tulajdonságai panel lett hozzáadva, amely a környezet regisztrációs adatait jeleníti meg. Ezeket az információkat a felügyeleti portál alapértelmezett irányítópultjának **régió-felügyeleti** csempére kattintva, majd a **Tulajdonságok** menüpontban tekintheti meg.

- Új emelt szintű végponti parancs hozzáadva a BMC-hitelesítő adatoknak a felhasználónévvel és jelszóval való frissítéséhez, amely a fizikai gépekkel való kommunikációhoz használatos. További információ: [a alaplapi felügyeleti vezérlő \( bmc-beli hitelesítő adatainak frissítése](../azure-stack-rotate-secrets.md).

- A Súgó és támogatás ikon (kérdőjel) a rendszergazda és a felhasználói portálok jobb felső sarkában elérhetővé tette az Azure-útiterv elérését, hasonlóan ahhoz, ahogy az a Azure Portal.

- Továbbfejlesztett piactér-kezelési élmény a leválasztott felhasználók számára. A Piactéri elemek leválasztott környezetben való közzétételének folyamata egy lépésre egyszerűsített, és nem kell külön feltölteni a rendszerképet és a Piactéri csomagot. A feltöltött termék a piactér-kezelés panelen is látható lesz.

- Ez a kiadás csökkenti a titkos elforgatáshoz szükséges karbantartási időszakot azáltal, hogy felveszi a csak külső tanúsítványokat a [titkos elforgatás során Azure stack](../azure-stack-rotate-secrets.md).

- [Azure stack PowerShell](../azure-stack-powershell-install.md) frissült a 1.6.0 verzióra. A frissítés a Azure Stack új, Storage szolgáltatással kapcsolatos funkcióinak támogatását tartalmazza. További információkért tekintse meg a [PowerShell-galéria Azure stack felügyeleti modul 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0) vonatkozó kibocsátási megjegyzéseit a Azure stack PowerShell frissítésével vagy telepítésével kapcsolatos információkért lásd: [a PowerShell telepítése Azure Stackhoz](../azure-stack-powershell-install.md).

- A Managed Disks mostantól alapértelmezés szerint engedélyezve van, amikor virtuális gépeket hoz létre a Azure Stack portál használatával. A virtuális gépek létrehozásával kapcsolatos hibák elkerülése érdekében a Managed Disks szükséges további lépésekért tekintse meg az [ismert problémák](#known-issues-post-installation) című szakaszt.

- Ez a kiadás a Azure Stack operátor riasztás- **javítási** műveleteit mutatja be. Néhány riasztás a 1811-ben a riasztásban kiválasztható **javítás** gomb, amely a probléma megoldására szolgál. További információ: [az állapot és a riasztások figyelése Azure Stackban](../azure-stack-monitor-health.md).

- A Azure Stack frissítési élményének frissítése. A frissítés továbbfejlesztései a következők: 
  - Azok a lapok, amelyek a frissítési előzményekből osztották fel a frissítéseket, a folyamatban lévő frissítések és a befejezett frissítések nyomon követése érdekében.
  - Továbbfejlesztett állapot-vizualizációk az alapvető és az OEM-verziók új ikonjaival és elrendezésével, valamint a legutóbbi frissítés dátumával.
  - A kibocsátási megjegyzések oszlop hivatkozásának **megtekintése** a felhasználót közvetlenül az adott frissítéshez tartozó dokumentációra irányítja, nem pedig az általános frissítés lapot.
  - A **frissítési előzmények** lap az egyes frissítések futtatási idejének meghatározására, valamint a továbbfejlesztett szűrési funkciókra szolgál.  
  - Azure Stack csatlakoztatott méretezési egységek továbbra is automatikusan megkapják a rendelkezésre álló **frissítést** , amint elérhetővé válnak.
  - A nem csatlakoztatott méretezési egységek Azure Stack a korábbihoz hasonló módon importálhatók. 
  - A JSON-naplók a portálról való letöltésének folyamata nem változik. Azure Stack operátorok megtekinthetik a folyamat előrehaladását kifejező további lépéseket.

    További információ: [frissítések alkalmazása Azure Stackban](../azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Megoldott problémák

<!-- TBD - IS ASDK --> 
- Kijavítva egy olyan problémát, amelyben a nyilvános IP-cím használati mérési adatok ugyanazt a **EventDateTime** értéket mutatták az egyes rekordokhoz a rekord létrehozásakor megjelenő **timedate** helyett. Mostantól ezeket az adatokat is használhatja a nyilvános IP-címek használatának pontos elvégzéséhez.

<!-- 3099544 – IS, ASDK --> 
- Egy új virtuális gép (VM) a Azure Stack portál használatával történő létrehozásakor fellépett probléma. A virtuális gép méretének kiválasztásakor az **USD/hónap** oszlopban egy nem **elérhető** üzenet jelenik meg. Ez az oszlop már nem jelenik meg; a virtuális gép díjszabási oszlopának megjelenítése Azure Stackban nem támogatott.

<!-- 2930718 - IS ASDK --> 
- Kijavított egy olyan problémát, amelyben a felügyeleti portál, a felhasználói előfizetés részleteinek elérésekor, a panel bezárása és a **legutóbbi** kattintás után a felhasználói előfizetés neve nem jelenik meg. Ekkor megjelenik a felhasználói előfizetés neve.

<!-- 3060156 - IS ASDK --> 
- Kijavított egy problémát a rendszergazda és a felhasználói portálon: kattintson a portál beállításaira, és válassza az összes beállítás törlése lehetőséget, és a **privát irányítópultok** nem a várt módon működnek, és a rendszer hibaüzenetet jelenít meg. Ez a beállítás most már megfelelően működik.

<!-- 2930799 - IS ASDK --> 
- Kijavított egy problémát a rendszergazda és a felhasználói portálon is: a **minden szolgáltatás** területen helytelenül szerepel a **DDoS Protection-csomagok** listája. Azure Stack nem érhető el. A lista el lett távolítva.
 
<!--2760466 – IS  ASDK --> 
- Kijavított egy olyan problémát, amely új Azure Stack-környezet telepítésekor történt, amelyben az **aktiválást** jelző riasztás nem volt megjelenítve. Most már helyesen jelenik meg.

<!--1236441 – IS  ASDK --> 
- Kijavított egy problémát, amely megakadályozza, hogy a RBAC szabályzatokat alkalmazza egy felhasználói csoportra az ADFS használatakor.

<!--3463840 - IS, ASDK --> 
- A nyilvános VIP-hálózatról nem elérhető fájlkiszolgáló miatt sikertelenül működő infrastruktúra-biztonsági mentések hibája javítva. Ez a javítás áthelyezi az infrastruktúra-biztonsági mentési szolgáltatást a nyilvános infrastruktúra-hálózatra. Ha a 1809-es [Azure stack legújabb gyorsjavítást](#azure-stack-hotfixes) alkalmazta a probléma megoldására, akkor a 1811-es frissítés nem végez további módosításokat. 

<!-- 2967387 – IS, ASDK --> 
- Kijavítva egy olyan problémát, amelyben a Azure Stack rendszergazdai vagy felhasználói portálra való bejelentkezéshez használt fiók **azonosítatlan felhasználóként** jelenik meg. Ez az üzenet akkor jelenik meg, ha a fiók nem rendelkezik a megadott **Utónév** vagy **vezetéknév** névvel.   

<!--  2873083 - IS ASDK --> 
- Kijavítva egy olyan problémát, amelyben a portál használatával virtuálisgép-méretezési csoport (VMSS) hozható létre, ami miatt a **példány mérete** legördülő menü nem töltődik be megfelelően az Internet Explorer használatakor. Ez a böngésző már megfelelően működik.  

<!-- 3190553 - IS ASDK -->
- Javítva a probléma, amely miatt a zajos riasztások generálták, hogy az infrastruktúra szerepkör-példánya nem volt elérhető, vagy a skálázási egység csomópontja offline állapotban volt

<!-- 2724961 - IS ASDK -->
- A Fiexed olyan problémát jelent, amelyben a virtuális gép áttekintő lapja nem tudja megfelelően megjeleníteni a virtuálisgép-metrikai diagramot. 

## <a name="changes"></a>Módosítások

- A tervekben lévő kvóták megtekintésének és szerkesztésének új módja a 1811-as verzióban érhető el. További információ: [meglévő kvóta megtekintése](../azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- A frissítés biztonsági fejlesztései a címtárszolgáltatás szerepkör biztonsági mentési méretének növekedését eredményezik. A külső tárterületre vonatkozó frissített méretezési útmutatásért tekintse meg az [infrastruktúra biztonsági mentési dokumentációját](../azure-stack-backup-reference.md#storage-location-sizing). Ez a változás hosszabb időt eredményez a biztonsági mentés elvégzéséhez a nagyobb méretű adatátvitel miatt. Ez a változás hatással van az integrált rendszerekre. 

- A BitLocker helyreállítási kulcsok lekéréséhez használt meglévő PEP-parancsmagot a rendszer a 1811-as verzióban átnevezi a Get-AzsRecoveryKeys Get-AzsCsvsRecoveryKeys. A BitLocker helyreállítási kulcsok beolvasásával kapcsolatos további információkért tekintse [meg a kulcsok lekérésének utasításait](../azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek

Ez a frissítés a következő biztonsági frissítéseket telepíti:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4478877](https://support.microsoft.com/help/4478877)-es cikkeiben tekintheti meg.

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Ha a **Get-AzureStackLog** PowerShell-parancsmagot a **test-AzureStack** futtatása után futtatja ugyanazon a PRIVILEGIZÁLT végponton (PEP), a **Get-AzureStackLog** sikertelen lesz. A probléma megkerüléséhez zárjuk le azt a PEP-munkamenetet, amelyben a **test-AzureStack** végrehajtotta, majd nyisson meg egy új munkamenetet a **Get-AzureStackLog** futtatásához.

- Az 1811-es frissítés telepítése során győződjön meg arról, hogy a felügyeleti portál összes példánya le van zárva ebben az időszakban. A felhasználói portál nyitva maradhat, de a felügyeleti portált be kell zárni.

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor, ha a **AzsInfraRoleSummary** vagy a **AzsPortalApiSummary** teszt sikertelen, a rendszer felszólítja, hogy futtassa a **test-AzureStack** a `-Repair` jelzővel.  Ha ezt a parancsot futtatja, a következő hibaüzenet jelenik meg:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  ezt a problémát egy későbbi kiadásban rögzíti a rendszer.

- Az 1811-es frissítés telepítése során a Azure Stack a portál használata nem érhető el, amíg a bővítmény-gazdagép konfigurálva van. A bővítmény gazdagépének konfigurációja akár 5 órát is igénybe vehet. Ez idő alatt ellenőrizheti a frissítés állapotát, vagy folytathatja egy frissítés sikertelen telepítését az [Azure Stack rendszergazdai PowerShell vagy a kiemelt végpont](../azure-stack-monitor-update.md) használatával. 

- Az 1811-es frissítés telepítése során előfordulhat, hogy a felhasználói portál irányítópultja nem érhető el, és a testreszabások elvesznek. A frissítés befejeződése után visszaállíthatja az irányítópultot az alapértelmezett értékre a portál beállításainak megnyitásával és az **alapértelmezett beállítások visszaállítása** lehetőség választásával.

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor a rendszer figyelmeztető üzenetet jelenít meg a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

- <!-- 2468613 - IS --> A frissítés telepítése során megjelenhetnek a riasztások a címmel, melyekkel `Error – Template for FaultType UserAccounts.New is missing.`  nyugodtan figyelmen kívül hagyhatják ezeket a riasztásokat. A riasztások automatikusan lezárulnak a frissítés telepítése után.

- <!-- 3139614 | IS --> Ha az OEM-ben Azure Stack frissítést alkalmazott, előfordulhat, hogy az **elérhető frissítés** nem jelenik meg a Azure stack felügyeleti portálon. A Microsoft Update telepítéséhez töltse le és importálja manuálisan az itt található útmutatások [alkalmazásával Azure stack](../azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Frissítés utáni lépések

- A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információ: [gyorsjavítások](#hotfixes)és [karbantartási szabályzat](../azure-stack-servicing-policy.md).  

- Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg. 

<!-- 2931230 – IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- TBD - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- TBD - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja [a PowerShellt az engedélyek ellenőrzéséhez](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Állapot és monitorozás

<!-- 1264761 - IS ASDK -->  
- Előfordulhat, hogy az állapotfigyelő **vezérlő** összetevő riasztásai megjelennek a következő részletekkel:  

  - Riasztás #1:
     - NÉV: az infrastruktúra szerepköre nem megfelelő állapotú
     - SÚLYOSSÁG: figyelmeztetés
     - ÖSSZETEVŐ: állapotfigyelő vezérlő
     - Leírás: az állapotfigyelő vezérlő szívverésének képolvasója nem érhető el. Ez érintheti az állapotadatok jelentéseit és a metrikákat.  

  - Riasztás #2:
     - NÉV: az infrastruktúra szerepköre nem megfelelő állapotú
     - SÚLYOSSÁG: figyelmeztetés
     - ÖSSZETEVŐ: állapotfigyelő vezérlő
     - Leírás: az állapotfigyelő vezérlő hibás képolvasó nem érhető el. Ez érintheti az állapotadatok jelentéseit és a metrikákat.

    Mindkét riasztás nyugodtan figyelmen kívül hagyható. Az idő múlásával automatikusan lezárul.  

### <a name="compute"></a>Compute

- Új Windowsos virtuális gép (VM) létrehozásakor a **Beállítások** panelen a folytatáshoz ki kell választania egy nyilvános bejövő portot. 1811-ben ez a beállítás kötelező, de nincs hatása. Ennek az az oka, hogy a szolgáltatás a Azure Firewalltól függ, amely nincs implementálva Azure Stackban. A virtuális gépek létrehozásának folytatásához a **nem nyilvános bejövő portok** vagy a többi lehetőség közül választhat. A beállítás nem lesz hatással.

- Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját. A probléma megkerüléséhez hozza létre újra a Storage-fiókot ugyanazzal a névvel, mint amit korábban használt.

- [Dv2 sorozatú virtuális gép](../../user/azure-stack-vm-considerations.md#vm-sizes)létrehozásakor a D11-14V2 virtuális gépek lehetővé teszik, hogy 4, 8, 16 és 32 adatlemezt hozzon létre. A virtuális gép létrehozása ablaktábla azonban 8, 16, 32 és 64 adatlemezt jelenít meg.

- A Azure Stack használati rekordjai nem várt nagybetűket tartalmazhatnak. például:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   Ebben a példában az erőforráscsoport nevét kell **AndrewRG**. Nyugodtan figyelmen kívül hagyhatja ezt az inkonzisztenciát.

<!-- 3235634 – IS, ASDK -->
- A **v2** utótagot tartalmazó méretű virtuális gépek üzembe helyezése; a **Standard_A2_v2** például **Standard_A2_v2** (kisbetűs v) utótagot kell megadni. Ne használjon **Standard_A2_V2** (nagybetűs V). Ez a globális Azure-ban működik, és inkonzisztens a Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Az [ **Add-AzsPlatformImage** parancsmag](/powershell/module/azs.compute.admin/add-azsplatformimage)használatakor a **-OsUri** paramétert kell használnia a lemez FELTÖLTÉSÉHEZ használt Storage-fiók URI azonosítójaként. Ha a lemez helyi elérési útját használja, a parancsmag a következő hibával meghiúsul: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Ha a portál segítségével virtuális gépeket (VM) hoz létre a prémium szintű virtuálisgép-méretekben (DS, Ds_v2, FS, FSv2), a virtuális gép egy standard Storage-fiókban jön létre. A standard szintű Storage-fiókban való létrehozás nem befolyásolja a működést, a IOPs vagy a számlázást. Nyugodtan figyelmen kívül hagyhatja a figyelmeztetést, amely a következőket írja: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- A virtuálisgép-méretezési csoport (VMSS) létrehozási élménye a CentOS-alapú 7,2-es verzióként szolgál a telepítéshez. Mivel ez a rendszerkép nem érhető el Azure Stackon, válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.  

<!-- 2724873 - IS --> 
- Ha a **Start-AzsScaleUnitNode** vagy a  **stop-AzsScaleUnitNode** PowerShell-parancsmagok használatával kezeli a méretezési egységeket, akkor a méretezési egység indításának vagy leállításának első kísérlete sikertelen lehet. Ha a parancsmag az első futtatáskor meghiúsul, futtassa a parancsmagot második alkalommal. A második futtatásnak sikeresen végre kell hajtania a műveletet. 

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl hosszú időt vesz igénybe, a kiépítési időkorlátot a virtuális gép felszabadításának vagy törlésének megkísérlése helyett le kell állítani.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks két új [számítási kvótát](../azure-stack-quota-types.md#compute-quota-types) hoz létre az üzembe helyezhető felügyelt lemezek maximális kapacitásának korlátozására. Alapértelmezés szerint a rendszer minden egyes felügyelt lemez típusú kvóta esetében 2048 GiB-t foglal le. Azonban a következő problémák merülhetnek fel:

   - Az 1808-es frissítés előtt létrehozott kvóták esetében a Managed Disks kvóta 0 értéket fog megjeleníteni a felügyeleti portálon, bár a 2048 GiB le van foglalva. A tényleges igények alapján növelheti vagy csökkentheti az értéket, az újonnan beállított kvóta pedig felülbírálja a 2048 GiB alapértelmezett értékét.
   - Ha a kvóta értékét 0-ra módosítja, az az alapértelmezett 2048 GiB értékkel egyenlő. Áthidaló megoldásként állítsa a kvóta értékét 1-re.

<!-- TBD - IS ASDK --> 
- Az 1811-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

   - Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a Managed Disks-t tartalmazó virtuális gép üzembe helyezése belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
      1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Válassza az **erőforrás-szolgáltatók** lehetőséget, majd válassza a **Microsoft. számítás** lehetőséget, majd kattintson az **újbóli regisztrálás** gombra.
      2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy szerepel-e a **AzureStack-DiskRP-Client** szerepkör.
   - Ha több-bérlős környezetet konfigurált, akkor előfordulhat, hogy a virtuális gépeknek a vendég címtárhoz társított előfizetésben való telepítése belső hibaüzenettel meghiúsul. A hiba elhárításához kövesse a [cikk](../azure-stack-enable-multitenancy.md#register-a-guest-directory) következő lépéseit az egyes vendég-címtárak újrakonfigurálásához.

- Az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. Áthidaló megoldásként használja a virtuális gép hozzáférését a Linux-bővítményhez az SSH-kulcsok üzembe helyezése után, vagy használjon jelszó alapú hitelesítést.

### <a name="networking"></a>Hálózatkezelés  

<!-- 1766332 - IS ASDK --> 
- Ha VPN-kapcsolat beállításához a **létrehozás VPN Gateway** elemre kattint, a **hálózat** területen a **házirend-alapú** beállítás VPN-típusként jelenik meg. Ne jelölje be ezt a beállítást. Azure Stack csak az **útvonalon alapuló** beállítást támogatja.

<!-- 1902460 - IS ASDK --> 
- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Az első helyi hálózati átjáró-kapcsolódás létrehozása után a rendszer elutasítja a helyi hálózati átjáró erőforrásának ismételt létrehozását ugyanazzal az IP-címmel.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló **automatikus** beállításával létrehozott virtuális hálózatokon az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

<!-- 2529607 - IS ASDK --> 
- Azure Stack *titkos kód elforgatása* során egy olyan időszak, amelyben a nyilvános IP-címek nem érhetők el két-öt percen belül.

<!-- 2664148 - IS ASDK --> 
- Olyan esetekben, amikor a bérlő egy S2S VPN-alagút használatával fér hozzá a virtuális gépekhez, előfordulhat, hogy a kapcsolódási kísérletek sikertelenek lesznek, ha a helyszíni alhálózat hozzá lett adva a helyi hálózati átjáróhoz, miután az átjáró már létrejött. 

- Ha a Azure Stack-portálon olyan IP-konfigurációhoz tartozó statikus IP-címet módosít, amely egy virtuálisgép-példányhoz csatolt hálózati adapterhez van kötve, egy figyelmeztető üzenet jelenik meg, amely szerint az állapot 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet; a rendszer akkor is megváltoztatja az IP-címet, ha a virtuálisgép-példány nem indul újra.

- A portál **hálózati tulajdonságok** paneljén található az egyes hálózati adapterek **érvényes biztonsági szabályaira** mutató hivatkozás. Ha ezt a hivatkozást választja, megnyílik egy új panel, amely a hiba előfordulásának hibaüzenetét jeleníti meg, `Not Found.` mert Azure stack még nem támogatja az **érvényes biztonsági szabályokat**.

- Ha a portálon egy bejövő biztonsági szabályt ad hozzá, és kijelöli a **szolgáltatás címkéje** forrásként lehetőséget, több lehetőség is megjelenik a **forrás címke** listában, amelyek nem érhetők el Azure stack. A Azure Stackban érvényes beállítások a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    A további beállítások nem támogatottak a Azure Stackban lévő forrásként szolgáló címkékként. Hasonlóképpen, ha hozzáad egy kimenő biztonsági szabályt, és kiválasztja a **szolgáltatás címkét** célként, a **forrás címkével** megegyező beállítások jelennek meg. Az egyetlen érvényes beállítás megegyezik a **forrás címkével**, az előző listában leírtak szerint.

- A **New-AzureRmIpSecPolicy PowerShell-** parancsmag nem támogatja az **DHGroup24** beállítását a `DHGroup` paraméterhez.

- A hálózati biztonsági csoportok (NSG-EK) nem működnek a Azure Stack ugyanúgy, mint a globális Azure. Az Azure-ban több portot is beállíthat egy NSG-szabályhoz (a portál, a PowerShell és a Resource Manager-sablonok használatával). Azure Stack nem állíthat be több portot egyetlen NSG-szabályon a portálon keresztül. A probléma megkerüléséhez használjon Resource Manager-sablont a további szabályok megadásához.

### <a name="infrastructure-backup"></a>Infrastruktúra biztonsági mentése

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Az automatikus biztonsági mentések engedélyezése után az ütemező szolgáltatás váratlanul letiltott állapotba kerül. A biztonságimásolat-vezérlő szolgáltatás felismeri, hogy az automatikus biztonsági mentések le vannak tiltva, és figyelmeztetés jelenik meg a felügyeleti portálon. Ez a figyelmeztetés akkor várható, ha az automatikus biztonsági mentések le vannak tiltva. 
    - Ok: ezt a problémát a szolgáltatás olyan hibája okozza, amely a Scheduler konfigurációjának elvesztését eredményezi. Ez a hiba nem változtatja meg a tárolási helyet, a felhasználónevet, a jelszót vagy a titkosítási kulcsot.   
    - Szervizelés: a probléma megoldásához nyissa meg a biztonsági mentési vezérlő beállításai panelt a Infrastructure Backup erőforrás-szolgáltatóban, és válassza az **automatikus biztonsági mentések engedélyezése** lehetőséget. Ügyeljen arra, hogy a kívánt gyakoriságot és a megőrzési időt állítsa be.
    - Előfordulás: alacsony 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Regisztrálnia kell a Storage erőforrás-szolgáltatót, mielőtt létrehozza az első Azure-függvényt az előfizetésben.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Rendszernapló

- A syslog-konfiguráció nem marad meg egy frissítési cikluson keresztül, így a syslog-ügyfél elveszti a konfigurációját, és a syslog-üzenetek továbbítása leáll. Ez a probléma az Azure Stack összes verziójára érvényes a syslog-ügyfél (1809) óta. A probléma megkerüléséhez konfigurálja újra a syslog-ügyfelet egy Azure Stack frissítés alkalmazása után.

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1811 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload) 

Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. További információ: [Azure stack frissítéseinek kezelése](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Következő lépések

- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).  
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).  