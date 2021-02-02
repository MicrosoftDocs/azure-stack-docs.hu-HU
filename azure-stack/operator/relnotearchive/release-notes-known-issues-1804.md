---
title: Azure Stack 1804 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1804-es frissítéssel Azure Stack integrált rendszerek, az ismert problémák és a frissítés letöltésének helyétől.
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
ms.openlocfilehash: 5d871458a7af4e9233e750b07404bc151274033a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248258"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1804-es frissítési csomag fejlesztéseit és javításait ismerteti, az ebben a kiadásban ismert problémákat, valamint a frissítés letöltésének helyét. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra és a build (telepítés utáni) problémákra vannak osztva.

> [!IMPORTANT]        
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája    
A Azure Stack 1804 Update Build száma **20180513,1**.   

### <a name="new-features"></a>Új funkciók
Ez a frissítés a Azure Stack következő tökéletesítéseit tartalmazza.

<!-- 15028744 - IS -->  
- **A Visual Studio támogatja a leválasztott Azure stack központi telepítéseket a AD FS használatával**. A Visual Studióban mostantól előfizetéseket adhat hozzá, és a hitelesítés AD FS összevont felhasználói hitelesítő adatokkal. 
 
<!-- 1779474, 1779458 - IS --> 
- **A Av2 és az F sorozatú virtuális gépek használata**. A Azure Stack mostantól a Av2 és az F sorozatú virtuálisgép-méretek alapján is használhat virtuális gépeket. További információ: [Azure stack által támogatott virtuálisgép-méretek](../../user/azure-stack-vm-sizes.md). 

<!-- 1759172 - IS, ASDK --> 
- **Új rendszergazdai előfizetések**. A 1804-as verzióban két új előfizetési típus érhető el a portálon. Ezek az új előfizetési típusok az alapértelmezett szolgáltatói előfizetésen kívül a 1804-es verziótól kezdődően új Azure Stack-telepítések mellett láthatók. *Ezeket az új előfizetési típusokat ne használja Azure stack ezen verziójával*. Bejelentjük a rendelkezésre állást, hogy ezeket az előfizetési típusokat egy későbbi frissítéssel használhassa. 

  Ha a 1804-es verzióra frissíti a Azure Stack, a két új előfizetés típusa nem látható. A Azure Stack integrált rendszerek és a Azure Stack Development Kit 1804-es vagy újabb verziójának telepítése azonban új központi telepítések mind a három előfizetési típushoz elérhetők.  

  Ezek az új előfizetési típusok nagyobb módosítás részét képezik az alapértelmezett szolgáltatói előfizetés biztonságossá tételéhez, és megkönnyítik a megosztott erőforrások, például az SQL üzemeltetési kiszolgálók üzembe helyezését. Mivel a nagyobb változások további részeit bővítjük a jövőbeli frissítésekkel Azure Stack az új előfizetési típusok alatt üzembe helyezett erőforrások elvesznek. 

  A három előfizetési típus már látható:  
  - Alapértelmezett szolgáltatói előfizetés: folytassa az előfizetés típusának használatát. 
  - Mérési előfizetés: ne *használja ezt az előfizetési típust.*
  - Felhasználási előfizetés: ne *használja ezt az előfizetési típust*

  



## <a name="fixed-issues"></a>Megoldott problémák

<!-- IS, ASDK -->  
- A felügyeleti portálon már nem kell frissítenie a frissítési csempét, mielőtt adatokat jelenít meg.
 
<!-- 2050709  -->  
- Mostantól a felügyeleti portál használatával szerkesztheti a Blob service, Table service és Queue szolgáltatás tárolási metrikáit.
 
<!-- IS, ASDK --> 
- A **hálózat** alatt, amikor a **kapcsolat** lehetőségre kattint a VPN-kapcsolat beállításához, a **helyek közötti (IPSec)** most az egyetlen elérhető lehetőség.

- A teljesítményre, a stabilitásra, a biztonságra és az Azure Stack által használt operációs rendszerre vonatkozó **különféle javítások** .

