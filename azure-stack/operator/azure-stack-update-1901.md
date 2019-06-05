---
title: Azure Stack 1901 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1901 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.openlocfilehash: afa011ca40ea0ca7cbd899f8d3f15fb0512a6811
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506336"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1901 csomag tartalmát. A frissítési fejlesztései, javításokat és új funkciók az Azure Stack ezen verziója tartalmazza. Ez a cikk is ebben a kiadásban az ismert problémákat, és tartalmaz egy hivatkozást a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat, és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

Látható [Azure Stack régebbi verzióinak kibocsátási megjegyzések a TechNet-katalógusban](http://aka.ms/azsarchivedrelnotes). Ezek a kibocsátási megjegyzések kizárólag tájékoztatási célt szolgálnak, és jelenti ezen verziók támogatása archivált. További segítségért forduljon a Microsoft ügyfél-támogatási szolgálathoz.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1901 frissítés buildszáma **1.1901.0.95** vagy **1.1901.0.99** 2019. február 26. után. Olvassa el a következő megjegyzést:

> [!IMPORTANT]  
> A Microsoft észlelte a hibát, amely hatással lehet a 1901 frissítése 1811 (1.1811.0.101) az ügyfelek és a probléma frissített 1901 csomagot: build 1.1901.0.99, 1.1901.0.95 frissítve lett. Ügyfeleink számára, akik már frissítve az 1.1901.0.95 nem kell további műveleteket végrehajtania.
>
> Csatlakoztatott ügyfelek, amelyek a 1811 automatikusan megjelenik az új 1901 (1.1901.0.99) csomagot a felügyeleti portálon, és telepítse, amikor készen áll. Leválasztott ügyfelek letöltése és importálása az új 1901 csomag ugyanilyen módon [az itt leírtak szerint](azure-stack-apply-updates.md).
>
> Mindkét verzióját 1901 rendelkező ügyfelek nem érinti, a következő teljes vagy a gyorsjavítás csomag telepítésekor.

### <a name="update-type"></a>Frissítés típusa

Az Azure Stack 1901 update build típusa **teljes**. További információt a frissítés build típusú, tekintse meg a [kezelheti a frissítéseket az Azure Stackben](azure-stack-updates.md) cikk.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Ne felejtse el telepíteni a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) az Azure Stack 1901 frissítése előtt 1811.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Az Azure Stack-gyorsjavítások

Ha már rendelkezik 1901 és nem telepítette a gyorsjavítások még akkor is [1902 közvetlenül telepítheti](azure-stack-update-1902.md), a 1901 gyorsjavítás telepítése nélkül.

- **1811**: Nincs elérhető aktuális gyorsjavítást.
- **1901**: [KB 4500636 – az Azure Stack gyorsjavítás 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Telepítse a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) esetében (ha vannak) 1811 1901 frissítése előtt. Ha már rendelkezik 1901, és még nem telepítette minden gyorsjavítást, telepítheti 1902, közvetlenül a 1901 gyorsjavítás telepítése nélkül.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Ha az Azure Stack felügyelt System Center Operations Manager (SCOM), mindenképpen a felügyeleti csomag a Microsoft Azure Stackhez készült verziójára való frissítéséhez 1.0.3.11 1901 alkalmazása előtt.

## <a name="new-features"></a>Új funkciók

A frissítés tartalmazza a következő új funkciókat és fejlesztéseket az Azure Stack:

