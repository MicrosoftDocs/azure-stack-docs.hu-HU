---
title: Azure Stack 1803 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1803-es frissítéssel Azure Stack integrált rendszerek, az ismert problémák és a frissítés letöltésének helyétől.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248268"
---
# <a name="azure-stack-1803-update"></a>Azure Stack 1803 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1803-es frissítési csomag fejlesztéseit és javításait ismerteti, az ebben a kiadásban ismert problémákat, valamint a frissítés letöltésének helyét. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]        
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája    
A Azure Stack 1803 Update Build száma **20180329,1**.


## <a name="before-you-begin"></a>Előkészületek    
> [!IMPORTANT]    
> A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).


### <a name="prerequisites"></a>Előfeltételek
- Telepítse a Azure Stack 1802 frissítést a Azure Stack 1803 frissítésének alkalmazása előtt.   

- Telepítse a **AzS gyorsjavítást 1.0.180312.1-Build 20180222,2** , mielőtt alkalmazza a Azure stack 1803 frissítést. Ez a gyorsjavítás frissíti a Windows Defendert, és elérhetővé válik a Azure Stack frissítéseinek letöltésekor.

  A gyorsjavítás telepítéséhez kövesse a [Azure stack frissítéseinek telepítésére](../azure-stack-apply-updates.md)vonatkozó szokásos eljárásokat. A frissítés neve **AzS gyorsjavítás 1.0.180312.1** jelenik meg, és a következő fájlokat tartalmazza: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1. bin
    - Metadata.xml

  Miután feltölti ezeket a fájlokat egy Storage-fiókba és-tárolóba, futtassa a telepítést a felügyeleti portál frissítés csempén. 
  
  A Azure Stack frissítéseitől eltérően a frissítés telepítése nem módosítja a Azure Stack verzióját. A frissítés telepítésének megerősítéséhez tekintse meg a **telepített frissítések** listáját.



### <a name="new-features"></a>Új funkciók 
Ez a frissítés a Azure Stack következő javításait és javításait tartalmazza.

- **Azure stack titkok frissítése** – (fiókok és tanúsítványok). A titkok kezelésével kapcsolatos további információkért lásd: [titkok Elforgatása Azure Stackban](../azure-stack-rotate-secrets.md). 

<!-- 1914853 --> 
- **Automatikus átirányítás HTTPS** -re, ha HTTP-n keresztül éri el a rendszergazda és a felhasználói portálokat. Ez a fejlesztés a Azure Stack [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) kapcsolatos visszajelzések alapján történt. 