## <a name="additional-releases-timed-with-this-update"></a>A frissítéssel egyidejűleg további kiadások is megjelentek  
A következők már elérhetők, de nem igényelnek Azure Stack 1804-es frissítést.
- **Frissítsen a Microsoft Azure Stack System Center Operations Manager figyelési csomagra**. [Letölthető](https://www.microsoft.com/download/details.aspx?id=55184)a Microsoft System Center Operations Manager Azure stack figyelési csomagjának új verziója (1.0.3.0). Ebben a verzióban az egyszerű szolgáltatásokat a csatlakoztatott Azure Stack központi telepítésének hozzáadásakor használhatja. Ez a verzió olyan Update Management-élményt is tartalmaz, amely lehetővé teszi a Szervizelési műveletek közvetlen elvégzését Operations Manager belülről. Vannak olyan új irányítópultok is, amelyek az erőforrás-szolgáltatókat, a skálázási egységeket és a skálázási egység csomópontjait jelenítik meg.

- **Új Azure stack felügyeleti PowerShell-verzió 1.3.0**.  Azure Stack PowerShell-1.3.0 már elérhető a telepítéshez. Ez a verzió parancsokat biztosít az összes felügyeleti erőforrás-szolgáltató számára a Azure Stack kezeléséhez.  Ebben a kiadásban egyes tartalmak elavultak lesznek a Azure Stack Tools GitHub- [adattárból](https://github.com/Azure/AzureStack-Tools). 

   A telepítés részleteiért kövesse az [utasításokat](../azure-stack-powershell-install.md) vagy a [Súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) tartalmát Azure stack modul 1.3.0. 

- **Azure stack API Rest-hivatkozás első kiadása**. Az [összes Azure stack felügyeleti erőforrás-szolgáltató API-referenciája](https://docs.microsoft.com/rest/api/azure-stack/) már közzé van téve. 


## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Telepítse a Azure Stack 1803 frissítést a Azure Stack 1804 frissítésének alkalmazása előtt.  
  
- Telepítse az 1803-es verzióhoz elérhető legújabb frissítést vagy gyorsjavítást. 


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák   
- Az 1804-es frissítés telepítése során riasztások jelenhetnek meg a *hibatípushoz UserAccounts. New hiányzik.*  Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan bezáródik a 1804-es frissítés befejeződése után.   
 
<!-- TBD - IS --> 
- A frissítés telepítése során ne kísérelje meg a virtuális gépek létrehozását. A frissítések kezelésével kapcsolatos további információkért lásd: [a frissítések kezelése Azure stack áttekintése](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Frissítés utáni lépések
A 1804-es telepítése után telepítse az összes vonatkozó gyorsjavítást. További információért tekintse meg a következő Tudásbázis-cikkeket, valamint a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).  
 - [KB 4344114 – Azure stack gyorsjavítás 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)
A Build  **20180513,1**-es verziójának telepítés utáni ismert problémái a következők.

#### <a name="portal"></a>Portál

- A Azure Stack műszaki dokumentáció a legújabb kiadásra koncentrál. A kiadások között a portálon megjelenő változások miatt a Azure Stack portálok használatakor megjelenő információk a dokumentációban láthatótól eltérőek lehetnek. 

<!-- TBD - IS ASDK --> 
- Az illesztőprogram-frissítések nem alkalmazhatók olyan OEM-kiterjesztési csomag használatával, amely Azure Stack ezen verzióját használja.  Erre a problémára nincs Áthidaló megoldás.

<!-- 1272111 - IS --> 
- A Azure Stack ezen verziójának telepítése vagy frissítése után előfordulhat, hogy nem tudja megtekinteni Azure Stack skálázási egységeket a felügyeleti portálon.  
  Megkerülő megoldás: a skálázási egységekkel kapcsolatos információk megtekintéséhez használja a PowerShellt. További információ: Azure Stack modul 1.3.0 kapcsolatos [súgótartalom](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) . 

<!-- 2332636 - IS -->  
- Ha AD FSt használ a Azure Stack identitásrendszer számára, és a Azure Stack ezen verziójára frissíti, az alapértelmezett szolgáltatói előfizetés alapértelmezett tulajdonosa visszaáll a beépített **CloudAdmin** -felhasználóra.  
  Áthidaló megoldás: a probléma megoldásához a frissítés telepítése után használja a trigger Automation 3. lépését a [jogcím-szolgáltatói megbízhatóság konfigurálásához Azure stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) eljárásban az alapértelmezett szolgáltatói előfizetés tulajdonosának alaphelyzetbe állításához.   

<!-- TBD - IS ASDK --> 
- Néhány felügyeleti előfizetés típusa nem érhető el.  Ha a Azure Stack erre a verzióra frissíti, az [1804-es verzióval bevezetett](#new-features) két előfizetési típus nem látható a konzolon. Ez a várható eredmény. A nem elérhető előfizetési típusok a *mérési előfizetés* és a *fogyasztási előfizetés*. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az *alapértelmezett szolgáltatói* előfizetés típusát.  


<!-- TBD -  IS ASDK -->
- [Új támogatási kérést nyithat meg a legördülő menüből](../azure-stack-manage-portals.md#quick-access-to-help-and-support) a felügyeleti portálon, nem érhető el. Ehelyett használja a következő hivatkozást:     
    - Azure Stack integrált rendszerek esetén használja a következőt: https://aka.ms/newsupportrequest .

<!-- 2403291 - IS ASDK --> 
- Előfordulhat, hogy nem használja a vízszintes görgetősávot a rendszergazda és a felhasználói portál alján. Ha a vízszintes görgetősávhoz fér hozzá, a navigációs sáv segítségével navigáljon egy korábbi panelre a portálon, és válassza ki a megtekinteni kívánt panel nevét a portál bal felső részén található navigációs listából.

<!-- TBD - IS --> 
- Előfordulhat, hogy a felügyeleti portálon nem lehet megtekinteni a számítási és a tárolási erőforrásokat. A probléma oka a frissítés telepítése során felmerülő hiba, amelynek hatására a frissítés helytelenül lesz jelentve. Ha ez a probléma jelentkezik, kérjen segítséget a Microsoft ügyfélszolgálati szolgálatának.

<!-- TBD - IS --> 
- Előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához jelölje ki a fogaskerék ikont a portál jobb felső sarkában, majd válassza az **alapértelmezett beállítások visszaállítása** lehetőséget.

<!-- TBD - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- TBD - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja a PowerShellt az engedélyek ellenőrzéséhez.

<!-- TBD - IS ASDK --> 
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
 

#### <a name="compute"></a>Compute
<!-- TBD - IS --> 
- A virtuálisgép-telepítés virtuálisgép-méretének kiválasztásakor egyes F sorozatú virtuális gépek méretei nem láthatók a méret kiválasztásának részeként a virtuális gép létrehozásakor. A következő virtuálisgép-méretek nem jelennek meg a választóban: *F8s_v2*, *F16s_v2*, *F32s_v2* és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét egy virtuális gép üzembe helyezéséhez. Minden metódusban meg kell adnia a használni kívánt virtuálisgép-méretet.

  - **Azure Resource Manager sablon:** Ha sablont használ, állítsa a sablonban lévő *vmSize* úgy, hogy az egyenlő legyen a kívánt VM-mérettel. Az alábbi példa a *F32s_v2* méretet használó virtuális gépek üzembe helyezésére használható:  

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
- Ha virtuális gépeket hoz létre a Azure Stack felhasználói portálon, a portál helytelen számú adatlemezt jelenít meg, amelyek egy D sorozatú virtuális géphez csatolhatók. Az összes támogatott D sorozatú virtuális gép annyi adatlemezt képes kezelni, mint az Azure-konfiguráció.

<!-- TBD - IS ASDK --> 
- Ha nem sikerül létrehozni egy virtuálisgép-lemezképet, a virtuálisgép-lemezképek számítási paneljén egy nem törölhető sikertelen elem is felvehető.

  Megkerülő megoldásként hozzon létre egy új virtuálisgép-rendszerképet egy, a Hyper-V használatával létrehozható dummy VHD-vel (új-VHD-Path C:\dummy.vhd-Fixed-SizeBytes 1 GB). A folyamatnak meg kell oldania a hibás elemek törlését megakadályozó problémát. Ezt követően 15 perccel a próbabábu rendszerképének létrehozása után sikeresen törölheti.

  Ezután próbálja meg újból letölteni a korábban meghiúsult virtuálisgép-rendszerképet.

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, a felhasználóknak el kell engedniük a kiépítési időkorlátot ahelyett, hogy le kellene állítani a virtuális gép felszabadítására vagy törlésére irányuló folyamatot.  

<!-- 1662991 IS ASDK --> 
- A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi.  


#### <a name="networking"></a>Hálózatkezelés
<!-- 1766332 - IS ASDK --> 
- Ha VPN-kapcsolat beállításához a **létrehozás VPN Gateway** elemre kattint, a **hálózat** területen a **házirend-alapú** beállítás VPN-típusként jelenik meg. Ne jelölje be ezt a beállítást. Azure Stack csak az **útvonalon alapuló** beállítást támogatja.

<!-- 2388980 - IS ASDK --> 
- Miután létrehozta és hozzárendelte a virtuális gépet egy nyilvános IP-címhez, a virtuális gépet nem lehet az adott IP-címről hozzárendelni. A kapcsolat megszüntetve állapotba kerül, de a korábban hozzárendelt nyilvános IP-cím továbbra is az eredeti virtuális géphez lesz társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia a létrehozott új virtuális gépekhez.

  Ez akkor is előfordul, ha az IP-címet egy új virtuális géphez rendeli hozzá (általában VIP- *swap* néven). Az ezen az IP-címen keresztüli kapcsolódás minden jövőbeli kísérlete az eredetileg társított virtuális géphez való kapcsolódást eredményezi, nem pedig az újat.

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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

<!-- TBD - IS --> 
- Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.  

<!-- IS, ASDK --> 
- A speciális karakterek, beleértve a szóközöket és az időszakokat, nem támogatottak a **családban** vagy a **rétegek** neveiben, ha az SQL-és a MySQL erőforrás-SZOLGÁLTATÓhoz hoz létre SKU-t.

<!-- TBD - IS -->
> [!NOTE]  
> A Azure Stack 1804-re való frissítés után továbbra is használhatja a korábban telepített SQL-és MySQL-erőforrás-szolgáltatókat.  Javasoljuk, hogy frissítse az SQL-t és a MySQL-t, ha új kiadás válik elérhetővé. A Azure Stackhoz hasonlóan az SQL-és a MySQL-erőforrás-szolgáltatókon is alkalmazhatja a frissítéseket.  Ha például az 1802-es verziót használja, először alkalmazza a 1803-es verziót, majd frissítsen a 1804-ra.      
>   
> Az 1804-es frissítés telepítése nem befolyásolja az SQL vagy a MySQL erőforrás-szolgáltatók aktuális használatát a felhasználók számára.
> A használt erőforrás-szolgáltatók verziójától függetlenül az adatbázisaiban lévő felhasználói adatai nincsenek megérintve, és továbbra is elérhetők maradnak.    



#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- A felhasználóknak regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozzák az előfizetésben.

<!-- TBD - IS ASDK --> 
- Az infrastruktúra (a feldolgozók, a felügyelet, az előtéri szerepkörök) felskálázásához a PowerShellt kell használnia a számításhoz szükséges kibocsátási megjegyzésekben leírtak szerint.

<!-- TBD - IS ASDK --> 
- A App Service jelenleg csak az **alapértelmezett szolgáltatói előfizetésbe** helyezhető üzembe.  Egy későbbi frissítési App Service a Azure Stack 1804-as verzióban bevezetett új mérési előfizetésbe fog települni, és az összes meglévő központi telepítés át lesz telepítve erre az új előfizetésre is.

#### <a name="usage"></a>Használat  
<!-- TBD - IS ASDK --> 
- A használati nyilvános IP-cím használati mérőszáma a rekord létrehozásakor megjelenő *timedate* -bélyegző helyett ugyanazt a *EventDateTime* -értéket jeleníti meg az egyes rekordoknál. Jelenleg ezeket az adatokat használhatja a nyilvános IP-címek pontos nyilvántartásának elvégzéséhez.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>A frissítés letöltése
A Azure Stack 1804 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Lásd még
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).
- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).