- Felügyelt engedélyezése az Azure Stack-rendszerképek, hogy hozzon létre egy felügyelt rendszerképet objektumot egy általánosított virtuális gépen (mindkettő nem felügyelt és felügyelt), amely csak hozhat létre felügyelt lemezes virtuális gépek, a jövőben. További információkért lásd: [Azure Stack-Managed Disks](../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Hibajavítás – `Import-AzureRmContext` helyesen deszerializálni a mentett jogkivonatot.  
   * **AzureRm.Resources**  
         Hibajavítás – `Get-AzureRmResource` lekérdezés esethez insensitively erőforrástípus szerint.  
   * **Azure.Storage**  
         Összesítő AzureRm-modul mostantól tartalmazza a már közzétett verzió 4.5.0 támogató a **api-verzió 2017-07-29**.  
   * **AzureRm.Storage**  
         Összesítő AzureRm-modul mostantól tartalmazza a már közzétett verzió 5.0.4 támogató a **api-verzió 2017-10-01**.  
   * **AzureRm.Compute**  
         A hozzáadott egyszerű paraméterkészletek `New-AzureRmVM` és `New-AzureRmVmss`, `-Image` paraméter megadásával felhasználói lemezképek támogatja.  
   * **AzureRm.Insights**  
         Összesítő AzureRm-modul mostantól tartalmazza a már közzétett verzió 5.1.5 támogató a **api-verzió a 2018-01-01** metrikákhoz, metrikadefinícióinak erőforrástípusok.

- **AzureStack 1.7.1** Ez használhatatlanná tévő változást kiadás. A kompatibilitástörő változásokkal kapcsolatos részletekért tekintse meg a következőt: https://aka.ms/azspshmigration171.
   * **Azs.Backup.Admin modul**  
         Kompatibilitástörő változás: Tanúsítványalapú titkosítási mód biztonsági mentésének módosításai. A tartalomkulcsok támogatása elavult.  
   * **Azs.Fabric.Admin Module**  
         `Get-AzsInfrastructureVolume` elavult. Új parancsmag `Get-AzsVolume`.  
         `Get-AzsStorageSystem` elavult.  Új parancsmag `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` elavult. A `StorageSubSystem` objektum tartalmazza a kapacitás tulajdonság.  
   * **Azs.Compute.Admin Module**  
         Hibajavítás – `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: Hívó `ConvertTo-PlatformImageObject` csak az a sikerhez vezető utat.  
         BugFix - `Add-AzsVmExtension`, `Get-AzsVmExtension`: ConvertTo-VmExtensionObject hívása csak a sikerhez vezető utat.  
   * **Azs.Storage.Admin Module**  
         Hibajavítás – új tárolási kvótát használ alapértelmezett értékeket, ha nincs megadva.

A frissített modulok referenciája áttekintéséhez lásd: [Azure Stack modulhivatkozás](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Hibák kijavítva:

- Kijavítva a portálon azt mutatta egy házirendalapú VPN-átjárók, amelyeket nem támogat az Azure Stackben létrehozásának lehetőségét. Ez a beállítás a portálról el lett távolítva.

<!-- 16523695 - IS, ASDK -->
- Javítva lett egy probléma, amelyben a virtuális hálózat, a DNS-beállításainak frissítése után **használata az Azure Stack DNS** való **egyéni DNS**, a példányok nem lettek frissítve az új beállítással.

- <!-- 3235634 - IS, ASDK -->
  Javítva lett egy probléma milyen üzembe virtuális gépeket tartalmazó méretű egy **v2** utótag; például **Standard_A2_v2**, mint az utótag megadása kötelező **Standard_A2_v2** () kis v). Mivel a globális Azure-ban, most már használhatják **Standard_A2_V2** (nagybetűs V).

<!--  2795678 - IS, ASDK --> 
- Kijavítva, hogy figyelmeztető üzenettel zárult, amikor a portál segítségével hozhat létre virtuális gépeket (VM) egy prémium szintű virtuális gép mérete (DS, Ds_v2, FS, FSv2). A virtuális gép létrejött, a standard szintű storage-fiókban. Bár ez nem érintette funkcionálisan, iops-t, illetve számlázással, a figyelmeztetés megoldották a problémát.

<!-- 1264761 - IS ASDK -->  
- Kijavítva az a **egészségügyi vezérlő** összetevő, amely volt a következő riasztások létrehozásával. A riasztások sikerült biztonságosan figyelmen kívül hagyja:

    - #1 riasztás:
       - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
       - SÚLYOSSÁG: Figyelmeztetés
       - ÖSSZETEVŐ: Vezérlő állapota
       - LEÍRÁS: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

    - Riasztási #2:
       - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
       - SÚLYOSSÁG: Figyelmeztetés
       - ÖSSZETEVŐ: Vezérlő állapota
       - LEÍRÁS: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.


<!-- 3507629 - IS, ASDK --> 
- Kijavítva a Managed Disks kvóták értékének beállításakor [kvótatípusok számítási](azure-stack-quota-types.md#compute-quota-types) 0, egyenértékű, 2048 GiB alapértelmezett értéke. A nulla kvótaérték most tiszteletben tartását.

<!-- 2724873 - IS --> 
- Kijavítva a PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleUnitNode** kezelheti a skálázási egység, amelyben indítása és leállítása a skálázási egység az első kísérlet sikertelen lehet.

<!-- 2724961- IS ASDK --> 
- Javítva lett egy probléma, amely regisztrálva a **Microsoft.Insight** az előfizetés beállításait, az erőforrás-szolgáltató és a létrehozott Windows virtuális gép vendég operációs rendszer diagnosztikai engedélyezve van, de a virtuális gépek – áttekintés oldalra a processzorhasználat diagram találtak a mérőszámadatokat. Az adatok most már helyesen jelenik meg.

- Javítva lett egy probléma milyen fut a **Get-AzureStackLog** parancsmag futtatása után nem **Test-AzureStack** kiemelt végponthoz (EGP) ugyanabban a munkamenetben. Most már használhatja a azonos EGP-munkamenet, amelyben végre **Test-AzureStack**.

<!-- bug 3615401, IS -->
- Kijavítva a hiba automatikus biztonsági mentést, ahol a scheduler szolgáltatás volna belépni az állapot váratlanul letiltva. 

<!--2850083, IS ASDK -->
- Eltávolítja a **átjáró alaphelyzetbe állítása** az Azure Stack portálon, amely hibát okozott, ha a gombra kattintott a gombra. Ez a gomb függvény. az Azure Stackben, rendszerszintű Azure Stack rendelkezik dedikált Virtuálisgép-példány helyett egy több-bérlős átjáró minden bérlő VPN-átjáró, ezért el lett távolítva a félreértések elkerülése érdekében. 

<!-- 3209594, IS ASDK -->
- Eltávolítja a **érvényes biztonsági szabályokat** hivatkozásra a **hálózati tulajdonságok** panelen, ez a funkció nem támogatott az Azure Stackben. Jelenlegi kapcsolatot megadta a benyomást, hogy ez a funkció támogatott volt, de nem működik. Zavart megkönnyítéséért, eltávolítottuk a hivatkozás.

<!-- 3139614 | IS -->
- Frissítés az Azure Stack, az OEM alkalmazása után, amelyben kijavítva a **frissítés érhető el** értesítési nem szerepelt az Azure Stack rendszergazdai portálon.

## <a name="changes"></a>Módosítások

<!-- 3083238 IS -->
- Biztonsági fejlesztések az ezt a frissítést a címtár-szolgáltatási szerepkör biztonsági másolat méretének növekedését eredményezi. Frissítve a méretezési útmutató a külső tárhelyen találhatók, lásd: a [infrastruktúra backup – dokumentáció](azure-stack-backup-reference.md#storage-location-sizing). Ez a módosítás végrehajtásához mérete nagyobb adatforgalom miatt a biztonsági mentés hosszabb időt eredményez. Ez a módosítás hatással van az integrált rendszerekről. 

- A január a 2019-től kezdődően telepítheti a Kubernetes-fürtök az Active Directory összevonási szolgáltatásokban (AD FS) regisztrált, csatlakoztatott Azure Stack-bélyegzők (az internet-hozzáférésre szükség). Kövesse az utasításokat [Itt](azure-stack-solution-template-kubernetes-cluster-add.md) az új Kubernetes Piactéri elem letöltéséhez. Kövesse az utasításokat [Itt](../user/azure-stack-solution-template-kubernetes-adfs.md) Kubernetes-fürt üzembe helyezéséhez. Jegyezze fel az új paraméterei a célrendszer hozzáadása vagy az AD FS voltát jelző regisztrálva. Ha az AD FS, adja meg, amely tárolja a központi telepítéséhez szükséges tanúsítvány a Key Vault-paraméterek az új mezők érhetők el.

   Vegye figyelembe, hogy még az AD FS támogatja, a Kubernetes-fürtök telepítésének internet-hozzáférést igényel.

- Az Azure Stackhez frissítés vagy gyorsjavítás telepítése után új funkciók bevezetésre kerülő, amely egy vagy több identitást használó alkalmazások számára biztosítani kell az új engedélyeket kér. Ezek az engedélyek biztosítása a kezdőkönyvtár rendszergazdai jogosultságú hozzáférés szükséges, és így ezek nem lehet automatikusan végbemennek. Példa:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Nincs új veszi figyelembe a pontos megtervezése az Azure Stack kapacitását. A 1901 a frissítés most már rendelkezésre áll egy korlát a létrehozható virtuális gépek teljes száma.  Ez a korlátozás készült ideiglenes megoldás instabil elkerülése érdekében. A forrás, virtuális gépek magasabb adatpontértékek a stabilitás probléma javítása folyamatban van, de egy meghatározott ütemterv szervizelési még nem határozták meg. A 1901 a frissítés most már rendelkezésre áll egy kiszolgáló legfeljebb 60 virtuális gépek és a egy teljes megoldás korlátja 700 száma.  Például egy 8 Azure Stack virtuális gépek maximális lenne 480-as (8 * 60).  12 – 16 kiszolgálóhoz az Azure Stack megoldás a korlát 700 lehet. Ez a korlátozás létrehozása szem összes számítási kapacitás kihasználását például a rugalmasság tartalék és a CPU virtuális és fizikai arányt, az operátornak szeretne a blokkban karbantartása. További információkért tekintse meg a capacity planner új kiadása.  
Abban az esetben, ha a rendszer elérte a Virtuálisgép-méretezési csoport megadott korlátot, ezért az alábbi hibakódok kellene visszaadnia: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- A Compute API-verzió 2017-12-01 nőtt.

- Infrastruktúra biztonsági mentését mostantól csak tanúsítvány nyilvános kulcsával (. CER) biztonsági mentési adatok titkosításához. Szimmetrikus titkosítási kulcs támogatása 1901 kezdve elavult. Ha infrastruktúra biztonsági mentését 1901 frissítése előtt van konfigurálva, a titkosítási kulcsok érvényben marad. Legalább 2 további frissíti a visszamenőleges kompatibilitás támogatási frissíteni a biztonsági mentési beállításokat kell. További információkért lásd: [Azure Stack infrastruktúrájának biztonsági mentési ajánlott eljárások](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori biztonsági rések és besorolási

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


További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis cikkei [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Próbál telepíteni egy Azure Stack-frissítést, ha a frissítés állapota sikertelen lehet, hogy, és az állapotváltozáshoz **PreparationFailed**. A frissítés erőforrás-szolgáltató (URP) nem képes megfelelően viheti át a fájlokat a storage-tárolót egy belső infrastruktúrát megosztáshoz feldolgozási okozza. 1901 (1.1901.0.95) verziójával kezdődően is használhatja a probléma megoldásához kattintva **frissítés most** újra (nem **folytatása**). A URP ezután törli a fájlokat az előző próbálkozás, és a letöltés ismét elindul.

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), ha a **AzsInfraRoleSummary** vagy a **AzsPortalApiSummary** teszt sikertelen, a rendszer felszólítja a Futtatás  **Test-AzureStack** együtt a `-Repair` jelzőt.  Ha ezt a parancsot futtatta, a következő hibaüzenettel meghiúsul:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), megjelenik egy figyelmeztető üzenet az alaplapi felügyeleti vezérlő (BMC). Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások `Error - Template for FaultType UserAccounts.New is missing.` biztonságosan figyelmen kívül hagyhatja ezeket a riasztásokat. A riasztás automatikusan bezáródik a frissítés a telepítés befejezése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: [gyorsjavítások](#hotfixes), valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  

- Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

<!-- 2931230 - IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- 3557860 - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.

<!-- 1663805 - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként használja [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése. A probléma megkerüléséhez hozza létre újból a tárfiókot, a korábban használt azonos néven.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtual machine scale set létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzembe helyezéshez használt egy másik operációs rendszert, vagy egy Azure Resource Manager-sablonnal, adjon meg egy másik CentOS rendszerképet, amely szerint a marketplace-ről üzembe helyezés előtt lett letöltve a operátor.  

<!-- TBD - IS ASDK --> 
- Frissítse a 1901 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   - Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Válassza ki **erőforrás-szolgáltatók**, majd **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)** , és ellenőrizze, hogy **AzureStack-DiskRP-ügyfél** szerepel a listán.
   - Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

- Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem teszi lehetővé, hogy jelentkezzen be az SSH-kulcsok használata. Áthidaló megoldásként használja a Linux-bővítményt a Virtuálisgép-hozzáférés SSH-kulcsok megvalósításához a kiépítés után, vagy jelszóalapú hitelesítés használatára.

- A méretezési készlet nem távolítható el a **Virtual Machine Scale Sets** panelen. Áthidaló megoldásként válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.

### <a name="networking"></a>Hálózat  

<!-- 3239127 - IS, ASDK -->
- Az Azure Stack portálon Ha módosít egy statikus IP-címet az IP-konfiguráció a Virtuálisgép-példányhoz csatolt hálózati adapterhez kötött látni fogja egy figyelmeztető üzenet arról, hogy 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Biztonságosan figyelmen kívül hagyhatja ezt az üzenetet; az IP-cím megváltozik, akkor is, ha a Virtuálisgép-példány nem indítja újra.

<!-- 3632798 - IS, ASDK -->
- A portálon, ha egy bejövő biztonsági szabály felvétele, és válassza a **Szolgáltatáscímke** forrásaként, több lehetőség megjelennek a **forráscímke** listája, amelyek nem érhetők el az Azure Stackhez. Érvényes, az Azure Stack csak lehetőségek a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    A többi beállítást az Azure Stackben forrás nem támogatottak. Hasonlóképpen ha adjon hozzá egy kimenő biztonsági szabályt, és válassza **Szolgáltatáscímke** célhelyeként, ugyanezt a listát lehetőségei **forráscímke** jelenik meg. A csak érvényes beállítások ugyanazok, mint a **forráscímke**, amint az az előző listában.

- Hálózati biztonsági csoportok (NSG) nem működik az Azure Stackhez ugyanolyan módon globális Azure-ban. Az Azure-ban, több port adhatók meg egy NSG-szabály (a portal, PowerShell, használatával és a Resource Manager-sablonok). Az Azure Stackben azonban nem állíthatja be több portot egy NSG-szabályt a portálon keresztül. A probléma megkerüléséhez használja egy Resource Manager-sablon vagy a PowerShell segítségével ezek további szabályokat állíthat be.

<!-- 3203799 - IS, ASDK -->
- Az Azure Stack nem támogatja a több mint 4 hálózati adapter (NIC) való csatlakoztatás egy Virtuálisgép-példányok, a példány méretétől függetlenül.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A storage erőforrás-szolgáltatót regisztrálnia kell, az előfizetés az első Azure-függvény létrehozása előtt.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Rendszernapló

- A syslog-konfiguráció nincs megőrizve egy frissítés tartózkodnak okoz a syslog-ügyfél elveszíti a konfigurációját, és leállítja a továbbított syslog-üzeneteket. A probléma az Azure Stack minden verziójára vonatkozik, a syslog-ügyfél (1809) a nyilvánosan elérhető verzió óta. A probléma elkerüléséhez konfigurálja újra a syslog-ügyfél az Azure Stack-frissítés telepítését követően.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1901 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload). 

Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. További információkért lásd: [frissítések kezelése az Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
