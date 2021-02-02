---
title: Azure Stack 1802 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1802-es frissítéssel Azure Stack integrált rendszerek, az ismert problémák és a frissítés letöltésének helyétől.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: b9334a3079a767f723240946af8091508204b8ae
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248297"
---
# <a name="azure-stack-1802-update"></a>Azure Stack 1802 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1802-es frissítési csomag fejlesztéseit és javításait ismerteti, az ebben a kiadásban ismert problémákat, valamint a frissítés letöltésének helyét. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]        
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája    
A Azure Stack 1802 Update Build száma **20180302,1**.  


## <a name="before-you-begin"></a>Előkészületek    
> [!IMPORTANT]    
> A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).


### <a name="prerequisites"></a>Előfeltételek
- Telepítse a Azure Stack 1712 frissítést a Azure Stack 1802 frissítésének alkalmazása előtt.    

- Telepítse a **AzS gyorsjavítást 1.0.180312.1-Build 20180222,2** , mielőtt alkalmazza a Azure stack 1802 frissítést. Ez a gyorsjavítás frissíti a Windows Defendert, és elérhetővé válik a Azure Stack frissítéseinek letöltésekor.

  A gyorsjavítás telepítéséhez kövesse a [Azure stack frissítéseinek telepítésére](../azure-stack-apply-updates.md)vonatkozó szokásos eljárásokat. A frissítés neve **AzS gyorsjavítás 1.0.180312.1** jelenik meg, és a következő fájlokat tartalmazza: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1. bin
    - Metadata.xml

  Miután feltölti ezeket a fájlokat egy Storage-fiókba és-tárolóba, futtassa a telepítést a felügyeleti portál frissítés csempén. 
  
  A Azure Stack frissítéseitől eltérően a frissítés telepítése nem módosítja a Azure Stack verzióját.  A frissítés telepítésének megerősítéséhez tekintse meg a **telepített frissítések** listáját.
 


