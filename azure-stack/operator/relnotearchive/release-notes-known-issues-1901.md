---
title: Azure Stack 1901 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1901-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
ms.reviewer: adepue
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: a062fa6f2d4242f1831233f672e0a8dc3dff20f7
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186925"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1901-es frissítési csomag tartalmát ismerteti. A frissítés tartalmazza a Azure Stack ezen verziójának javításait, javításait és új funkcióit. Ez a cikk a kiadás ismert problémáit is ismerteti, és tartalmaz egy hivatkozást a frissítés letöltéséhez. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra vannak osztva, és a build (telepítés után) problémái vannak.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1901 Update Build száma **1.1901.0.95** vagy **1.1901.0.99** 2019 február 26. után. Tekintse meg a következő megjegyzést:

> [!IMPORTANT]  
> A Microsoft olyan problémát észlelt, amely hatással lehet az ügyfeleknek a 1811-es (1.1811.0.101) és a 1901-es verzióra való frissítésre, és frissített 1901 csomagot adott ki a probléma megoldásához: build 1.1901.0.99, frissítve a 1.1901.0.95-től. Azok az ügyfelek, akik már frissítettek a 1.1901.0.95, nem kell további műveleteket végezniük.
>
> Az 1811-on lévő csatlakoztatott ügyfelek automatikusan megtekinthetik a felügyeleti portálon elérhető új 1901 (1.1901.0.99) csomagot, és ha készen állnak, telepíteniük kell. A leválasztott ügyfelek az [itt ismertetett](../azure-stack-apply-updates.md)eljárással tölthetik le és importálják az új 1901-csomagot.
>
> A 1901-es verzióval rendelkező ügyfeleket a következő teljes vagy gyorsjavítási csomag telepítésekor nem érinti a rendszer.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1811-es [Azure stack legújabb gyorsjavítást](#azure-stack-hotfixes) telepíti a 1901-es Azure stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack gyorsjavítások

Ha már rendelkezik 1901-as verzióval, és még nem telepített gyorsjavításokat, a 1902-et közvetlenül is telepítheti, anélkül, hogy telepítené a 1901 gyorsjavítást.

- **1811**: nem érhető el aktuális gyorsjavítás.
- **1901**: [KB 4500636 – Azure stack gyorsjavítás 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A 1901-re való frissítés előtt telepítse a 1811-es (ha van) [legújabb Azure stack gyorsjavítását](#azure-stack-hotfixes) . Ha már rendelkezik 1901-as verzióval, és még nem telepített gyorsjavításokat, a 1902-et közvetlenül is telepítheti, anélkül, hogy telepítené a 1901 gyorsjavítást.

- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Ha a Azure Stack System Center Operations Manager (SCOM) felügyelete alatt áll, akkor a 1901-es verzió alkalmazása előtt ne felejtse el frissíteni a Microsoft Azure Stack verziójának felügyeleti csomagját.

## <a name="new-features"></a>Új funkciók

Ez a frissítés a Azure Stack alábbi új szolgáltatásait és tökéletesítéseit tartalmazza:

- A Azure Stack felügyelt lemezképek lehetővé teszik, hogy felügyelt lemezkép-objektumokat hozzon létre egy általánosított virtuális gépen (nem felügyelt és felügyelt), amely csak felügyelt lemezes virtuális gépeket hozhat létre. További információ: [Azure Stack Managed Disks](../../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm. profil**  
         Hibajavítás – `Import-AzureRmContext` helyesen deszerializálja a mentett tokent.  
   * **AzureRm. Resources**  
         Hibajavítás – `Get-AzureRmResource` Ha az erőforrás típusa nem különbözteti meg a kis-és nagybetűket.  
   * **Azure.Storage**  
         A AzureRm kumulatív modul mostantól tartalmazza a már közzétett verziót, amely támogatja az **2017-07-29-es API-verziót**.  
   * **AzureRm. Storage**  
         A AzureRm kumulatív modul mostantól tartalmazza a már közzétett verziót, amely támogatja az **2017-10-01-es API-verziót**.  
   * **AzureRm. számítás**  
         A (z) és a (z) egyszerű paramétereinek hozzáadásakor a `New-AzureRmVM` `New-AzureRmVmss` `-Image` paraméter támogatja a felhasználói lemezképek megadását.  
   * **AzureRm.-áttekintés**  
         A AzureRm kumulatív modul mostantól tartalmazza a már közzétett verziót, amely támogatja a 5.1.5 **API-version 2018-01-01-es verzióját** a metrikák, a metrikus definíciók erőforrástípus.

- **AzureStack 1.7.1** Ez egy Breaking Change kiadás. A kompatibilitástörő változásokkal kapcsolatos részletekért tekintse meg a következőt: https://aka.ms/azspshmigration171.
   * **AZS. backup. admin modul**  
         Kompatibilitástörő változás: Tanúsítványalapú titkosítási mód biztonsági mentésének módosításai. A tartalomkulcsok támogatása elavult.  
   * **AZS. Fabric. admin modul**  
         `Get-AzsInfrastructureVolume` elavult. Használja az új parancsmagot `Get-AzsVolume` .  
         `Get-AzsStorageSystem` elavult.  Használja az új parancsmagot `Get-AzsStorageSubSystem` .  
         `Get-AzsStoragePool` elavult. Az `StorageSubSystem` objektum tartalmazza a Capacity tulajdonságot.  
   * **Azs.Compute.Admin modul**  
         Hibajavítás – `Add-AzsPlatformImage` , `Get-AzsPlatformImage` : `ConvertTo-PlatformImageObject` csak a siker elérési útjában hívja meg a hívást.  
         Hibajavító- `Add-AzsVmExtension` , `Get-AzsVmExtension` : a ConvertTo-VmExtensionObject hívása csak a siker elérési útjában.  
   * **Azs.Storage.Admin modul**  
         Javított hiba – Az Új tárterületkvóta megadott értékek hiányában az alapértelmezett értékeket használja.

A frissített modulok referenciájának áttekintését lásd: [Azure stack modul referenciája](/powershell/azure/azure-stack/overview?preserve-view=true&view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Megoldott problémák

- Kijavított egy olyan problémát, amelyben a portálon olyan házirend-alapú VPN-átjárók hozhatók létre, amelyek Azure Stack nem támogatottak. Ez a beállítás el lett távolítva a portálról.

<!-- 16523695 - IS, ASDK -->
- Kijavítva egy olyan problémát, amely után a Virtual Network DNS-beállításait a **Azure stack DNS használata** az **Egyéni DNS**-be való frissítés után a példányok nem frissültek az új beállítással.

- <!-- 3235634 - IS, ASDK -->
  Kijavítva egy probléma, amelyben a virtuális gépek telepítése **v2** utótagot tartalmazó méretekkel történt. **Standard_A2_v2** például az utótagot **Standard_A2_v2ként** (kisbetűs v) kell megadnia. A globális Azure-hoz hasonlóan mostantól **Standard_A2_V2** (nagybetűs V) is használható.

<!--  2795678 - IS, ASDK --> 
- Kijavított egy problémát, amely figyelmeztetést generált, amikor a portálon virtuális gépeket hoz létre a prémium szintű virtuálisgép-méretekben (DS, Ds_v2, FS, FSv2). A virtuális gép egy standard Storage-fiókban lett létrehozva. Bár ez nem befolyásolta a funkciót, a IOPs vagy a számlázást, a figyelmeztetés megoldódott.

<!-- 1264761 - IS ASDK -->  
- Kijavított egy problémát az állapotfigyelő **vezérlő** összetevővel, amely a következő riasztásokat hozta létre. A riasztásokat nyugodtan figyelmen kívül hagyhatja:

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


<!-- 3507629 - IS, ASDK --> 
- Javítva lett a probléma, amikor a [számítási kvóta típusainál](../azure-stack-quota-types.md#compute-quota-types) Managed Disks kvóták értékét 0-ra értékre állítja, és ez egyenértékű az alapértelmezett 2048 GIB értékkel. A nulla kvóta értékének betartása most megtörténik.

<!-- 2724873 - IS --> 
- Kijavított egy problémát, ha a **Start-AzsScaleUnitNode** vagy a  **stop-AzsScaleUnitNode** PowerShell-parancsmagok használatával kezeli a méretezési egységeket, amelyekben a méretezési egység indításának vagy leállításának első kísérlete sikertelen lehet.

<!-- 2724961- IS ASDK --> 
- Kijavított egy problémát, amelyben regisztrálta a **Microsoft. Insight** erőforrás-szolgáltatót az előfizetési beállításokban, és létrehozott egy Windows rendszerű virtuális gépet, amelyen engedélyezve van a vendég operációs rendszer diagnosztika, de a virtuális gép áttekintő lapja nem jeleníti meg a metrikák adatait. Az adatértékek mostantól helyesen jelennek meg.

- A **Get-AzureStackLog** parancsmag futtatásával kapcsolatos probléma kijavítva, miután a **test-AzureStack** ugyanabban a PRIVILEGIZÁLT végpontban (PEP) fut. Most már használhatja ugyanazt a PEP-munkamenetet, amelyben a **test-AzureStack** végrehajtotta.

<!-- bug 3615401, IS -->
- Kijavítva az automatikus biztonsági mentések hibáját, ahol az ütemező szolgáltatás váratlanul letiltott állapotba kerül. 

<!--2850083, IS ASDK -->
- Az **átjáró alaphelyzetbe állítása** gomb el lett távolítva a Azure stack portálról, amely hibát jelzett, ha a gombra kattintott. Ez a gomb nem tartalmaz függvényt a Azure Stackban, mivel a Azure Stack több-bérlős átjáróval rendelkezik, nem pedig dedikált virtuálisgép-példányok az egyes bérlők VPN Gateway, ezért a rendszer eltávolította a félreértések elkerülése érdekében. 

<!-- 3209594, IS ASDK -->
- Az **érvényben lévő biztonsági szabályok** eltávolítása a **hálózati tulajdonságok** paneljéről, mivel ez a funkció Azure stack nem támogatott. A hivatkozás jelenléte azt a benyomást kelti, hogy ez a funkció támogatott, de nem működik. A félreértések enyhítése érdekében eltávolította a hivatkozást.

<!-- 3139614 | IS -->
- Kijavítva egy probléma, amely után a rendszer egy frissítést alkalmazott a Azure Stack egy SZÁMÍTÓGÉPGYÁRTÓtól, az **elérhető frissítés** értesítés nem jelenik meg a Azure stack felügyeleti portálon.

## <a name="changes"></a>Módosítások

<!-- 3083238 IS -->
- A frissítés biztonsági fejlesztései a címtárszolgáltatás szerepkör biztonsági mentési méretének növekedését eredményezik. A külső tárterületre vonatkozó frissített méretezési útmutatásért lásd az [infrastruktúra biztonsági mentési dokumentációja című témakört. /Azure-stack-Backup-Reference.MD # Storage – Location – sizing. Ez a változás hosszabb időt eredményez a biztonsági mentés elvégzéséhez a nagyobb méretű adatátvitel miatt. Ez a változás hatással van az integrált rendszerekre. 

- A január 2019-től kezdődően a Kubernetes-fürtöket Active Directory összevont szolgáltatások (AD FS) regisztrált, csatlakoztatott Azure Stack Stamp (Internet-hozzáférés szükséges) használatával telepítheti. Az új Kubernetes Marketplace-elemek letöltéséhez kövesse az [itt](../azure-stack-solution-template-kubernetes-cluster-add.md) található utasításokat. Kubernetes-fürt üzembe helyezéséhez kövesse az [itt](../../user/azure-stack-solution-template-kubernetes-adfs.md) található utasításokat. Jegyezze fel az új paramétereket, amelyek jelzik, hogy a célként megadott rendszer hozzáadva vagy AD FS regisztrálva van-e. Ha AD FS, a rendszer új mezőket is megadhat a központi telepítési tanúsítvány tárolási Key Vault paramétereinek megadásához.

   Vegye figyelembe, hogy még AD FS támogatással is a Kubernetes-fürtök üzembe helyezéséhez internet-hozzáférés szükséges.

- A frissítések vagy gyorsjavítások Azure Stackba való telepítése után új funkciók is megjelenhetnek, amelyekhez új engedélyek szükségesek egy vagy több identitás-alkalmazás számára. Ezen engedélyek megadása a kezdőkönyvtár rendszergazdai hozzáférését igényli, ezért nem végezhető el automatikusan. Például:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- A Azure Stack kapacitásának pontos megtervezése új szempont. Az 1901-es frissítéssel mostantól korlátozható a létrehozható Virtual Machines teljes száma.  Ez a korlát ideiglenes lehet a megoldás instabilitásának elkerülése érdekében. A stabilitási probléma forrása nagyobb számú virtuális gépen, de a szervizelés egy adott idővonala még nem lett meghatározva. Az 1901-es frissítéssel a 60-es virtuális gépek száma a maximálisan megengedett 700-os korláttal rendelkezik.  Például egy 8 kiszolgáló Azure Stack virtuális gép korlátja 480 (8 * 60).  Egy 12 – 16 kiszolgálói Azure Stack megoldás esetén a korlát 700. Ez a korlát azért jött létre, hogy az összes számítási kapacitással kapcsolatos szempontot figyelembe vegye, például a rugalmassági tartalékot és a CPU virtuális fizikai arányát, amelyet egy operátor a bélyegzőn szeretne fenntartani. További információ: a Capacity Planner új kiadása.  
Abban az esetben, ha elérte a virtuális gép méretezési korlátját, a rendszer a következő hibakódokat adja vissza eredményként: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- A számítási API-verzió 2017-12-01-ra nőtt.

- Az infrastruktúra biztonsági mentése csak nyilvános kulccsal rendelkező tanúsítványra van szükség (. CER) a biztonsági mentési adatmennyiség titkosításához. A szimmetrikus titkosítási kulcs támogatása a 1901-től kezdve elavult. Ha az infrastruktúra biztonsági mentése a 1901-es verzióra való frissítés előtt van konfigurálva, a titkosítási kulcsok érvényben maradnak. A biztonsági mentési beállítások frissítéséhez legalább 2 frissítést kell megfelelnie a visszamenőleges kompatibilitási támogatással. További információ: [Azure stack infrastruktúra biztonsági mentése – ajánlott eljárások](../azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek

Ez a frissítés a következő biztonsági frissítéseket telepíti:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4480977](https://support.microsoft.com/en-us/help/4480977)-es cikkeiben tekintheti meg.

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed** állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli a fájlokat az előző kísérletből, és újra elindítja a letöltést.

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor, ha a **AzsInfraRoleSummary** vagy a **AzsPortalApiSummary** teszt sikertelen, a rendszer felszólítja, hogy futtassa a **test-AzureStack** a `-Repair` jelzővel.  Ha ezt a parancsot futtatja, a következő hibaüzenet jelenik meg:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor a rendszer figyelmeztető üzenetet jelenít meg a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

- <!-- 2468613 - IS --> A frissítés telepítése során megjelenhetnek a riasztások a címmel, melyekkel `Error - Template for FaultType UserAccounts.New is missing.`  nyugodtan figyelmen kívül hagyhatják ezeket a riasztásokat. A riasztások automatikusan lezárulnak a frissítés telepítése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információ: [gyorsjavítások](#hotfixes)és [karbantartási szabályzat](../azure-stack-servicing-policy.md).  

- Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg. 

<!-- 2931230 - IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- 3557860 - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- 1663805 - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja [a PowerShellt az engedélyek ellenőrzéséhez](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját. A probléma megkerüléséhez hozza létre újra a Storage-fiókot ugyanazzal a névvel, mint amit korábban használt.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtuálisgép-méretezési csoport (VMSS) létrehozási élménye a CentOS-alapú 7,2-es verzióként szolgál a telepítéshez. Mivel ez a rendszerkép nem érhető el Azure Stackon, válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.  

<!-- TBD - IS ASDK --> 
- Az 1901-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

   - Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a Managed Disks-t tartalmazó virtuális gép üzembe helyezése belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
      1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Válassza az **erőforrás-szolgáltatók** lehetőséget, majd válassza a **Microsoft. számítás** lehetőséget, majd kattintson az **újbóli regisztrálás** gombra.
      2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy szerepel-e a **AzureStack-DiskRP-Client** .
   - Ha több-bérlős környezetet konfigurált, akkor előfordulhat, hogy a virtuális gépeknek a vendég címtárhoz társított előfizetésben való telepítése belső hibaüzenettel meghiúsul. A hiba elhárításához kövesse a [cikk](../azure-stack-enable-multitenancy.md#register-a-guest-directory) következő lépéseit az egyes vendég-címtárak újrakonfigurálásához.

- Az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. Áthidaló megoldásként használja a virtuális gép hozzáférését a Linux-bővítményhez az SSH-kulcsok üzembe helyezése után, vagy használjon jelszó alapú hitelesítést.

- Méretezési csoport nem távolítható el a **Virtual Machine Scale sets** panelről. Áthidaló megoldásként válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.

### <a name="networking"></a>Hálózatkezelés  

<!-- 3239127 - IS, ASDK -->
- Ha a Azure Stack-portálon olyan IP-konfigurációhoz tartozó statikus IP-címet módosít, amely egy virtuálisgép-példányhoz csatolt hálózati adapterhez van kötve, egy figyelmeztető üzenet jelenik meg, amely szerint az állapot 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet; a rendszer akkor is megváltoztatja az IP-címet, ha a virtuálisgép-példány nem indul újra.

<!-- 3632798 - IS, ASDK -->
- Ha a portálon egy bejövő biztonsági szabályt ad hozzá, és kijelöli a **szolgáltatás címkéje** forrásként lehetőséget, több lehetőség is megjelenik a **forrás címke** listában, amelyek nem érhetők el Azure stack. A Azure Stackban érvényes beállítások a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    A további beállítások nem támogatottak a Azure Stackban lévő forrásként szolgáló címkékként. Hasonlóképpen, ha hozzáad egy kimenő biztonsági szabályt, és kiválasztja a **szolgáltatás címkét** célként, a **forrás címkével** megegyező beállítások jelennek meg. Az egyetlen érvényes beállítás megegyezik a **forrás címkével**, az előző listában leírtak szerint.

- A hálózati biztonsági csoportok (NSG-EK) nem működnek a Azure Stack ugyanúgy, mint a globális Azure. Az Azure-ban több portot is beállíthat egy NSG-szabályhoz (a portál, a PowerShell és a Resource Manager-sablonok használatával). Azure Stack azonban nem állíthat be több portot egyetlen NSG-szabályon a portálon keresztül. A probléma megkerüléséhez a további szabályok megadásához használja a Resource Manager-sablont vagy a PowerShellt.

<!-- 3203799 - IS, ASDK -->
- A Azure Stack jelenleg nem támogatja több mint 4 hálózati adapter (NIC) csatolását egy virtuálisgép-példányhoz, a példány méretétől függetlenül.

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

A Azure Stack 1901 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload) 

Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. További információ: [Azure stack frissítéseinek kezelése](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).