<!-- 2202621  --> 
- A **piactér eléréséhez** mostantól megnyithatja a Azure stack Marketplace-t a rendszergazda és a felhasználói portálon található [+ új](https://ms.portal.azure.com/#create/hub) lehetőség használatával ugyanúgy, ahogy az Azure Portalon is.
 
<!-- 2202621 --> 
- **Azure monitor** – a Azure stack hozzáadja a [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) a felügyeleti és felhasználói portálokhoz. Ide tartoznak a metrikák és a tevékenységek naplóihoz tartozó új felfedezők. Ha ezt a Azure Monitor külső hálózatokból szeretné elérni, a **13012** -es portot meg kell nyitni a tűzfal konfigurációjában. A Azure Stack által igényelt portokkal kapcsolatos további információkért lásd: [Azure stack Datacenter Integration – közzétételi végpontok](../azure-stack-integrate-endpoints.md).

   Ennek a változásnak a részeként a **További szolgáltatások** alatt a *naplók* mostantól *tevékenység naplóként* jelennek meg. A funkció már konzisztens a Azure Portal. 

<!-- 1664791 --> 
- **Ritka fájlok** – amikor új rendszerképet ad hozzá a Azure Stackhoz, vagy hozzáadhat egy rendszerképet a Marketplace Syndication használatával, a rendszer egy ritka fájlba konvertálja a rendszerképet. Az 1803-es verzió Azure Stack használata előtt hozzáadott lemezképek nem alakíthatók át. Ehelyett a Piactéri hírszolgáltatás használatával küldje el újra ezeket a lemezképeket a szolgáltatás előnyeinek kihasználásához. 
 
   A ritka fájlok hatékony fájlformátumot használnak a tárolóhelyek használatának csökkentése és az I/O-műveletek javítása érdekében. ? További információ: az [fsutil ritka](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) for Windows Server. 

### <a name="fixed-issues"></a>Megoldott problémák

<!-- 1739988 --> 
- A belső terheléselosztás (ILB) mostantól megfelelően kezeli a háttérbeli virtuális gépek MAC-címét, ami azt eredményezi, hogy a ILB a csomagokat a háttér-hálózaton lévő Linux-példányok használatakor eldobják a háttér-hálózaton. A ILB a Windows-példányokkal jól működik a háttérrendszer-hálózaton. 

<!-- 1805496 --> 
- Olyan probléma, amelyben a Azure Stack közötti VPN-kapcsolatok megszakadnak, mivel Azure Stack az Azure-ban az IKE-házirend különböző beállításait használják. A SALifetime (Time) és a SALiftetime (bájt) értékei nem kompatibilisek az Azure-val, és 1803-ben változtak az Azure-beállításoknak megfelelően. Az 1803-es előtti SALifetime (másodperc) értéke 14 400 volt, és mostantól a 27 000-ra változott 1803-ben. Az 1803-es előtti SALifetime (bájt) értéke 819 200 volt, és a 33 553 408-es változásokat 1803-ben módosították.

<!-- 2209262 --> 
- Az az IP-hiba, amelyben a VPN-kapcsolatok korábban láthatók a portálon; az IP-továbbítás engedélyezése vagy kapcsolása azonban nincs hatással. A funkció alapértelmezés szerint be van kapcsolva, és a módosítási lehetőség még nem támogatott.  A vezérlő el lett távolítva a portálról. 

<!-- 1766332 --> 
- A Azure Stack nem támogatja a házirend-alapú VPN-átjárókat, még akkor is, ha a lehetőség megjelenik a portálon.  A beállítás el lett távolítva a portálról. 

<!-- 1868283 --> 
- Azure Stack mostantól megakadályozza a dinamikus lemezekkel létrehozott virtuális gépek átméretezését. 

<!-- 1756324 --> 
- A virtuális gépek használati adatait mostantól óránként kell elválasztani. Ez konzisztens az Azure-ban. 

<!--  2253274 --> 
- A rendszergazda és a felhasználói portálok esetében a vNet alhálózatok beállítások panelje nem töltődik be. Áthidaló megoldásként a PowerShell és a [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) parancsmag használatával tekintheti meg és kezelheti ezeket az adatokat.

- Virtuális gép létrehozásakor a rendszer nem *jeleníti meg a díjszabást* , ha a virtuálisgép-méret kiválasztásakor nem jelenik meg az üzenet.

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó különféle javítások.


### <a name="changes"></a>Módosítások
- Az újonnan létrehozott ajánlat állapotának módosítása *magánjellegűről* *nyilvánosra* vagy *leszereltre* módosult. További információ: [create a Offer](../azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák    
<!-- 2328416 --> Az 1803-es frissítés telepítése során a blob Service-t és a blob Service-t használó belső szolgáltatásokat is leállíthatja. Ilyenek például a virtuális gépek műveletei. Ez a leállási idő hibát okozhat a bérlői műveletek vagy a riasztások olyan szolgáltatások esetén, amelyek nem férhetnek hozzá az adatokhoz. Ez a probléma feloldja magát, amikor a frissítés befejeződött. 



### <a name="post-update-steps"></a>Frissítés utáni lépések
- A 1803-es telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

  - [KB 4344115 – Azure stack gyorsjavítás 1.0.180427.15](https://support.microsoft.com/help/4344115).

- A frissítés telepítése után tekintse át a tűzfal konfigurációját, és győződjön meg arról, hogy a [szükséges portok](../azure-stack-integrate-endpoints.md) nyitva vannak. Ez a frissítés például bevezeti a *Azure monitor* , amely a naplók naplóba való módosítását is tartalmazza. Ezzel a módosítással a 13012-es port már használatban van, és az is nyitva kell lennie.  


### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)
A Build  **20180323,2**-es verziójának telepítés utáni ismert problémái a következők.

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

- A felügyeleti portálon kritikus riasztás jelenhet meg a *Microsoft. Update. admin* összetevőhöz. A riasztás neve, leírása és szervizelése az összes megjelenítéssel:  
    - *HIBA – hiányzik a hibatípushoz ResourceProviderTimeout sablonja.*

  Ez a riasztás nyugodtan figyelmen kívül hagyható. 


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



- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Miután létrehozta az első helyi hálózati átjáró-kapcsolódást, a rendszer letiltja a helyi hálózati átjáró erőforrásának egyazon IP-címmel történő létrehozását.

- A DNS-kiszolgáló *automatikus* beállításával létrehozott Virtual Network az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

- A Azure Stack a virtuális gép telepítése után nem támogatja további hálózati adapterek hozzáadását a virtuálisgép-példányokhoz. Ha a virtuális gépnek több hálózati adapterre van szüksége, akkor azokat a központi telepítési időpontban kell meghatározni.

<!-- 2096388 --> 
- A hálózati biztonsági csoportra vonatkozó szabályok frissítéséhez a felügyeleti portál nem használható. 

    Megkerülő megoldás App Service: Ha távoli asztalt kell a vezérlő példányaihoz, a hálózati biztonsági csoportokban lévő biztonsági szabályokat a PowerShell használatával kell módosítania.  A következő példa bemutatja, hogyan *engedélyezheti* és állíthatja vissza a konfigurációt a *Megtagadás* érdekében:  
    
    - *Lehetővé*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
> A Azure Stack 1803-re való frissítés után továbbra is használhatja a korábban telepített SQL-és MySQL-erőforrás-szolgáltatókat.  Javasoljuk, hogy frissítse az SQL-t és a MySQL-t, ha új kiadás válik elérhetővé. A Azure Stackhoz hasonlóan az SQL-és a MySQL-erőforrás-szolgáltatókon is alkalmazhatja a frissítéseket.  Ha például az 1711-es verziót használja, először alkalmazza a 1712-es, majd az 1802-es verziót, majd frissítsen a 1803-re.      
>   
> Az 1803-es frissítés telepítése nem befolyásolja az SQL vagy a MySQL erőforrás-szolgáltatók aktuális használatát a felhasználók számára.
> A használt erőforrás-szolgáltatók verziójától függetlenül az adatbázisaiban lévő felhasználói adatai nincsenek megérintve, és továbbra is elérhetők maradnak.    



#### <a name="app-service"></a>App Service
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.

- Az infrastruktúra (a feldolgozók, a felügyelet, az előtéri szerepkörök) felskálázásához a PowerShellt kell használnia a számításhoz szükséges kibocsátási megjegyzésekben leírtak szerint.


#### <a name="usage"></a>Használat  
- A használati nyilvános IP-cím használati mérőszáma a rekord létrehozásakor megjelenő *timedate* -bélyegző helyett ugyanazt a *EventDateTime* -értéket jeleníti meg az egyes rekordoknál. Jelenleg ezeket az adatokat használhatja a nyilvános IP-címek pontos nyilvántartásának elvégzéséhez.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Azure Stack eszközök letöltése a GitHubról
- A következő hibaüzenet jelenik meg, ha az *meghívó-webkérés PowerShell-* parancsmagot használja a Azure stack-eszközök githubról való letöltéséhez:     
    -  *Meghívó-webkérés: a kérelem megszakadt: nem hozható létre SSL/TLS biztonságos csatorna.*     

  Ez a hiba azért fordul elő, mert a GitHub támogatja a Tlsv1 és a TLS 1.1 titkosítási szabvány (a PowerShell alapértelmezett értéke) elavult használatát. További információ: [gyenge titkosítási szabványokra vonatkozó eltávolítási nyilatkozat](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához adja hozzá a `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` parancsot a parancsfájl tetejéhez, hogy a PowerShell-konzol a TLS 1.2-es verzióját használja a GitHub-Tárházak letöltésekor.


## <a name="download-the-update"></a>A frissítés letöltése
A Azure Stack 1803 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Lásd még
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).