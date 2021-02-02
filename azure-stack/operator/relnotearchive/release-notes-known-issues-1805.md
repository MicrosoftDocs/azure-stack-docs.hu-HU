---
title: Azure Stack 1805 frissítés | Microsoft Docs
description: Ismerkedjen meg az Azure Stack integrált rendszerek 1805-es frissítésével kapcsolatos újdonságokkal, beleértve az ismert problémákat és a frissítés letöltésének helyét.
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
ms.openlocfilehash: 16e11fcbc46065f512cc6b887b9c7159f6b0ac65
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248178"
---
# <a name="azure-stack-1805-update"></a>Azure Stack 1805 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk a 1805-es frissítési csomag fejlesztéseit és javításait ismerteti, az ebben a verzióban ismert problémákat és a frissítés letöltésének helyét. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]        
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája    
A Azure Stack 1805 frissítési Build száma **1.1805.1.47**.  

> [!TIP]  
> Az ügyfelek visszajelzései alapján a Microsoft Azure Stack használt verzió sémájának frissítése történik.  Ettől a frissítéstől kezdve a 1805-as verzióban az új séma jobban reprezentálja a felhő aktuális verzióját.  
> 
> A verzió sémája most *Version. YearYearMonthMonth. MinorVersion. BuildNumber* , ahol a második és a harmadik csoport a verziót és a kiadást jelöli. Például a 1805,1 a 1805-es verziójának *gyártói* (RTM) verzióját jelöli.  


### <a name="new-features"></a>Új funkciók
Ez a frissítés a Azure Stack következő tökéletesítéseit tartalmazza.
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack mostantól egy *syslog* -ügyfelet is tartalmaz** *előzetes verziójú szolgáltatásként*. Ez az ügyfél lehetővé teszi a Azure Stack infrastruktúrához kapcsolódó naplózási és biztonsági naplók továbbítását a syslog-kiszolgáló vagy a biztonsági információk és az Event Management (SIEM) szoftver számára, amely kívül van a Azure Stackn. A syslog-ügyfél jelenleg csak az alapértelmezett 514-as porton támogatja a nem hitelesített UDP-kapcsolatokat. Az egyes syslog-üzenetek hasznos adatai a Common Event Format (CEF) formátumban vannak formázva. 

  A syslog-ügyfél konfigurálásához használja a Kiemelt végponton elérhető **set-SyslogServer** parancsmagot. 

  Ebben az előzetes verzióban a következő három riasztás jelenhet meg. Azure Stack által megjelenített riasztások közé tartoznak a *leírások* és a *szervizelési* útmutatások. 
  - TITLE: kód integritása kikapcsolva  
  - TITLE: kód integritása naplózási módban 
  - TITLE: létrehozott felhasználói fiók

  Habár ez a funkció előzetes verzióban érhető el, az éles környezetekben nem lehet rájuk támaszkodni.   




### <a name="fixed-issues"></a>Megoldott problémák

<!-- # - applicability -->
- Kijavítottuk azt a problémát, amely letiltotta a [legördülő lista új támogatási kérésének megnyitását](../azure-stack-manage-portals.md#quick-access-to-help-and-support) a felügyeleti portálról. Ez a beállítás most már a kívánt módon működik. 

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó **különféle javítások** .


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Telepítse a Azure Stack 1804 frissítést a Azure Stack 1805 frissítésének alkalmazása előtt.  
- Telepítse az 1804-es verzióhoz elérhető legújabb frissítést vagy gyorsjavítást.   
- Mielőtt megkezdené a 1805-es frissítés telepítését, futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) -t a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket. 

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák   
- Az 1805-es frissítés telepítése során riasztások jelenhetnek meg a *hibatípushoz UserAccounts. New hiányzik.*  Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan bezáródik a 1805-es frissítés befejeződése után.   

<!-- 2489559 - IS --> 
- A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Frissítés utáni lépések
A 1805-es telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).  
 - [KB 4344102 – Azure stack gyorsjavítás 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)
Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál  

- A Azure Stack műszaki dokumentáció a legújabb kiadásra koncentrál. A kiadások között a portálon megjelenő változások miatt a Azure Stack portálok használatakor megjelenő információk a dokumentációban láthatótól eltérőek lehetnek. 

<!-- 2931230 IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!-- TBD - IS ASDK --> 
- Az illesztőprogram-frissítések nem alkalmazhatók olyan OEM-kiterjesztési csomag használatával, amely Azure Stack ezen verzióját használja.  Erre a problémára nincs Áthidaló megoldás.

