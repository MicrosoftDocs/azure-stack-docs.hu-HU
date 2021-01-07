---
title: Azure Stack hub hibáinak megoldása
titleSuffix: Azure Stack
description: Megtudhatja, hogyan lehet elhárítani a Azure Stack hubot, beleértve a virtuális gépekkel, a tárolással és a App Serviceekkel kapcsolatos problémákat.
author: PatAltimore
ms.topic: article
ms.date: 12/10/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 12/10/2020
ms.openlocfilehash: 4c5117469e1a568252e23f2794996f8d367942af
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974251"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Azure Stack hub hibáinak elhárítása

Ez a dokumentum a Azure Stack hub integrált környezetek hibaelhárítási információit tartalmazza. A Azure Stack Development Kit kapcsolatos segítségért lásd: [ASDK-hibaelhárítás](../asdk/asdk-troubleshooting.md) vagy Segítség kérése szakértőktől az [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ezek a fejezetek olyan dokumentumokra mutató hivatkozásokat tartalmaznak, amelyek a Microsoft ügyfélszolgálata eljuttatott gyakori kérdésekre vonatkoznak.

### <a name="purchase-considerations"></a>A vásárláskor megfontolandó szempontok

* [A vásárlás menete](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Az Azure Stack Hub áttekintése](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Frissítések és diagnosztika

* [Diagnosztikai eszközök használata az Azure Stack hub-ban](./azure-stack-diagnostic-log-collection-overview.md)
* [Azure Stack hub rendszerállapotának ellenőrzése](azure-stack-diagnostic-test.md)
* [A csomag kiadási ritmusának frissítése](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Csomópont állapotának ellenőrzése és hibakeresése](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>A vendég virtuális gépek által támogatott operációs rendszerek és méretek

* [Az Azure Stack Hub által támogatott vendég operációs rendszerek](azure-stack-supported-os.md)
* [Azure Stack hub által támogatott virtuálisgép-méretek](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Piactér

* [Az Azure Stack Hubhoz elérhető Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Kapacitás kezelése

#### <a name="memory"></a>Memória

Azure Stack hub teljes rendelkezésre álló memória-kapacitásának növeléséhez további memóriát adhat hozzá. Azure Stack hub-ban a fizikai kiszolgálót a skálázási egység csomópontjának is nevezzük. Az ugyanahhoz a skálázási egységhez tartozó skálázásiegység-csomópontokhoz [azonos mennyiségű memóriát](azure-stack-manage-storage-physical-memory-capacity.md) kell hozzárendelni.

#### <a name="retention-period"></a>Megőrzési időszak

A megőrzési időtartam beállítás lehetővé teszi, hogy a felhő operátora napokban határozzon meg egy időszakot (0 és 9999 nap között), amely alatt a törölt fiókok esetleg visszaállíthatók. Az alapértelmezett megőrzési időtartam **0** napra van állítva. Ha a **0** értéket állítja be, az azt jelenti, hogy a törölt fiókok azonnal megmaradnak, és meg vannak jelölve az időszakos szemét-gyűjtéshez.

* [Az adatmegőrzési időszak beállítása](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Biztonság, megfelelőség és identitás  

#### <a name="manage-rbac"></a>Az RBAC kezelése

Azure Stack hub egyik felhasználója lehet olvasó, tulajdonos vagy közreműködő az előfizetés, az erőforráscsoport vagy a szolgáltatás minden példánya számára.

* [Azure Stack hub RBAC kezelése](azure-stack-manage-permissions.md)

Ha az Azure-erőforrások beépített szerepkörei nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel.

* [Oktatóanyag: egyéni szerepkör létrehozása Azure-erőforrásokhoz Azure PowerShell használatával](/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Használat és számlázás kezelése felhőszolgáltatóként

* [Használat és számlázás kezelése felhőszolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [CSP-vagy APSS-előfizetés létrehozása](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Válassza ki a Azure Stack hub-hoz használt megosztott szolgáltatási fiók típusát. A több-bérlős Azure Stack központ regisztrálásához használható előfizetések típusai a következők:

* Felhőszolgáltató
* Partner Shared Services-előfizetés

### <a name="get-scale-unit-metrics"></a>Méretezési egység metrikáinak beolvasása

A PowerShell használatával lekérheti a Stamp kihasználtsági információit a Microsoft ügyfélszolgálata súgója nélkül. A bélyegző kihasználtságának beszerzése:

1. Hozzon létre egy PEP-munkamenetet.
2. Futtassa az `test-azurestack` parancsot.
3. Lépjen ki a PEP-munkamenetből.
4. Futtassa `get-azurestacklog -filterbyrole seedring` a parancsot egy meghívó hívásával.
5. Bontsa ki a seedring. zip fájlt. Az ellenőrzési jelentést a futtatott ERCS mappából szerezheti be `test-azurestack` .

További információ: [Azure stack hub Diagnostics](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Virtuális gépek (VM-EK) hibáinak megoldása

### <a name="reset-linux-vm-password"></a>Jelszó visszaállítása Linux rendszerű virtuális gépen

Ha elfelejti a Linux rendszerű virtuális gép jelszavát, és a **jelszó alaphelyzetbe állítása** lehetőség nem működik a VMAccess-bővítménysel kapcsolatos problémák miatt, a következő lépések végrehajtásával állíthatja vissza:

1. Válassza ki a Linux rendszerű virtuális gépet helyreállítási virtuális gépként való használatra.

1. Jelentkezzen be a felhasználói portálra:
   1. Jegyezze fel a virtuális gép méretét, a hálózati adaptert, a nyilvános IP-címet, a NSG és az adatlemezeket.
   1. Állítsa le az érintett virtuális gépet.
   1. Távolítsa el az érintett virtuális gépet.
   1. Csatlakoztassa a lemezt az érintett virtuális gépről adatlemezként a helyreállítási virtuális gépen (ez eltarthat néhány percig, amíg a lemez elérhetővé válik).

1. Jelentkezzen be a helyreállítási virtuális gépre, és futtassa a következő parancsot:

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. Jelentkezzen be a felhasználói portálra:

   1. Válassza le a lemezt a helyreállítási virtuális gépről.
   1. Hozza létre újra a virtuális gépet a lemezről.
   1. Ügyeljen arra, hogy a nyilvános IP-címet az előző virtuális gépről vigye át, csatolja az adatlemezeket stb.


Elkészítheti az eredeti lemez pillanatképét is, és létrehozhat egy új lemezt, ahelyett, hogy közvetlenül az eredeti lemezen hajtja végre a módosításokat. További információt az alábbi témakörökben talál:

- [Új jelszó létrehozása](/azure/virtual-machines/troubleshooting/reset-password)
- [Lemez létrehozása pillanatképből](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [A gyökér jelszavának módosítása és alaphelyzetbe állítása](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>A licenc aktiválása sikertelen a Windows Server 2012 R2-ben a kiépítés során

Ebben az esetben a Windows nem aktiválja az aktiválást, és a képernyő jobb alsó sarkában egy vízjelet fog látni. A C:\Windows\Panther alatt található WaSetup.xml naplók a következő eseményt tartalmazzák:

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


A licenc aktiválásához másolja az aktiválni kívánt SKU automatikus virtuálisgép-aktiválási (AVMA) kulcsát.

|Kiadás|AVMA kulcs|
|-|-|
|Adatközpont|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Standard|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Alapvető erőforrások|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

Futtassa a következő parancsot a virtuális gépen:

```powershell
slmgr /ipk <AVMA_key>
```

A részletekért lásd: [virtuális gép aktiválása](/windows-server/get-started-19/vm-activation-19).

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

Ha egy integrált rendszer egy leválasztott forgatókönyvben van használatban, ajánlott vállalati hitelesítésszolgáltató (CA) használata. Exportálja a főtanúsítványt Base-64 formátumban, majd importálja Azure Storage Explorerba. Győződjön meg arról, hogy eltávolítja a záró perjelet ( `/` ) a Resource Manager-végpontból. További információ: [felkészülés a Azure stack hubhoz való csatlakozásra](../user/azure-stack-storage-connect-se.md).

## <a name="troubleshoot-app-service"></a>Hibakeresés App Service

### <a name="create-aadidentityappps1-script-fails"></a>Create-AADIdentityApp.ps1 szkript sikertelen

Ha a App Service szükséges Create-AADIdentityApp.ps1 parancsfájl nem sikerül, ügyeljen arra, hogy a `-AzureStackAdminCredential` parancsfájl futtatásakor a szükséges paramétert is tartalmazza. További információkért lásd: [az App Service üzembe helyezésének Előfeltételei Azure stack központban](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Azure Stack hub frissítéseinek hibáinak megoldása

Az Azure Stack hub javítási és frissítési folyamata úgy lett kialakítva, hogy a kezelők következetes és áramvonalas módon alkalmazzák a frissítési csomagokat. Habár nem gyakori, a javítások és a frissítési folyamat során problémák léphetnek fel. A javítási és frissítési folyamat során a következő lépések ajánlottak:

0. **Előfeltételek**: Győződjön meg arról, hogy követte a [frissítési tevékenység ellenőrzőlistáját](release-notes-checklist.md) , és engedélyezze az előjelzéses [naplók gyűjtését](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively).

1. Ha a frissítés sikertelen volt, kövesse a sikertelen riasztások által létrehozott hibaelhárítási lépéseket.

2. Ha nem tudta feloldani a problémát, hozzon létre egy [Azure stack hub támogatási jegyet](./azure-stack-help-and-support-overview.md). Győződjön meg arról, hogy a probléma előfordulásakor a rendszer a [naplókat gyűjti](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now) az időtartományhoz.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>A Azure Stack hub általános javítási és frissítési problémái

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

### <a name="preparationfailed"></a>PreparationFailed

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**OK**: az Azure stack hub frissítésének telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és az állapot módosul `PreparationFailed` . Internetkapcsolattal rendelkező rendszerek esetében ez általában azt jelzi, hogy a frissítési csomag nem tölthető le megfelelően, mert gyenge az internetkapcsolat. 

**Szervizelés**: a probléma megkerüléséhez kattintson a **Telepítés most** lehetőségre. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) szakaszt követve manuálisan töltse fel a frissítési csomagot.

**Előfordulás**: gyakori

### <a name="warnings-and-errors-reported-while-update-is-in-progress"></a>A frissítés során jelentett figyelmeztetések és hibák

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**OK**: Ha Azure stack hub frissítés állapota **folyamatban** van, a rendszer figyelmeztetéseket és hibákat jelez a portálon. Az összetevők időtúllépést okozhatnak a frissítés során más összetevők számára, így hiba történt. Az Azure Stack hub olyan mechanizmussal rendelkezik, amely időnkénti hibák miatt újrapróbálkozik vagy szervizeli a feladatokat.

**Szervizelés**: az Azure stack hub frissítésének állapota **folyamatban** van, a portálon jelentett figyelmeztetések és hibák figyelmen kívül hagyhatók.

**Előfordulás**: gyakori

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>2002 frissítés nem sikerült

**Alkalmazható**: Ez a probléma csak az 2002-es kiadásra vonatkozik.

**OK**: a 2002-es frissítés megkísérlése során előfordulhat, hogy a frissítés sikertelen lesz, és a következő üzenet jelenik meg: `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace` .

**Szervizelés**: [hozzon létre egy privát belső hálózatot](./azure-stack-network.md?view=azs-2002&preserve-view=true#private-network).
::: moniker-end