### <a name="post-update-steps"></a>Frissítés utáni lépések
A 1802-es telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md). 
- Azure Stack gyorsjavítások **1.0.180302.4**. [KB 4131152 – lehetséges, hogy a meglévő Virtual Machine Scale Sets használhatatlanná válik]( https://support.microsoft.com/help/4131152) 

  Ez a javítás a  [KB 4103348 – Network Controller API szolgáltatás összeomlásával kapcsolatos hibákat is elhárítja, amikor egy Azure stack frissítést próbál telepíteni](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Új funkciók és javítások
Ez a frissítés a Azure Stack következő javításait és javításait tartalmazza.

- **A támogatás a következő Azure Storage szolgáltatás API-verzióihoz lett hozzáadva**:
    - 2017. 4. 17. 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    További információt a [Azure stack Storage: különbségek és megfontolások](../../user/azure-stack-acs-differences.md)című témakörben talál.

- **Nagyobb [blokkos Blobok](../../user/azure-stack-acs-differences.md)támogatása**:
    - A maximálisan engedélyezett blokk mérete 4 MB-ról 100 MB-ra nő.
    - A blob maximális mérete 195 GB-ról 4,75 TB-ra nő.  

- Az **infrastruktúra biztonsági mentése** most megjelenik az erőforrás-szolgáltatók csempén, és a biztonsági mentésre vonatkozó riasztások engedélyezve vannak. A Infrastructure Backup szolgáltatással kapcsolatos további információkért lásd: [Azure stack biztonsági mentése és az adatok helyreállítása a Infrastructure Backup szolgáltatással](../azure-stack-backup-infrastructure-backup.md).

- **Frissítsen a *test-AzureStack* parancsmagra** a tároló diagnosztika javítása érdekében. További információ erről a parancsmagról: [Azure stack érvényesítése](../azure-stack-diagnostic-test.md).

- **Szerepköralapú Access Control (RBAC)** – mostantól használhatja az RBAC-t az univerzális felhasználói csoportok engedélyeinek delegálására, ha az Azure Stack a AD FS használatával van telepítve. További információ a RBAC: a [RBAC kezelése](../azure-stack-manage-permissions.md).

- **A támogatás több tartalék tartományhoz is hozzá van adva**.  További információ: Azure Stack magas rendelkezésre állása.

- A **fizikai memória verziófrissítésének támogatása** – mostantól kiterjesztheti Azure stack integrált rendszer memóriájának kapacitását a kezdeti telepítés után. További információ: [Azure stack fizikai memória kapacitásának kezelése](../azure-stack-manage-storage-physical-memory-capacity.md).

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó **különféle javítások** .

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák    
*Nincsenek ismert problémák az 1802-es frissítés telepítésével kapcsolatban.*


### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)
A Build **20180302,1** -es verziójának telepítés utáni ismert problémái a következők:

#### <a name="portal"></a>Portál
<!-- 2332636 - IS -->  
- Ha AD FSt használ a Azure Stack identitásrendszer számára, és a Azure Stack ezen verziójára frissíti, az alapértelmezett szolgáltatói előfizetés alapértelmezett tulajdonosa visszaáll a beépített **CloudAdmin** -felhasználóra.  
  Áthidaló megoldás: a probléma megoldásához a frissítés telepítése után használja a trigger Automation 3. lépését a [jogcím-szolgáltatói megbízhatóság konfigurálásához Azure stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) eljárásban az alapértelmezett szolgáltatói előfizetés tulajdonosának alaphelyzetbe állításához.   

- [Új támogatási kérést nyithat meg a legördülő menüből](../azure-stack-manage-portals.md#quick-access-to-help-and-support) a felügyeleti portálon, nem érhető el. Ehelyett használja a következő hivatkozást:     
    - Azure Stack integrált rendszerek esetén használja a következőt: https://aka.ms/newsupportrequest .

<!-- 2050709 --> 
- A felügyeleti portálon nem lehet szerkeszteni Blob service, Table service vagy Queue szolgáltatás tárolási metrikáit. Amikor a tárterületre lép, majd kiválasztja a blob-, tábla-vagy üzenetsor-szolgáltatás csempét, megnyílik egy új panel, amely megjeleníti az adott szolgáltatás metrikai diagramját. Ha ezt követően a metrikák diagram csempe tetején a Szerkesztés elemet választja, megnyílik a diagram szerkesztése panel, de nem jeleníti meg a metrikák szerkesztésének lehetőségeit.

- Előfordulhat, hogy a felügyeleti portálon nem lehet megtekinteni a számítási és a tárolási erőforrásokat. A probléma oka a frissítés telepítése során felmerülő hiba, amelynek hatására a frissítés helytelenül lesz jelentve. Ha ez a probléma jelentkezik, kérjen segítséget a Microsoft ügyfélszolgálati szolgálatának.

- Előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához jelölje ki a fogaskerék ikont a portál jobb felső sarkában, majd válassza az **alapértelmezett beállítások visszaállítása** lehetőséget.

- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja a PowerShellt az engedélyek ellenőrzéséhez.

- A felügyeleti portál irányítópultján a frissítés csempe nem tud adatokat megjeleníteni a frissítésekről. A probléma megoldásához kattintson a csempére a frissítéshez.

-   A felügyeleti portálon kritikus riasztás jelenhet meg a Microsoft. Update. admin összetevőhöz. A riasztás neve, leírása és szervizelése az összes megjelenítéssel:  
    - *HIBA – hiányzik a hibatípushoz ResourceProviderTimeout sablonja.*

    Ez a riasztás nyugodtan figyelmen kívül hagyható. 

<!-- 2253274 --> 
- A rendszergazdák és a felhasználói portálok esetében a vNet-alhálózatok beállítások panelje nem tölthető be. Áthidaló megoldásként a PowerShell és a [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) parancsmag használatával tekintheti meg és kezelheti ezeket az adatokat.

- A felügyeleti Portálon és a felhasználói portálon az Áttekintés panel nem töltődik be, amikor kiválasztja a régebbi API-verzióval létrehozott Storage-fiókok áttekintés paneljét (például: 2015-06-15). Ez magában foglalja a rendszer Storage-fiókokat, például a javítás és a frissítés során használt **updateadminaccount** . 

  Áthidaló megoldásként a PowerShell használatával futtassa a **Start-ResourceSynchronization.ps1** szkriptet a Storage-fiók adatainak elérésének visszaállításához. [A parancsfájl elérhető a githubról](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), és a szolgáltatás-rendszergazdai hitelesítő adatokkal kell futnia a privilegizált végponton. 

- A **Service Health** panel betöltése sikertelen. Ha a Service Health panelt a rendszergazda vagy a felhasználói portálon nyitja meg, Azure Stack hibaüzenetet jelenít meg, és nem tölti be az adatokat. Ez az elvárt működés. Bár kiválaszthatja és megnyithatja Service Health, ez a funkció még nem érhető el, de a Azure Stack egy későbbi verziójában lesz implementálva.


#### <a name="health-and-monitoring"></a>Állapot és monitorozás
<!-- 1264761 - IS ASDK -->  
- Előfordulhat, hogy az állapotfigyelő *vezérlő* összetevő riasztásai megjelennek a következő részletekkel:  

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

  Mindkét riasztás nyugodtan figyelmen kívül hagyható. Az idő múlásával automatikusan lezárul.  


#### <a name="marketplace"></a>Marketplace
- A felhasználók az előfizetés nélkül böngészhetik a teljes piactéren, és a felügyeleti elemeket, például a csomagokat és ajánlatokat láthatják. Ezek az elemek nem működőképesek a felhasználók számára.

#### <a name="compute"></a>Compute
- A virtuálisgép-méretezési csoportokra vonatkozó méretezési beállítások nem érhetők el a portálon. Megkerülő megoldásként használhatja a [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A PowerShell verziójának eltérései miatt a helyett a `-Name` paramétert kell használnia `-VMScaleSetName` .

<!-- 2290877  --> 
- Nem méretezhető fel olyan virtuálisgép-méretezési csoport (VMSS), amely a 1802-es verzió előtti Azure Stack használatakor lett létrehozva. Ennek oka a rendelkezésre állási csoportok virtuálisgép-méretezési csoportokkal való használatának támogatása. Ez a támogatás a 1802-es verzióval bővült.  Ha további példányok hozzáadását kísérli meg egy olyan VMSS skálázásához, amely a támogatás hozzáadása előtt lett létrehozva, akkor a művelet meghiúsul, ha az üzenet *kiépítési állapota meghiúsult*. 

  Ezt a problémát a 1803-es verzióban oldják fel. Az 1802-es verzióval kapcsolatos probléma megoldásához telepítse Azure Stack gyorsjavítási **1.0.180302.4**. További információért lásd [: KB 4131152: a meglévő Virtual Machine Scale sets használhatatlanná válhatnak](https://support.microsoft.com/help/4131152). 

- A Azure Stack csak a rögzített típusú virtuális merevlemezeket támogatja. A piactéren elérhető néhány lemezkép Azure Stack dinamikus virtuális merevlemezeket használ, de ezek el lettek távolítva. Egy virtuális gép (VM) átméretezése, amelyhez egy dinamikus lemez csatlakozik, a gép hibás állapotba kerül.

  A probléma megoldásához törölje a virtuális GÉPET a virtuális gép lemezének törlése nélkül, egy VHD-blobot egy Storage-fiókban. Ezután alakítsa át a VHD-t egy dinamikus lemezről egy rögzített lemezre, majd hozza létre újra a virtuális gépet.

- Amikor **új** számítási rendelkezésre állási készlettel hoz létre egy rendelkezésre állási készletet a portálon  >    >  , csak egy tartalék tartományt és 1. frissítési tartományt tartalmazó rendelkezésre állási csoport hozható létre. Megkerülő megoldásként új virtuális gép létrehozásakor hozza létre a rendelkezésre állási készletet a PowerShell, a parancssori felület vagy a portálon belül.

- Ha virtuális gépeket hoz létre a Azure Stack felhasználói portálon, a portál helytelen számú adatlemezt jelenít meg, amelyek egy D sorozatú virtuális géphez csatolhatók. Az összes támogatott D sorozatú virtuális gép annyi adatlemezt képes kezelni, mint az Azure-konfiguráció.

- Ha nem sikerül létrehozni egy virtuálisgép-lemezképet, a virtuálisgép-lemezképek számítási paneljén egy nem törölhető sikertelen elem is felvehető.

  Megkerülő megoldásként hozzon létre egy új virtuálisgép-rendszerképet egy, a Hyper-V használatával létrehozható dummy VHD-vel (új-VHD-Path C:\dummy.vhd-Fixed-SizeBytes 1 GB). A folyamatnak meg kell oldania a hibás elemek törlését megakadályozó problémát. Ezt követően 15 perccel a próbabábu rendszerképének létrehozása után sikeresen törölheti.

  Ezután próbálja meg újból letölteni a korábban meghiúsult virtuálisgép-rendszerképet.

-  Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  




#### <a name="networking"></a>Hálózatkezelés
- Miután létrehozta és hozzárendelte a virtuális gépet egy nyilvános IP-címhez, a virtuális gépet nem lehet az adott IP-címről hozzárendelni. A kapcsolat megszüntetve állapotba kerül, de a korábban hozzárendelt nyilvános IP-cím továbbra is az eredeti virtuális géphez lesz társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia a létrehozott új virtuális gépekhez.

  Ez akkor is előfordul, ha az IP-címet egy új virtuális géphez rendeli hozzá (általában VIP- *swap* néven). Az ezen az IP-címen keresztüli kapcsolódás minden jövőbeli kísérlete az eredetileg társított virtuális géphez való kapcsolódást eredményezi, nem pedig az újat.

- A belső terheléselosztás (ILB) nem kezeli megfelelően a MAC-címeket a háttérbeli virtuális gépekhez, így a ILB megszakadhat, amikor Linux-példányokat használ a Back-End hálózaton.  A ILB a Back-End hálózaton futó Windows-példányokkal jól működik.

-   Az IP-továbbítási funkció látható a portálon, azonban az IP-továbbítás engedélyezése nincs hatással. Ez a funkció még nem támogatott.

- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Miután létrehozta az első helyi hálózati átjáró-kapcsolódást, a rendszer letiltja a helyi hálózati átjáró erőforrásának egyazon IP-címmel történő létrehozását.

- A DNS-kiszolgáló *automatikus* beállításával létrehozott Virtual Network az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

- A Azure Stack a virtuális gép telepítése után nem támogatja további hálózati adapterek hozzáadását a virtuálisgép-példányokhoz. Ha a virtuális gépnek több hálózati adapterre van szüksége, akkor azokat a központi telepítési időpontban kell meghatározni.

<!-- 2096388 --> 
- A hálózati biztonsági csoportra vonatkozó szabályok frissítéséhez a felügyeleti portál nem használható. 

    Megkerülő megoldás App Service: Ha távoli asztalt kell a vezérlő példányaihoz, a hálózati biztonsági csoportokban lévő biztonsági szabályokat a PowerShell használatával kell módosítania.  A következő példa bemutatja, hogyan *engedélyezheti* és állíthatja vissza a konfigurációt a *Megtagadás* érdekében:  
    
    - *Lehetővé*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Tagadja*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL és MySQL
 - A továbblépés előtt tekintse át a fontos megjegyzést, [mielőtt megkezdené](#before-you-begin) a jelen kibocsátási megjegyzések megkezdését.
- Akár egy óráig is eltarthat, mielőtt a felhasználók új SQL-vagy MySQL-telepítésben hozhatnak létre adatbázisokat.

- Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.  

<!-- IS, ASDK --> 
- A speciális karakterek, beleértve a szóközöket és az időszakokat, nem támogatottak a **család** nevében, ha az SQL-és a MySQL erőforrás-szolgáltatóhoz hoz létre SKU-t.

> [!NOTE]  
> A Azure Stack 1802-re való frissítés után továbbra is használhatja a korábban telepített SQL-és MySQL-erőforrás-szolgáltatókat.  Javasoljuk, hogy frissítse az SQL-t és a MySQL-t, ha új kiadás válik elérhetővé. A Azure Stackhoz hasonlóan az SQL-és a MySQL-erőforrás-szolgáltatókon is alkalmazhatja a frissítéseket.  Ha például az 1710-es verziót használja, először alkalmazza a 1711-es, majd az 1712-es verziót, majd frissítsen a 1802-re.      
>   
> Az 1802-es frissítés telepítése nem befolyásolja az SQL vagy a MySQL erőforrás-szolgáltatók aktuális használatát a felhasználók számára.
> A használt erőforrás-szolgáltatók verziójától függetlenül az adatbázisaiban lévő felhasználói adatai nincsenek megérintve, és továbbra is elérhetők maradnak.    


#### <a name="app-service"></a>App Service
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.

- Az infrastruktúra (a feldolgozók, a felügyelet, az előtéri szerepkörök) felskálázásához a PowerShellt kell használnia a számításhoz szükséges kibocsátási megjegyzésekben leírtak szerint.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Azure Stack eszközök letöltése a GitHubról
- A következő hibaüzenet jelenik meg, ha az *meghívó-webkérés PowerShell-* parancsmagot használja a Azure stack-eszközök githubról való letöltéséhez:     
    -  *Meghívó-webkérés: a kérelem megszakadt: nem hozható létre SSL/TLS biztonságos csatorna.*     

  Ez a hiba azért fordul elő, mert a GitHub támogatja a Tlsv1 és a TLS 1.1 titkosítási szabvány (a PowerShell alapértelmezett értéke) elavult használatát. További információ: [gyenge titkosítási szabványokra vonatkozó eltávolítási nyilatkozat](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához adja hozzá a `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` parancsot a parancsfájl tetejéhez, hogy a PowerShell-konzol a TLS 1.2-es verzióját használja a GitHub-Tárházak letöltésekor.


## <a name="download-the-update"></a>A frissítés letöltése
A Azure Stack 1802 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)


## <a name="more-information"></a>További információ
A Microsoft a 1710-es frissítéssel telepített privilegizált végpont (PEP) használatával figyelheti és folytathatja a frissítéseket.

- Tekintse [meg a frissítések figyelése Azure stack az emelt szintű végpont dokumentációjának használatával](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Lásd még

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).