<!-- 2551834 - IS, ASDK --> 
- Ha a felügyeleti vagy a felhasználói portálon a Storage-fiókok **áttekintését** választja, akkor az *alapvető* erőforrások ablaktáblán található információk nem jelennek meg.  Az Essentials ablaktábla a fiókkal kapcsolatos információkat jeleníti meg, például az *erőforráscsoportot*, a *helyet* és az *előfizetés azonosítóját*.  Az áttekintő egyéb lehetőségei elérhetők, például a *szolgáltatások* és a *figyelés*, valamint a *megnyitási* lehetőségek a Explorerben vagy a *Storage-fiók törlése*. 

  A nem elérhető információk megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0&preserve-view=true) PowerShell-parancsmagot. 

<!-- 2551834 - IS, ASDK --> 
- Ha a rendszergazdák vagy a felhasználói portálok esetében kiválasztja az egyik Storage-fiók **címkéit** , az információ nem töltődik be, és nem jelenik meg.  

  A nem elérhető információk megtekintéséhez használja a [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0&preserve-view=true) PowerShell-parancsmagot.


<!-- 2332636 - IS -->  
- Ha AD FSt használ a Azure Stack identitásrendszer számára, és a Azure Stack ezen verziójára frissíti, az alapértelmezett szolgáltatói előfizetés alapértelmezett tulajdonosa visszaáll a beépített **CloudAdmin** -felhasználóra.  
  Áthidaló megoldás: a probléma megoldásához a frissítés telepítése után használja a trigger Automation 3. lépését a [jogcím-szolgáltatói megbízhatóság konfigurálásához Azure stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) eljárásban az alapértelmezett szolgáltatói előfizetés tulajdonosának alaphelyzetbe állításához.   

<!-- TBD - IS ASDK --> 
- Néhány felügyeleti előfizetés típusa nem érhető el. Ha a Azure Stack erre a verzióra frissíti, az 1804-es verzióval bevezetett két előfizetési típus nem látható a konzolon. Ez a várható eredmény. A nem elérhető előfizetési típusok a *mérési előfizetés* és a *fogyasztási előfizetés*. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az *alapértelmezett szolgáltatói* előfizetés típusát.  

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

  Mindkét riasztás #1 és #2 nyugodtan figyelmen kívül hagyható, és az idő múlásával automatikusan lezárul. 

  Előfordulhat, hogy az alábbi riasztást is láthatja a *kapacitáshoz*. Ehhez a riasztáshoz a leírásban azonosított rendelkezésre álló memória százalékos aránya változhat:  

  Riasztás #3:
   - NÉV: kevés a memória kapacitása
   - SÚLYOSSÁG: kritikus
   - ÖSSZETEVŐ: kapacitás
   - Leírás: a régió a rendelkezésre álló memória több mint 80,00%-át felhasználta. Sikertelen lehet a nagy mennyiségű memóriával rendelkező virtuális gépek létrehozása.  

  A Azure Stack jelen verziójában ez a riasztás nem tud megfelelően tüzet. Ha a bérlői virtuális gépek továbbra is sikeresen üzembe helyezhetők, nyugodtan figyelmen kívül hagyhatja ezt a riasztást. 
  
  A riasztási #3 nem záródik be automatikusan. Ha bezárta ezt a riasztást, Azure Stack 15 percen belül ugyanazt a riasztást fogja létrehozni.  

<!-- 2368581 - IS. ASDK --> 
- Azure Stack operátorként, ha alacsony memória-riasztást kap, és a bérlői virtuális gépek nem tudnak üzembe helyezni *háló VM-létrehozási hiba* miatt, előfordulhat, hogy az Azure stack Stamp kívül esik a rendelkezésre álló memórián. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) a számítási feladatokhoz rendelkezésre álló kapacitás megismeréséhez. 


### <a name="compute"></a>Compute
<!-- TBD - IS, ASDK --> 
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
- Ha nem sikerül létrehozni egy virtuálisgép-lemezképet, a virtuálisgép-lemezképek számítási paneljén egy nem törölhető sikertelen elem is felvehető.

  Megkerülő megoldásként hozzon létre egy új virtuálisgép-rendszerképet egy, a Hyper-V használatával létrehozható dummy VHD-vel (új-VHD-Path C:\dummy.vhd-Fixed-SizeBytes 1 GB). A folyamatnak meg kell oldania a hibás elemek törlését megakadályozó problémát. Ezt követően 15 perccel a próbabábu rendszerképének létrehozása után sikeresen törölheti.

  Ezután próbálja meg újból letölteni a korábban meghiúsult virtuálisgép-rendszerképet.

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  


