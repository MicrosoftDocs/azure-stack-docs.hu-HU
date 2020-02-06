---
title: Azure Stack hub hibáinak megoldása
titleSuffix: Azure Stack
description: Megtudhatja, hogyan lehet elhárítani a Azure Stack hubot, beleértve a virtuális gépekkel, a tárolással és a App Serviceekkel kapcsolatos problémákat.
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f5c223e08207518bde315725fd69ddb3fb97a578
ms.sourcegitcommit: 74ce7c12a93d47315d70427b02bcacbd3b44f854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77037267"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Azure Stack hub hibáinak elhárítása

Ez a dokumentum a Azure Stack hub integrált környezetek hibaelhárítási információit tartalmazza. A Azure Stack Development Kit kapcsolatos segítségért lásd: [ASDK-hibaelhárítás](../asdk/asdk-troubleshooting.md) vagy Segítség kérése szakértőktől az [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ezek a témakörök a Microsoft terméktámogatási szolgálatának (CSS) eljuttatott gyakori kérdésekre mutató hivatkozásokat tartalmaznak.

### <a name="purchase-considerations"></a>Vásárlási szempontok

* [A vásárlás menete](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack hub áttekintése](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Frissítések és diagnosztika

* [Diagnosztikai eszközök használata az Azure Stack hub-ban](azure-stack-diagnostics.md)
* [Azure Stack hub rendszerállapotának ellenőrzése](azure-stack-diagnostic-test.md)
* [A csomag kiadási ritmusának frissítése](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>A vendég virtuális gépek által támogatott operációs rendszerek és méretek

* [Azure Stack hub által támogatott vendég operációs rendszerek](azure-stack-supported-os.md)
* [Azure Stack hub által támogatott virtuálisgép-méretek](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Piactér

* [Azure Stack hub számára elérhető Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Kapacitás kezelése

#### <a name="memory"></a>Memory (Memória)

Azure Stack hub teljes rendelkezésre álló memória-kapacitásának növeléséhez további memóriát adhat hozzá. Azure Stack hub-ban a fizikai kiszolgálót a skálázási egység csomópontjának is nevezzük. Az ugyanahhoz a skálázási egységhez tartozó skálázásiegység-csomópontokhoz [azonos mennyiségű memóriát](azure-stack-manage-storage-physical-memory-capacity.md) kell hozzárendelni.

#### <a name="retention-period"></a>Megőrzési időszak

A megőrzési időtartam beállítás lehetővé teszi, hogy a felhő operátora napokban határozzon meg egy időszakot (0 és 9999 nap között), amely alatt a törölt fiókok esetleg visszaállíthatók. Az alapértelmezett megőrzési időtartam **0** napra van állítva. Ha a **0** értéket állítja be, az azt jelenti, hogy a törölt fiókok azonnal megmaradnak, és meg vannak jelölve az időszakos szemét-gyűjtéshez.

* [Az adatmegőrzési időszak beállítása](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Biztonság, megfelelőség és identitás  

#### <a name="manage-rbac"></a>Az RBAC kezelése

Azure Stack hub egyik felhasználója lehet olvasó, tulajdonos vagy közreműködő az előfizetés, az erőforráscsoport vagy a szolgáltatás minden példánya számára.

* [Azure Stack hub RBAC kezelése](azure-stack-manage-permissions.md)

Ha az Azure-erőforrások beépített szerepkörei nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel.

* [Oktatóanyag: egyéni szerepkör létrehozása Azure-erőforrásokhoz Azure PowerShell használatával](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Használat és számlázás kezelése felhőszolgáltatóként

* [Használat és számlázás kezelése felhőszolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [CSP-vagy APSS-előfizetés létrehozása](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Válassza ki a Azure Stack hub-hoz használt megosztott szolgáltatási fiók típusát. A több-bérlős Azure Stack központ regisztrálásához használható előfizetések típusai a következők:

* Felhőalapú megoldás szolgáltatója
* Partner Shared Services-előfizetés

### <a name="get-scale-unit-metrics"></a>Méretezési egység metrikáinak beolvasása

A PowerShell használatával lekérheti a bélyegző kihasználtsági adatait a CSS súgója nélkül. A bélyegző kihasználtságának beszerzése:

1. Hozzon létre egy PEP-munkamenetet.
2. Futtassa az `test-azurestack` parancsot.
3. Lépjen ki a PEP-munkamenetből.
4. `get-azurestacklog -filterbyrole seedring` futtatása hívási parancs használatával.
5. Bontsa ki a seedring. zip fájlt. Az ellenőrzési jelentést a `test-azurestack`futtatott ERCS mappából szerezheti be.

További információ: [Azure stack hub Diagnostics](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="troubleshoot-virtual-machines-vms"></a>Virtuális gépek (VM-EK) hibáinak megoldása

### <a name="default-image-and-gallery-item"></a>Alapértelmezett rendszerkép és gyűjtemény elem

A virtuális gépek Azure Stack központban való üzembe helyezése előtt hozzá kell adni egy Windows Server-lemezképet és-gyűjteményi elemeket.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Töröltem néhány virtuális gépet, de továbbra is láthatók a lemezen lévő VHD-fájlok

Ennek a viselkedésnek a kialakítása a következő:

* Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. A lemezek különálló erőforrások az erőforráscsoporthoz.
* Ha a Storage-fiók törölve lesz, a törlés azonnal látható Azure Resource Manageron keresztül. Az esetlegesen tartalmazható lemezek azonban továbbra is a tárolóban maradnak, amíg a rendszer begyűjti a szemetet.

Ha "árva" virtuális merevlemezeket lát, fontos tisztában lennie azzal, hogy egy törölt Storage-fiók mappájában vannak-e. Ha a Storage-fiók nem lett törölve, akkor az normális, hogy még mindig ott vannak.

További információk az adatmegőrzési küszöbérték és az igény szerinti, a [Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)című témakörben olvashatók.

## <a name="troubleshoot-storage"></a>A tárolás hibaelhárítása

### <a name="storage-reclamation"></a>Tárhely-visszanyerés

Akár 14 órát is igénybe vehet a visszaigényelt kapacitás a portálon való megjelenítéséhez. A lemezterület-visszanyerés a különböző tényezőktől függ, például a belső tároló fájljainak használati százaléka a blob-tárolóban. Ezért attól függően, hogy mennyi adattal törli a rendszer, nem garantálható, hogy a rendszer mennyi helyet szabadít fel a Garbage Collector futtatásakor.

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>Azure Storage Explorer nem működik Azure Stack hub-vel

Ha egy integrált rendszer egy leválasztott forgatókönyvben van használatban, ajánlott vállalati hitelesítésszolgáltató (CA) használata. Exportálja a főtanúsítványt Base-64 formátumban, majd importálja Azure Storage Explorerba. Győződjön meg arról, hogy eltávolítja a záró perjelet (`/`) a Resource Manager-végpontból. További információ: [felkészülés a Azure stack hubhoz való csatlakozásra](/azure-stack/user/azure-stack-storage-connect-se).

## <a name="troubleshooting-app-service"></a>Hibaelhárítási App Service

### <a name="create-aadidentityappps1-script-fails"></a>A Create-AADIdentityApp. ps1 parancsfájl végrehajtása sikertelen

Ha a App Servicehoz szükséges Create-AADIdentityApp. ps1 parancsfájl nem sikerül, ügyeljen arra, hogy a parancsfájl futtatásakor a szükséges `-AzureStackAdminCredential` paramétert is tartalmazza. További információkért lásd: [az App Service üzembe helyezésének Előfeltételei Azure stack központban](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).
