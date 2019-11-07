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
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4c04eafab93da233859b5b67571b70899b081b95
ms.sourcegitcommit: c583f19d15d81baa25dd49738d53d8fc01463bef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659241"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Hibaelhárítás Microsoft Azure Stack

Ez a dokumentum a Azure Stack integrált környezetek hibaelhárítási információit tartalmazza. Ha segítségre van a Azure Stack Development Kit kapcsolatban, tekintse meg a [ASDK-hibaelhárítást](../asdk/asdk-troubleshooting.md) ismertető témakört, vagy kérjen segítséget a [Azure stack MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)szakértőitől. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ezek a témakörök a Microsoft terméktámogatási szolgálatának (CSS) eljuttatott gyakori kérdésekre mutató hivatkozásokat tartalmaznak.

### <a name="purchase-considerations"></a>Vásárlási szempontok

* [A vásárlás menete](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack áttekintése](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Frissítések és diagnosztika

* [Diagnosztikai eszközök használata a Azure Stackban](azure-stack-diagnostics.md)
* [A Azure Stack rendszerállapotának ellenőrzése](azure-stack-diagnostic-test.md)
* [A csomag kiadási ritmusának frissítése](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>A vendég virtuális gépek által támogatott operációs rendszerek és méretek

* [Az Azure Stack által támogatott vendég operációs rendszerek](azure-stack-supported-os.md)
* [Az Azure Stackben támogatott virtuálisgép-méretek](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Piactér

* [Az Azure Stackhez elérhető Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Kapacitás kezelése

#### <a name="memory"></a>Memória

További memória hozzáadásával megnövelheti az Azure Stack számára rendelkezésre álló teljes memóriakapacitást. Az Azure Stackben a fizikai kiszolgálót skálázásiegység-csomópontnak is nevezik. Az ugyanahhoz a skálázási egységhez tartozó skálázásiegység-csomópontokhoz [azonos mennyiségű memóriát](azure-stack-manage-storage-physical-memory-capacity.md) kell hozzárendelni.

#### <a name="retention-period"></a>Megőrzési idő

Az adatmegőrzési időtartam beállítása lehetővé teszi, hogy a felhőüzemeltető napokban (0–9999 nap) meghatározza azt az időszakot, amely alatt a törölt fiókok esetleg helyreállíthatók. Az alapértelmezett megőrzési időtartam **0** napra van állítva. Ha a **0** értéket állítja be, az azt jelenti, hogy a törölt fiókok azonnal megmaradnak, és meg vannak jelölve az időszakos szemét-gyűjtéshez.

* [Az adatmegőrzési időszak beállítása](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Biztonság, megfelelőség és identitás  

#### <a name="manage-rbac"></a>Az RBAC kezelése

Az Azure Stack-felhasználók az előfizetés, erőforráscsoport vagy szolgáltatás minden egyes példányánál rendelkezhetnek olvasó, tulajdonos vagy közreműködő szerepkörrel.

* [Azure Stack RBAC kezelése](azure-stack-manage-permissions.md)

Ha az Azure-erőforrások beépített szerepkörei nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel.

* [Oktatóanyag: egyéni szerepkör létrehozása Azure-erőforrásokhoz Azure PowerShell használatával](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Használat és számlázás kezelése felhőszolgáltatóként

* [Használat és számlázás kezelése felhőszolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [CSP-vagy APSS-előfizetés létrehozása](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Válassza ki az Azure Stackhez használandó megosztott szolgáltatásfiók típusát. Az alábbi előfizetés-típusokkal lehet regisztrálni egy több-bérlős Azure Stacket:

* Cloud Solution Provider program
* Partner Shared Services-előfizetés

### <a name="get-scale-unit-metrics"></a>Méretezési egység metrikáinak beolvasása

A PowerShell használatával lekérheti a bélyegző kihasználtsági adatait a CSS súgója nélkül. A bélyegző kihasználtságának beszerzése: 

1. PEP-munkamenet létrehozása
2. Teszt futtatása – azurestack
3. A PEP-munkamenet bezárása
4. A Get-azurestacklog-filterbyrole seedring futtatása hívási parancs használatával
5. Bontsa ki a seedring. zip fájlt, és szerezze be az ellenőrzési jelentést a ERCS mappából, amelyen a test-azurestack futott.

További információ: [Azure stack diagnosztika](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems).

## <a name="troubleshoot-virtual-machines"></a>Virtuális gépek hibáinak megoldása
### <a name="default-image-and-gallery-item"></a>Alapértelmezett rendszerkép és gyűjtemény elem
A virtuális gépek Azure Stack-ben való üzembe helyezése előtt hozzá kell adni egy Windows Server-lemezképet és-gyűjteményi elemeket.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>Töröltem néhány virtuális gépet, de a lemezen továbbra is láthatók a VHD-fájlok
Ennek a viselkedésnek a kialakítása a következő:

* Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. A lemezek különálló erőforrások az erőforráscsoporthoz.
* Ha a Storage-fiók törölve lesz, a törlés azonnal látható Azure Resource Manageron keresztül, de az esetlegesen tartalmazott lemezek továbbra is tárolás alatt maradnak a tárolóban, amíg a rendszer a szemetet nem fut.

Ha "árva" virtuális merevlemezeket lát, fontos tudni, hogy a mappa részét képezik-e a törölt Storage-fiók mappájának. Ha a Storage-fiók nem lett törölve, akkor azok normálisak maradnak.

További információk az adatmegőrzési küszöbérték és az igény szerinti, a [Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)című témakörben olvashatók.

## <a name="troubleshoot-storage"></a>A tárolás hibaelhárítása
### <a name="storage-reclamation"></a>Tárhely-visszanyerés
Akár 14 órát is igénybe vehet a visszaigényelt kapacitás a portálon való megjelenítéséhez. A lemezterület-visszanyerés a különböző tényezőktől függ, például a belső tároló fájljainak használati százaléka a blob-tárolóban. Ezért attól függően, hogy mennyit töröl az adatmennyiség, a rendszer nem garantálja, hogy mennyi helyet szabadít fel a rendszer a Garbage Collector futtatásakor.

### <a name="azure-storage-explorer-not-working-with-azure-stack"></a>Azure Storage Explorer nem működik Azure Stack 
 
Ha egy integrált rendszer leválasztott forgatókönyvben van használatban, ajánlott vállalati hitelesítésszolgáltatót (CA) használni. Exportálja a főtanúsítványt Base-64 formátumban, majd importálja Azure Storage Explorerba. Győződjön meg arról, hogy eltávolítja a záró perjelet ("/") az ARM-végpontból. További információ: [felkészülés a Azure Stackhoz való csatlakozásra](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se#prepare-for-connecting-to-azure-stack).
 

## <a name="troubleshooting-app-service"></a>Hibaelhárítási App Service
### <a name="create-aadidentityappps1-script-fails"></a>A Create-AADIdentityApp. ps1 parancsfájl végrehajtása sikertelen

Ha a App Servicehoz szükséges Create-AADIdentityApp. ps1 parancsfájl nem sikerül, ügyeljen arra, hogy a parancsfájl futtatásakor vegye fel a szükséges-AzureStackAdminCredential paramétert. További információ: a [app Service telepítésének Előfeltételei Azure stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