### <a name="networking"></a>Hálózatkezelés
<!-- TBD - IS ASDK --> 
- A felhasználó által megadott útvonalak nem hozhatók létre a rendszergazda vagy a felhasználói portálon. Áthidaló megoldásként használja a [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
- Ha VPN-kapcsolat beállításához a **létrehozás VPN Gateway** elemre kattint, a **hálózat** területen a **házirend-alapú** beállítás VPN-típusként jelenik meg. Ne jelölje be ezt a beállítást. Azure Stack csak az **útvonalon alapuló** beállítást támogatja.

<!-- 2388980 - IS ASDK --> 
- Miután létrehozta és hozzárendelte a virtuális gépet egy nyilvános IP-címhez, a virtuális gépet nem lehet az adott IP-címről hozzárendelni. A kapcsolat megszüntetve állapotba kerül, de a korábban hozzárendelt nyilvános IP-cím továbbra is az eredeti virtuális géphez lesz társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia a létrehozott új virtuális gépekhez.

  Ez akkor is előfordul, ha az IP-címet egy új virtuális géphez rendeli hozzá (általában VIP- *swap* néven). Az IP-címen keresztüli kapcsolódás minden jövőbeli kísérlete az eredeti virtuális géphez való kapcsolódást eredményezi, nem pedig az újat.

<!-- 2292271 - IS ASDK --> 
- Ha kvótát hoz létre egy olyan hálózati erőforráshoz, amely egy bérlői előfizetéshez társított ajánlat és csomag része, az új korlátot a rendszer nem alkalmazza az adott előfizetésre. Az új korlát azonban az új előfizetésekre is vonatkozik, amelyek a kvóta növelése után jönnek létre.

  A probléma megkerüléséhez használjon Add-On tervet a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetéshez. További információ: How to [make a add-on Plan elérhető](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Nem törölhet olyan előfizetést, amely a DNS-zónák erőforrásaival vagy a hozzájuk társított útválasztási erőforrásokkal rendelkezik. Az előfizetés sikeres törléséhez először törölnie kell a DNS-zónát, és a bérlői előfizetésből kell átirányítani a tábla erőforrásait.


<!-- 1902460 - IS ASDK --> 
- A Azure Stack IP-címenként egyetlen *helyi hálózati átjárót* támogat. Ez az összes bérlői előfizetés esetében igaz. Miután létrehozta az első helyi hálózati átjáró-kapcsolódást, a rendszer letiltja a helyi hálózati átjáró erőforrásának egyazon IP-címmel történő létrehozását.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló *automatikus* beállításával létrehozott Virtual Network az egyéni DNS-kiszolgálókra való váltás meghiúsul. A frissített beállítások nem lesznek leküldve az adott vnet lévő virtuális gépekre.

<!-- TBD - IS ASDK --> 
- A Azure Stack a virtuális gép telepítése után nem támogatja további hálózati adapterek hozzáadását a virtuálisgép-példányokhoz. Ha a virtuális gépnek több hálózati adapterre van szüksége, akkor azokat a központi telepítési időpontban kell meghatározni.

<!-- 2096388 IS --> 
- A hálózati biztonsági csoportra vonatkozó szabályok frissítéséhez a felügyeleti portál nem használható.

    Megkerülő megoldás App Service: Ha távoli asztalt kell a vezérlő példányaihoz, a hálózati biztonsági csoportokban lévő biztonsági szabályokat a PowerShell használatával kell módosítania.  A következő példa bemutatja, hogyan *engedélyezheti* és állíthatja vissza a konfigurációt a *Megtagadás* érdekében:  

    - *Lehetővé*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn't work. Need to set properties again even in case of edit

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

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn't work. Need to set properties again even in case of edit

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


### <a name="sql-and-mysql"></a>SQL és MySQL

<!-- TBD - IS --> 
- Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.  

<!-- IS, ASDK --> 
- A speciális karakterek, beleértve a szóközöket és az időszakokat, nem támogatottak a **családban** vagy a **rétegek** neveiben, ha az SQL-és a MySQL erőforrás-SZOLGÁLTATÓhoz hoz létre SKU-t.

<!-- TBD - IS --> 
> [!NOTE]  
> A Azure Stack 1805-re való frissítés után továbbra is használhatja a korábban telepített SQL-és MySQL-erőforrás-szolgáltatókat.  Javasoljuk, hogy frissítse az SQL-t és a MySQL-t, ha új kiadás válik elérhetővé. A Azure Stackhoz hasonlóan az SQL-és a MySQL-erőforrás-szolgáltatókon is alkalmazhatja a frissítéseket. Ha például az 1803-es verziót használja, először alkalmazza a 1804-es verziót, majd frissítsen a 1805-ra.      
>   
> Az 1805-es frissítés telepítése nem befolyásolja az SQL vagy a MySQL erőforrás-szolgáltatók aktuális használatát a felhasználók számára.
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
A Azure Stack 1805 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Lásd még
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).