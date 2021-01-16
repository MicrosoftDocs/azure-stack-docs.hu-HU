---
title: Áttelepítés Azure Stack HCI-re új hardveren
description: Ismerje meg, hogyan telepíthet át Azure Stack HCI-re új hardveren
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7a0c0ca7a99b3554b74cc80911acbee92793aa52
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254823"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>Áttelepítés Azure Stack HCI-re új hardveren

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Ez a témakör azt ismerteti, hogyan telepítheti át a virtuális gépek (VM) fájljait a Windows Server 2012 R2, a Windows Server 2016 vagy a Windows Server 2019 rendszerből a Windows PowerShell és a Robocopy használatával új Azure Stack HCI Server-hardverre. A Robocopy egy robusztus módszer a fájlok egyik kiszolgálóról a másikra történő másolásához. Folytatja, ha a kapcsolat megszakadt, és folytatja az utolsó ismert állapottal való munkát. A Robocopy a többszálas fájlok másolását is támogatja a kiszolgálói üzenetblokk (SMB) protokollon keresztül. További információ: [Robocopy](/windows-server/administration/windows-commands/robocopy).

> [!NOTE]
> A Hyper-V Élő áttelepítés és a Hyper-V replika a Windows Serverről a Azure Stack HCI-re nem támogatott.

