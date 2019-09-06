---
title: Microsoft Azure Stack hibaelhárítás | Microsoft Docs
description: Azure Stack hibaelhárítás.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: a9d62640b2baabfd3283099656719a880dd0a41b
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377243"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Hibaelhárítás Microsoft Azure Stack

Ez a dokumentum a Azure Stack hibaelhárítási információit tartalmazza. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ezek a témakörök a Microsoft terméktámogatási szolgálatának (CSS) eljuttatott gyakori kérdésekre mutató hivatkozásokat tartalmaznak.

### <a name="purchase-considerations"></a>Vásárlási szempontok

* [A vásárlás menete](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack áttekintése](azure-stack-overview.md)

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack fejlesztői készlet (ASDK)

Ha segítségre van a [Azure stack Development Kit](../asdk/asdk-what-is.md)számára, ismerkedjen meg a [Azure stack MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)szakértőivel. A ASDK nem támogatja a CSS-t támogató értékelési környezetet. Az ASDK-hez megnyitott támogatási eseteket az MSDN-fórumnak nevezzük.

### <a name="updates-and-diagnostics"></a>Frissítések és diagnosztika

* [Diagnosztikai eszközök használata a Azure Stackban](azure-stack-diagnostics.md)
* [A Azure Stack rendszerállapotának ellenőrzése](azure-stack-diagnostic-test.md)
* [A csomag kiadási ritmusának frissítése](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>A vendég virtuális gépek által támogatott operációs rendszerek és méretek

* [Az Azure Stack által támogatott vendég operációs rendszerek](azure-stack-supported-os.md)
* [Azure Stack támogatott virtuálisgép-méretek](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Az Azure Stackhez elérhető Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Kapacitás kezelése

#### <a name="memory"></a>Memory (Memória)

További memória hozzáadásával megnövelheti az Azure Stack számára rendelkezésre álló teljes memóriakapacitást. Az Azure Stackben a fizikai kiszolgálót skálázásiegység-csomópontnak is nevezik. Az ugyanahhoz a skálázási egységhez tartozó skálázásiegység-csomópontokhoz [azonos mennyiségű memóriát](azure-stack-manage-storage-physical-memory-capacity.md) kell hozzárendelni.

#### <a name="retention-period"></a>Megtartási időszak

Az adatmegőrzési időtartam beállítása lehetővé teszi, hogy a felhőüzemeltető napokban (0–9999 nap) meghatározza azt az időszakot, amely alatt a törölt fiókok esetleg helyreállíthatók. Az alapértelmezett adatmegőrzési időszak 0 nap. Az érték „0”-ra állításával a törölt fiók azonnal megszűnik, és a rendszer megjelöli az időszakos szemétgyűjtésre.

* [Az adatmegőrzési időszak beállítása](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Biztonság, megfelelőség és identitás  

#### <a name="manage-rbac"></a>Az RBAC kezelése

Az Azure Stack-felhasználók az előfizetés, erőforráscsoport vagy szolgáltatás minden egyes példányánál rendelkezhetnek olvasó, tulajdonos vagy közreműködő szerepkörrel.

* [Azure Stack RBAC kezelése](azure-stack-manage-permissions.md)

Ha az Azure-erőforrások beépített szerepkörei nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel.

* [Oktatóanyag: Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure PowerShell használatával](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Kezelése a használati és számlázási információkkal egy CSP-hez

* [Használat és számlázás kezelése felhőszolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [CSP-vagy APSS-előfizetés létrehozása](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Válassza ki az Azure Stackhez használandó megosztott szolgáltatásfiók típusát. Az alábbi előfizetés-típusokkal lehet regisztrálni egy több-bérlős Azure Stacket:

* Felhőszolgáltató
* Partner Shared Services-előfizetés


## <a name="troubleshoot-deployment"></a>Üzembe helyezési hibák 
### <a name="general-deployment-failure"></a>Általános telepítési hiba
Ha a telepítés során hiba lép fel, a központi telepítési parancsfájl újbóli beállításával újraindíthatja a telepítést a sikertelen lépéssel.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>A ASDK-telepítés végén a PowerShell-munkamenet továbbra is nyitva van, és nem jeleníti meg a kimenetet.
Ez a viselkedés valószínűleg csak egy PowerShell-parancssorablak alapértelmezett viselkedésének eredménye, ha ki van választva. A fejlesztői csomag üzembe helyezése sikeres volt, de a szkriptet az ablak kiválasztásakor szüneteltették. Ellenőrizze, hogy a telepítés befejeződött-e, ha a "kiválasztás" szót keresi a parancssori ablakban. Az ESC billentyű lenyomásával törölje a kijelölését, és a befejezési üzenetet is meg kell jeleníteni.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>A telepítés sikertelen a külső hozzáférés hiánya miatt
Ha a telepítés sikertelen, és a külső hozzáférés kötelező, az alábbi példához hasonló kivételt ad vissza:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Ha ez a hiba történik, ellenőrizze, hogy az összes minimális hálózati követelmény teljesült-e a [központi telepítési hálózati forgalom dokumentációjának](deployment-networking.md)áttekintésével. A partneri eszközkészlet részeként egy hálózati ellenőrzési eszköz is elérhető a partnerek számára.

Az üzembe helyezési hibák a fenti kivétellel általában az interneten található erőforrásokhoz való kapcsolódási problémák miatt jelentkeznek.

A probléma ellenőrzéséhez hajtsa végre a következő lépéseket:

1. A PowerShell megnyitása
2. Adja meg a-PSSession a WAS01 vagy a ERCs virtuális gépekhez
3. Futtassa a parancsmagot: Test-NetConnection login.windows.net-port 443

Ha a parancs végrehajtása sikertelen, ellenőrizze, hogy a TOR kapcsoló és bármely más hálózati eszköz úgy van-e konfigurálva, hogy [engedélyezze a hálózati forgalmat](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Virtuális gépek hibáinak megoldása
### <a name="default-image-and-gallery-item"></a>Alapértelmezett rendszerkép és gyűjtemény elem
A virtuális gépek Azure Stack-ben való üzembe helyezése előtt hozzá kell adni egy Windows Server-lemezképet és-gyűjteményi elemeket.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>A Azure Stack gazdagép újraindítása után előfordulhat, hogy néhány virtuális gép nem indul el automatikusan.
A gazdagép újraindítása után észreveheti, Azure Stack szolgáltatások nem azonnal érhetők el.  Ennek az az oka, hogy Azure Stack [infrastruktúra virtuális](../asdk/asdk-architecture.md#virtual-machine-roles ) gépei és erőforrás-szolgáltatói időt vesz igénybe a konzisztencia ellenőrzéséhez, de a rendszer végül automatikusan elindul.

Azt is megfigyelheti, hogy a bérlői virtuális gépek nem indulnak el automatikusan a Azure Stack Development Kit-gazdagép újraindítása után. Ez egy ismert probléma, és csak néhány manuális lépést kell megtennie, hogy online állapotba kerüljön:

1.  A Azure Stack Development Kit-gazdagépen indítsa el **Feladatátvevőfürt-kezelő** a Start menüből.
2.  Válassza ki az **S-cluster. azurestack. local**fürtöt.
3.  Válassza a **szerepkörök**lehetőséget.
4.  A bérlői virtuális gépek *mentett* állapotban jelennek meg. Ha az összes infrastruktúra-virtuális gép fut, kattintson a jobb gombbal a bérlői virtuális gépekre, és válassza az **Indítás** lehetőséget a folytatáshoz.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Töröltem néhány virtuális gépet, de továbbra is láthatók a lemezen lévő VHD-fájlok. Ez a viselkedés várható?
Igen, ez a várt viselkedés. Ez a következőképpen lett kialakítva:

* Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. A lemezek különálló erőforrások az erőforráscsoporthoz.
* Ha a Storage-fiók törölve lesz, a törlés azonnal látható Azure Resource Manageron keresztül, de az esetlegesen tartalmazott lemezek továbbra is tárolás alatt maradnak a tárolóban, amíg a rendszer a szemetet nem fut.

Ha "árva" virtuális merevlemezeket lát, fontos tudni, hogy a mappa részét képezik-e a törölt Storage-fiók mappájának. Ha a Storage-fiók nem lett törölve, akkor azok normálisak maradnak.

További információk az adatmegőrzési küszöbérték és az igény szerinti, a [Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)című témakörben olvashatók.

## <a name="troubleshoot-storage"></a>A tárolás hibaelhárítása
### <a name="storage-reclamation"></a>Tárhely-visszanyerés
Akár 14 órát is igénybe vehet a visszaigényelt kapacitás a portálon való megjelenítéséhez. A lemezterület-visszanyerés a különböző tényezőktől függ, például a belső tároló fájljainak használati százaléka a blob-tárolóban. Ezért attól függően, hogy mennyit töröl az adatmennyiség, a rendszer nem garantálja, hogy mennyi helyet szabadít fel a rendszer a Garbage Collector futtatásakor.