Ha az áttelepíteni kívánt Windows 2012 R2 vagy régebbi verziójú virtuális gépekkel rendelkezik, tekintse meg a [régebbi virtuális gépek áttelepítése](#migrating-older-vms)című témakört.

Ha ugyanazokkal a hardverekkel szeretne áttérni Azure Stack HCI-re, tekintse [meg az áttelepítés Azure stack HCI-re ugyanazon a hardveren](migrate-cluster-same-hardware.md)című témakört.

A következő ábra egy Windows Server forrás-fürtöt és egy Azure Stack HCI-fürtöt mutat be példaként. Önálló kiszolgálókon is áttelepítheti a virtuális gépeket is.

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="Fürt migrálása Azure Stack HCI-be" lightbox="media/migrate/migrate-cluster.png":::

A várható állásidő tekintetében egyetlen hálózati ADAPTERt használ egy kettős 40 GB-os RDMA East-West hálózattal a fürtök között, és a Robocopy a 32-es 1,9 több szálra van konfigurálva.

> [!NOTE]
> A virtuális gépeknek a kiterjesztett fürtökre történő áttelepítését ebben a cikkben nem tárgyalja.

## <a name="before-you-begin"></a>Előkészületek

Az áttelepítés megkezdése előtt több követelményt és megfontolandó szempontot kell figyelembe venni:

- Minden Windows PowerShell-parancsot rendszergazdaként kell futtatni.

- A forrás-és a célkiszolgáló esetében rendszergazdai engedélyekkel rendelkező tartományi hitelesítő adatokkal kell rendelkeznie, és a forrás és a cél szervezeti egység (OU) teljes jogosultsággal kell rendelkeznie, amely mindkét fürtöt tartalmazza.

- Mindkét fürtnek ugyanabban a Active Directory erdőben és tartományban kell lennie, hogy megkönnyítse a fürtök közötti Kerberos-hitelesítést a virtuális gépek áttelepítéséhez.

- Mindkét fürtnek egy olyan Active Directory szervezeti egységben kell lennie, amelyen a szervezeti egységben a Csoportházirend Object (GPO) blokk öröklődése van beállítva. Ez biztosítja, hogy a tartományi szintű csoportházirend-objektumok és biztonsági házirendek ne befolyásolják az áttelepítést.

- Mindkét fürtnek ugyanahhoz az időforráshoz kell csatlakoznia a fürtök közötti konzisztens Kerberos-hitelesítés támogatásához.

- Jegyezze fel a Hyper-V virtuális kapcsoló nevét, amelyet a forrás-fürtön található virtuális gépek használnak. A virtuális gépek importálása előtt ugyanazt a virtuális kapcsolót kell használnia a Azure Stack HCI-célként megadott "virtuálisgép-hálózat" nevű fürthöz.

- Távolítsa el a forrásként szolgáló virtuális gépekhez tartozó összes ISO-képfájlt. Ezt a Hyper-V kezelőjével végezheti el a **virtuális gép tulajdonságainál** a **hardver szakaszban**. Válassza az **Eltávolítás** lehetőséget a virtuális CD-/DVD-meghajtókhoz.

- A forráskiszolgálón lévő összes virtuális gép leállítása. Erre azért van szükség, hogy az áttelepítési folyamat során megmaradjon a verziókövetés és az állapot.

- Ellenőrizze, hogy Azure Stack HCI támogatja-e a virtuális gépek verzióját a virtuális gépek igény szerinti importálására és frissítésére. Ennek módjáról a [virtuális gépek verziójának támogatása és frissítése](#vm-version-support-and-update) című szakaszban olvashat.

- A forráskiszolgálón lévő összes virtuális gép biztonsági mentése. Fejezze be az összes alkalmazás és szolgáltatás összeomlás-konzisztens biztonsági mentését, valamint az összes adatbázis alkalmazás-konzisztens biztonsági mentését. Az Azure-ba történő biztonsági mentéshez lásd: [Azure Backup használata](../manage/use-azure-backup.md).

- Készítsen ellenőrzőpontot a forrásoldali fürt virtuális gépei és a tartományvezérlő számára abban az esetben, ha vissza kell állítania egy korábbi állapotot. Ez a fizikai kiszolgálók esetében nem alkalmazható.

- Győződjön meg arról, hogy a maximális Jumbo-keret mérete azonos a forrás-és a cél fürtöt tároló hálózatok között, különösen a RDMA hálózati adapterek és a hozzájuk tartozó hálózati portok a leghatékonyabb végpontok közötti adatátviteli csomagok méretének biztosításához.

- Jegyezze fel a Hyper-V virtuális kapcsoló nevét a forrás-fürtön. Ezt újra fel fogja használni a célszámítógépen.

- A Azure Stack HCI-hardvernek a forrás hardveréhez legalább az egyenlő kapacitással és konfigurációval kell rendelkeznie.

- Csökkentse a hálózati ugrások számát vagy a forrás-és a célkiszolgáló közötti fizikai távolságot a leggyorsabb fájlátvitel elősegítése érdekében.

## <a name="vm-version-support-and-update"></a>A VM verziójának támogatása és frissítése

Ez a táblázat a Windows Server operációsrendszer-verzióit és azok virtuálisgép-verzióit sorolja fel.

Függetlenül attól, hogy a virtuális gép operációs rendszerének milyen verziója fut a rendszeren, az Azure Stack HCI-re való közvetlen áttelepítéshez támogatott minimális virtuálisgép-verzió 5,0-as verzió. Ez a Windows Server 2012 R2 rendszerű virtuális gépek alapértelmezett verzióját jelöli. Ezért az 2,0, 3,0 vagy 4,0 verziónál régebbi virtuális gépeket is frissíteni kell az 5,0-es verzióra az áttelepítés előtt.

|Operációs rendszer verziója|VM-verzió|
|---|---|
|Windows Server 2008 SP1|2,0|
|Windows Server 2008 R2|3,0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5,0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

A Windows Server 2012 R2, a Windows Server 2016 és a Windows Server 2019 rendszerű virtuális gépek esetében először frissítse a forrás hardveren támogatott legújabb VM-verziót a Robocopy áttelepítési parancsfájl futtatása előtt. Ez biztosítja, hogy az összes virtuális gép legalább a 5,0-es verzióban legyen sikeres virtuális gépek importálása esetén.

A Windows Server 2008 SP1, a Windows Server 2008 R2-SP1 és a Windows 2012 rendszerű virtuális gépek esetében a virtuális gép verziója kevesebb, mint 5,0. Ezek a virtuális gépek. vcmx-fájl helyett. xml fájlt is használnak a konfigurációhoz. Így a virtuális gép közvetlen importálása Azure Stack HCI-re nem támogatott. Ezekben az esetekben két lehetőség közül választhat, amelyek a [régebbi virtuális gépek áttelepítését](#migrating-older-vms)részletezik.

### <a name="updating-the-vm-version"></a>A virtuális gép verziójának frissítése

A következő parancsok a Windows Server 2012 R2 és újabb rendszerekre vonatkoznak. A következő parancs használatával jelenítheti meg az összes virtuálisgép-verziót egyetlen kiszolgálón:

```powershell
Get-VM * | Format-Table Name,Version
```

Az összes virtuális gép verziószámának megjelenítése a fürt összes kiszolgálóján:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Az összes virtuális gép frissítése a legújabb támogatott verzióra az összes kiszolgálón:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>RDMA javaslatok

Ha távoli közvetlen memória-hozzáférést (RDMA) használ, a Robocopy kihasználhatja a virtuális gépek fürtök közötti másolásához. Íme néhány javaslat a RDMA használatához:

- Csatlakoztasson mindkét fürtöt ugyanahhoz a rack (ToR) kapcsolóhoz a forrás-és a célkiszolgáló közötti leggyorsabb hálózati útvonal használatához. A tárolási hálózati elérési út esetében ez általában támogatja a 10 GbE/25GbE vagy a nagyobb sebességet, és kihasználja a RDMA.

- Ha a RDMA adapter vagy a standard eltér a forrás-és a célkiszolgáló (ROCE vs iWARP) között, a Robocopy Ehelyett TCP/IP protokollon keresztüli SMB-t használ a leggyorsabb elérhető hálózaton keresztül. Ez általában egy kettős 10 GbE/25Gbe vagy nagyobb sebességű a East-West hálózat számára, amely a legoptimálisabb módszert biztosítja a virtuális gépek VHDX-fájljainak fürtök közötti másolásához.

- Annak biztosítása érdekében, hogy a Robocopy képes legyen a fürtök (kelet-nyugati hálózat) közötti RDMA kihasználni, konfigurálja a RDMA, hogy azok a forrás-és a cél fürtök között legyenek irányíthatók.

## <a name="create-the-new-cluster"></a>Az új fürt létrehozása

Az Azure Stack HCI-fürt létrehozása előtt telepítenie kell a Azure Stack HCI operációs rendszert minden olyan új kiszolgálóra, amely a fürtben lesz. Ennek módjáról az [Azure stack HCI operációs rendszer központi telepítése](operating-system.md)című témakörben olvashat bővebben.

Az új fürt létrehozásához használja a Windows felügyeleti központot vagy a Windows PowerShellt. Ennek módjával kapcsolatos részletes információkért lásd: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központban](create-cluster.md) és [egy Azure stack HCI-fürt létrehozása a Windows PowerShell használatával](create-cluster-powershell.md).

> [!IMPORTANT]
> A fürtök közötti Hyper-V virtuális kapcsoló ( `VMSwitch` ) nevének meg kell egyeznie. Győződjön meg arról, hogy a célként megadott fürtön létrehozott virtuális kapcsolók nevei megegyeznek a forrás-fürtön az összes kiszolgálón használt nevekkel. A virtuális gépek importálása előtt ellenőrizze a kapcsolók nevét.

> [!NOTE]
> Ahhoz, hogy új virtuális gépeket hozna létre, regisztrálnia kell az Azure Stack HCI-fürtöt az Azure-ban. További információ: [regisztráció az Azure](register-with-azure.md)-ban.

## <a name="run-the-migration-script"></a>Az áttelepítési parancsfájl futtatása

A következő PowerShell-parancsfájl a `Robocopy_Remote_Server_.ps1` Robocopy eszközt használja a virtuálisgép-fájlok és a hozzájuk tartozó könyvtárak és metaadatok másolásához a forrásról a cél fürtre. Ezt a szkriptet az eredeti szkriptből módosították a TechNet címen a következő helyen: [Robocopy fájlok a távoli kiszolgálóra a PowerShell és a Robocopy használatával](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154).

A parancsfájl átmásolja az összes virtuális merevlemez-, VHDX-és VMCX-fájlt a célkiszolgálóra egy adott Fürt megosztott kötete (CSV) számára. Egyszerre egy CSV-t telepítünk át.

Az áttelepítési parancsfájl helyileg fut az egyes forráskiszolgálón, hogy kihasználja a RDMA és a gyors hálózati átvitel előnyeit. Ehhez tegye a következőket:

1. Győződjön meg arról, hogy minden cél fürtcsomópont a CSV-tulajdonosra van beállítva a cél CSV-fájlhoz.

1. Az összes másolandó VM-és VHDX-fájl helyének meghatározásához használja a következő parancsmagot. Tekintse át a `C:\vmpaths.txt` fájlt, hogy meghatározza a Robocopy parancs legfelső forrásfájl-elérési útját a 4. lépés:

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > Ha a VHD-és a VHDX-fájlok ugyanazon a köteten különböző elérési utakon találhatók, a másoláshoz minden más elérési útra el kell végeznie az áttelepítési parancsfájlt.

1. Módosítsa a következő három változót úgy, hogy az megfeleljen a fürt virtuálisgép-elérési útjának a célként megadott fürt virtuálisgép-elérési útjával:

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. Futtassa a következő parancsfájlt minden Windows Server forráskiszolgálón:

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

## <a name="import-the-vms"></a>A virtuális gépek importálása

Az ajánlott eljárás az, hogy a fürt csomópontjain hozzon létre legalább egy Fürt megosztott kötete (CSV), hogy az egyes CSV-tulajdonosok számára egyenletes egyensúlyt biztosítson a virtuális gépek számítási feladatainak nagyobb rugalmassága, teljesítménye és méretezése érdekében. Alapértelmezés szerint ez az egyenleg öt percenként automatikusan megtörténik, és figyelembe kell venni, amikor a Robocopy a forrás-és a cél fürt csomópontja között van, hogy a forrás és a cél CSV-tulajdonosa megfelel a legoptimálisabb átviteli útvonalnak és sebességnek.

A virtuális gépek importálásához hajtsa végre az alábbi lépéseket a Azure Stack HCI-fürtön, hogy azok elérhetők legyenek, és indítsa el őket:

1. Futtassa a következő parancsmagot az összes CSV tulajdonosi csomópont megjelenítéséhez:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Az egyes kiszolgálói csomópontok esetében válassza `C:\Clusterstorage\Volume` a lehetőséget, és adja meg az elérési utat az összes virtuális gép számára – például: `C:\Clusterstorage\volume01` .

1. Futtassa a következő parancsmagot az egyes CSV-tulajdonosi csomópontokon, hogy a virtuális gép összes VMCX-fájljának elérési útja megjelenjen a virtuálisgép-importálás előtt. Módosítsa az elérési utat a környezetének megfelelően:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

    > [!NOTE]
    > A Windows Server 2012 R2 és a régebbi virtuális gépek egy VCMX-fájl helyett XML-fájlt használnak. További információk: a **régebbi virtuális gépek áttelepítése** című szakasz.

1. Futtassa az alábbi parancsmagot minden egyes kiszolgáló-csomóponton, hogy a virtuális gépeket minden egyes CSV-tulajdonosi csomóponton importálja, regisztrálja és tegye elérhetővé. Ez biztosítja a virtuális gépek egyenletes eloszlását a processzor és a memória optimális kiosztása érdekében:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Minden cél virtuális gép elindítása minden csomóponton:

    ```powershell
    Start-VM -Name
    ```

1. Jelentkezzen be, és ellenőrizze, hogy az összes virtuális gép fut-e, és hogy az összes alkalmazás és szolgáltatás elérhető-e:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Frissítse a virtuális gépeket Azure Stack HCI legújabb verziójára, hogy kihasználhassa az összes előrelépést:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. A parancsfájl befejeződése után ellenőrizze a Robocopy naplófájlját a felsorolt hibáknál, és ellenőrizze, hogy az összes virtuális gép másolása sikeresen megtörtént-e.

## <a name="migrating-older-vms"></a>Régebbi virtuális gépek áttelepítése

Ha a Windows Server 2008 SP1, a Windows Server 2008 R2-SP1, a Windows Server 2012 vagy a Windows Server 2012 R2 rendszerű virtuális gépek vannak, akkor ez a szakasz Önre vonatkozik. A virtuális gépek kezelésére két lehetőség áll rendelkezésre:

- Telepítse át ezeket a virtuális gépeket a Windows Server 2012 R2, a Windows Server 2016 vagy a Windows Server 2019 rendszerre először, frissítse a virtuális gép verzióját, majd kezdje el az áttelepítési folyamatot.

- A Robocopy paranccsal másolja az összes virtuális merevlemezt Azure Stack HCI-re. Ezután hozzon létre új virtuális gépeket, és csatlakoztassa a másolt virtuális merevlemezeket Azure Stack HCI-ben lévő virtuális gépekhez. Ezzel megkerüli a virtuális gépek verziójának korlátozását a régebbi virtuális gépek esetében.

A Windows Server 2012 R2 és a régebbi Hyper-V-gazdagépek egy XML-fájlformátumot használnak a virtuálisgép-konfigurációhoz, ami eltér a Windows Server 2016 és újabb rendszerű Hyper-V-gazdagépekhez használt VCMX-fájlformátumtól. Ehhez egy másik Robocopy-parancsra van szükség a virtuális gépek Azure Stack HCI-re való másolásához.

### <a name="option-1-staged-migration"></a>1. lehetőség: előkészített áttelepítés

Ez a Windows Server 2008 SP1, a Windows Server 2008 R2-SP és a Windows Server 2012 rendszerekben üzemeltetett virtuális gépek kétfázisú áttelepítése. A folyamat a következőképpen használható:

1. Fedezze fel az összes másolandó VM-és VHDX-fájl helyét, majd tekintse át a `vmpaths.txt` fájlt, és határozza meg a Robocopy által a legfelső forrásfájl elérési útját. Használja a következő parancsmagot:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. A következő példában a Robocopy parancs használatával másolja a virtuális gépeket a Windows Server 2012 R2-re az 1. lépésben meghatározott legfelső elérési úttal.

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Ellenőrizze, hogy a `VMSwitch` Windows server 2012 R2-fürtön használt virtuális kapcsoló () neve megegyezik-e a windows 2008 R2 vagy a Windows server 2008 R2-SP1 forrásban használt kapcsoló nevével. A fürt összes kiszolgálóján használt kapcsolók nevének megjelenítéséhez használja a következőt:

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    Szükség szerint nevezze át a kapcsoló nevét a Windows Server 20212 R2-re. A kapcsoló nevének a fürt összes kiszolgálójára történő átnevezéséhez használja a következőt:

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. A virtuális gépek másolása és importálása Windows Server 2012 R2-re:

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Windows Server 2012 R2 rendszeren frissítse a virtuálisgép-verziót 5,0-re az összes virtuális GÉPEN:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [Futtassa az áttelepítési parancsfájlt](#run-the-migration-script) a virtuális gépek Azure stack HCI-re másolásához.

1. Az XML-fájlok kezeléséhez és a virtuális gépek Azure Stack HCI-re történő importálásához kövesse a [virtuális gépek importálása](#import-the-vms), a 3. lépés és a 4. lépés közötti eljárást.

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Végezze el a [virtuális gépek importálása](#import-the-vms)folyamatban lévő további lépéseket.

### <a name="option-2-direct-vhd-copy"></a>2. lehetőség: a VHD-fájl közvetlen másolása

Ez a módszer a Robocopy használatával másolja a Windows 2008 SP1, a Windows 2008 R2-SP1 és a Windows 2012 rendszerű virtuális merevlemezeket a HCI Azure Stack. Ezzel a lépéssel megkerüli a virtuális gépek minimálisan támogatott verziójának korlátozását. Ezt a lehetőséget javasoljuk a Windows Server 2008 SP1 és a Windows Server 2008 R2 – SP1 rendszerű virtuális gépeken.

A Windows 2008 SP1 és a Windows 2008 R2-SP1 rendszerben üzemeltetett virtuális gépek csak 1. generációs virtuális gépeket támogatnak 1. generációs VHD-k esetén. Ennek megfelelően a megfelelő 1. generációs virtuális gépeket létre kell hozni Azure Stack HCI-ben, hogy a másolt virtuális merevlemezek az új virtuális gépekhez is csatlakoztathatók legyenek. Vegye figyelembe, hogy ezek a VHD-k nem frissíthetők a 2. generációs virtuális merevlemezekre.

> [!NOTE]
> A Windows Server 2012 az 1. és a 2. generációs virtuális gépeket is támogatja.

A folyamat a következőképpen használható:

1. A következő példában a Robocopy paranccsal másolja át a virtuális gépek virtuális merevlemezeit közvetlenül Azure Stack HCI-be:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Hozzon létre új 1. generációs virtuális gépeket. Ennek módjával kapcsolatos részletes információkat a [virtuális gépek kezelése](../manage/vm.md)című témakörben talál.

1. Csatolja a másolt VHD-fájlokat az új virtuális gépekhez. Részletes információk: [virtuális merevlemezek kezelése (VHD)](/windows-server/storage/disk-management/manage-virtual-hard-disks)

A következő Windows Server vendég operációs rendszerek támogatják a 2. generációs virtuális gépeket:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- a Windows 8,1 64 bites verziói (64-bit)
- 64 – a Windows 8 bites verziói (64 bites)
- Linux (lásd [a támogatott Linux és FreeBSD rendszerű virtuális gépeket](/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows))

## <a name="next-steps"></a>További lépések

- A fürt érvényesítése az áttelepítés után. Lásd: [Azure stack HCI-fürt ellenőrzése](validate.md).

- Ha ugyanezt a hardvert használva Azure Stack HCI-re kíván áttérni, tekintse [meg az áttelepítés Azure stack HCI-re ugyanazon a hardveren](migrate-cluster-same-hardware.md)című témakört.