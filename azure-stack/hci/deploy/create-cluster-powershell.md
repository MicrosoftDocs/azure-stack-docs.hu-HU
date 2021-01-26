---
title: Azure Stack HCI-fürt létrehozása a Windows PowerShell használatával
description: Megtudhatja, hogyan hozhat létre fürtöt Azure Stack HCI-hez a Windows PowerShell használatával
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 2099d7e9dcd2d01f949d54ad5bd59ce06ecaccbc
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772200"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>Azure Stack HCI-fürt létrehozása a Windows PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI, Version v20H2

Ebből a cikkből megtudhatja, hogyan hozhat létre a Windows PowerShell használatával olyan Azure Stack HCI hiperkonvergens-fürtöt, amely Közvetlen tárolóhelyekt használ. Ha inkább a fürt létrehozása varázslót használja a Windows felügyeleti központban a fürt létrehozásához, tekintse meg [a fürt létrehozása a Windows felügyeleti központtal](create-cluster.md)című témakört.

Két fürt típusa közül választhat:

- Standard fürt legalább két kiszolgáló-csomóponttal, amelyek mindegyike egyetlen helyen található.
- A kinyújtott fürt legalább négy olyan kiszolgáló-csomóponttal rendelkezik, amely két-hely között két csomópontra terjed ki.

Ebben a cikkben egy Cluster1 nevű fürtöt hozunk létre, amely a Kiszolgáló1, a Kiszolgáló2, a Server3 és a Server4 nevű négy kiszolgáló-csomópontból áll.

A kiterjesztett fürt esetében a ClusterS1 nevet használjuk, és ugyanazokat a négy kiszolgáló-csomópontot használjuk, amelyek a hely1 és a Site2 között vannak kiterjesztve.

További információ a kifeszített fürtökről: a [kiterjesztett fürtök áttekintése](../concepts/stretched-clusters.md).

Ha érdekli a Azure Stack HCI kipróbálása, de korlátozott vagy nem rendelkezik tartalék hardverrel, tekintse meg a [Azure stack HCI-értékelési útmutatót](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), ahol áttekintheti a Azure stack HCI-t a beágyazott virtualizálás használatával, akár az Azure-ban, akár a helyszínen, akár egyetlen fizikai rendszeren.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg róla, hogy:

- Olvassa el a [Azure stack HCI rendszerkövetelményeit](../concepts/system-requirements.md).
- Olvassa el a Azure Stack HCI [fizikai hálózati követelményeit](../concepts/physical-network-requirements.md) és a [gazdagép hálózati követelményeit](../concepts/host-network-requirements.md) .
- Telepítse a Azure Stack HCI operációs rendszert a fürt minden kiszolgálóján. Lásd: [a Azure stack HCI operációs rendszer telepítése](operating-system.md).
- Olyan fiókkal kell rendelkeznie, amely tagja a helyi Rendszergazdák csoportnak az egyes kiszolgálókon.
- Objektumok létrehozásához Active Directory rendelkezik jogosultságokkal.
- A többhelyes fürtök esetében a két helyet előre kell beállítani Active Directoryban.

## <a name="using-windows-powershell"></a>A Windows PowerShell használata

Futtathatja a PowerShellt helyileg egy RDP-munkamenetben egy gazdagép-kiszolgálón, vagy távolról is futtathatja a PowerShellt egy felügyeleti számítógépről. Ez a cikk a távoli lehetőséget fogja lefedni.

Ha felügyeleti számítógépről futtatja a PowerShellt, a `-Name` vagy a paramétert adja meg a felügyelt `-Cluster` kiszolgáló vagy fürt nevével. Emellett előfordulhat, hogy meg kell adnia a teljes tartománynevet (FQDN), ha a `-ComputerName` paramétert használja a kiszolgálói csomóponthoz.

Szüksége lesz a Hyper-V és a feladatátvételi fürtszolgáltatás Távoli kiszolgálófelügyelet eszközei (RSAT) parancsmagjai és PowerShell-moduljaira is. Ha ezek még nem állnak rendelkezésre a PowerShell-munkamenetben a felügyeleti számítógépen, a következő paranccsal adhatja hozzá őket: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

## <a name="step-1-provision-the-servers"></a>1. lépés: a kiszolgálók kiépítése

Először csatlakozni fogunk a kiszolgálókhoz, csatlakoztatjuk őket egy tartományhoz (a felügyeleti számítógép azonos tartományához), és telepíteni kell a szükséges szerepköröket és szolgáltatásokat.

### <a name="step-11-connect-to-the-servers"></a>1,1. lépés: Kapcsolódás a kiszolgálókhoz

A kiszolgálókhoz való csatlakozáshoz először hálózati kapcsolattal kell rendelkeznie, ugyanahhoz a tartományhoz vagy teljesen megbízható tartományhoz kell csatlakoznia, és helyi rendszergazdai engedélyekkel kell rendelkeznie a kiszolgálókhoz.

Nyissa meg a PowerShellt, és használja vagy annak a kiszolgálónak a teljes tartománynevét vagy IP-címét, amelyhez csatlakozni szeretne. A következő parancs minden kiszolgálón való futtatása után a rendszer jelszót kér. 

Ebben a példában feltételezzük, hogy a kiszolgálók neve Kiszolgáló1, Kiszolgáló2, Server3 és Server4:

   ```powershell
   Enter-PSSession -ComputerName "Server1" -Credential "Server1\Administrator"
   ```

Íme egy másik példa arra, hogy ugyanezt a dolgot hajtja végre:

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> Ha PowerShell-parancsokat futtat a felügyeleti SZÁMÍTÓGÉPéről, előfordulhat, hogy hibaüzenetet kap, például *a WinRM nem tudja feldolgozni a kérést.* Ennek kijavításához a PowerShell használatával adja hozzá az egyes kiszolgálókat a felügyeleti számítógép megbízható gazdagépek listájához. Ez a lista támogatja a helyettesítő karaktereket ( `Server*` például:).
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> A megbízható gazdagépek listájának megtekintéséhez írja be a következőt: `Get-Item WSMAN:\Localhost\Client\TrustedHosts` .  
>
> A lista kiürítéséhez írja be a következőt: `Clear-Item WSMAN:\Localhost\Client\TrustedHost` .  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>1,2. lépés: csatlakozás a tartományhoz és tartományi fiókok hozzáadása

Eddig a helyi rendszergazdai fiókkal kapcsolódott az egyes kiszolgálók csomópontjaihoz `<ServerName>\Administrator` .

A folytatáshoz csatlakoztatnia kell a kiszolgálókat egy tartományhoz, és a helyi Rendszergazdák csoportban lévő tartományi fiókot kell használnia minden kiszolgálón.

A `Enter-PSSession` parancsmag használatával kapcsolódjon az egyes kiszolgálókhoz, és futtassa a következő parancsmagot, amely a kiszolgáló nevét, a tartománynevet és a tartományi hitelesítő adatokat helyettesíti:

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

Ha a rendszergazda fiókja nem tagja a Tartománygazdák csoportnak, adja hozzá a rendszergazdai fiókot a helyi Rendszergazdák csoporthoz az egyes kiszolgálókon – vagy még jobb, ha hozzáadja a rendszergazdák számára használt csoportot. Ehhez a következő parancsot használhatja:

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>1,3. lépés: szerepkörök és szolgáltatások telepítése

A következő lépés a szükséges Windows-szerepkörök és-szolgáltatások telepítése a fürt összes kiszolgálóján. A telepítendő szerepkörök a következők:

- BitLocker
- Adatközpont-áthidalás (RoCEv2 hálózati adapterek esetében)
- Feladatátvételi fürtszolgáltatás
- Fájlkiszolgáló
- FS – adatmásolási modul
- Hyper-V
- RSAT-AD-PowerShell modul
- Storage-replika (kifeszített fürtök esetében)

Használja az alábbi parancsot minden kiszolgálóhoz:

```powershell
Install-WindowsFeature -ComputerName "Server1" -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-AD-Powershell", "RSAT-Clustering-PowerShell", "Storage-Replica" -IncludeAllSubFeature -IncludeManagementTools
```

Ha a parancsot a fürt összes kiszolgálóján egyszerre szeretné futtatni, használja a következő parancsfájlt, és módosítsa a változók listáját az elején, hogy illeszkedjen a környezetéhez.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-AD-Powershell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist -IncludeAllSubFeature -IncludeManagementTools
}
```
Ezután indítsa újra az összes kiszolgálót:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList -WSManAuthentication Kerberos
```

## <a name="step-2-configure-networking"></a>2. lépés: a hálózatkezelés konfigurálása

Ez a lépés különböző hálózati elemeket, például virtuális kapcsolókat és hálózati adaptereket konfigurál a környezetében. A RDMA (iWARP és RoCE) hálózati adapterek is támogatottak.

További információ a RDMA és a Hyper-V gazdagép hálózatkezeléséről Azure Stack HCI esetében: a [gazdagép hálózati követelményei](../concepts/host-network-requirements.md).

### <a name="disable-unused-networks"></a>Nem használt hálózatok letiltása

Le kell tiltania minden olyan hálózatot, amely le van választva a felügyelethez, a tároláshoz vagy a munkaterhelés-forgalomhoz (például virtuális gépekhez). A használaton kívüli hálózatok azonosításának módja:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $ServerList | Where-Object Status -eq Disconnected
```
A következő módon tilthatja le őket:

```powershell
Get-NetAdapter -CimSession $ServerList | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="create-virtual-switches"></a>Virtuális kapcsolók létrehozása

A fürt minden egyes kiszolgáló-csomópontján szükség van egy virtuális kapcsolóra. A következő példában az SR-IOV képességgel rendelkező virtuális kapcsoló a csatlakoztatott hálózati adapterek használatával jön létre (az állapot fel van állítva). Az SR-IOV engedélyezve is hasznos lehet, mivel ez szükséges a RDMA-kompatibilis Vmnics;) (Vnic virtuális gépek esetén).

Minden hálózati adapternek azonosnak kell lennie az összevonási hálózati adapterek esetében.

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

És hozza létre a virtuális kapcsolót:

```powershell
Invoke-Command -ComputerName $ServerList -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

Most ellenőrizze, hogy a kapcsoló sikeresen létrejött-e:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $ServerList | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $ServerList
```

### <a name="assign-virtual-network-adapters"></a>Virtuális hálózati adapterek kiosztása

Ezután rendeljen hozzá virtuális hálózati adaptereket (Vnic) a felügyelethez és a többi forgalomhoz, ahogy az alábbi példában is látható. Legalább egy hálózati adaptert konfigurálnia kell a fürt felügyeletéhez.

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $ServerList
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $ServerList
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $ServerList
```

És ellenőrizze, hogy sikeresen hozzáadta és hozzárendelte-e a következőket:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $ServerList -ManagementOS
```

### <a name="configure-ip-addresses-and-vlans"></a>IP-címek és VLAN-ok konfigurálása

Egy vagy két alhálózatot is beállíthat. Két alhálózat használata javasolt, ha meg szeretné akadályozni a Switch Interconnect túlterhelését. Az SMB Storage-forgalom például egy fizikai kapcsolóhoz dedikált alhálózaton marad.

> [!NOTE]
> Az IP-címek konfigurálásakor a kapcsolat néhány percen belül megszakadhat, mivel az új IP-cím beszerzése során előfordulhat, hogy az egyik virtuális adapterhez kapcsolódott.

#### <a name="obtain-network-interface-information"></a>Hálózati adapter információinak beszerzése

A hálózati adapterek IP-címeinek beállítása előtt meg kell adnia néhány információt, például a csatoló indexét ( `ifIndex` ), `Interface Alias` és `Address Family` . Írja le ezeket az összes kiszolgáló-csomópontra, mert később szüksége lesz rájuk.

Futtassa a következőt:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -CimSession $ServerList
```

#### <a name="configure-one-subnet"></a>Egy alhálózat konfigurálása

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $ServerList){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $ServerList
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $ServerList
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $ServerList
```

#### <a name="configure-two-subnets"></a>Két alhálózat konfigurálása

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $ServerList){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $ServerList
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $ServerList
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>VLAN-azonosítók és alhálózatok ellenőrzése

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $ServerList -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $ServerList | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>3. lépés: a fürt telepítésének előkészítője

Ezután ellenőrizze, hogy a kiszolgálók készen állnak-e a fürtözésre. Először is ellenőrizze, hogy az alábbi parancsokat futtatja-e, és győződjön meg arról, hogy a kiszolgálók még nem tartoznak fürthöz:

`Get-ClusterNode`Az összes csomópont megjelenítéséhez használja a következőt:

```powershell
Get-ClusterNode
```

`Get-ClusterResource`Az összes fürtcsomópont megjelenítéséhez használja a következőt:

```powershell
Get-ClusterResource
```

`Get-ClusterNetwork`Az összes fürthálózat megjelenítéséhez használja a következőt:

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>3,1. lépés: meghajtók előkészítése

A Közvetlen tárolóhelyek engedélyezése előtt gondoskodjon arról, hogy a meghajtók üresek legyenek. Futtassa a következő szkriptet a régi partíciók vagy egyéb adatlemezek eltávolításához.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>3,2. lépés: a fürt konfigurációjának tesztelése

Ebben a lépésben meg kell győződnie arról, hogy a kiszolgáló-csomópontok megfelelően vannak konfigurálva a fürt létrehozásához. A `Test-Cluster` parancsmag a tesztek futtatására szolgál, hogy ellenőrizze, a konfiguráció alkalmas-e hiperkonvergens-fürtként való működésre. Az alábbi példa a `-Include` paramétert használja a megadott típusú tesztek alapján. Ez biztosítja, hogy a megfelelő tesztek szerepeljenek az ellenőrzésben.

```powershell
Test-Cluster –Node $ServerList –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>4. lépés: a fürt létrehozása

Most már készen áll arra, hogy létrehoz egy fürtöt az előző lépésekben érvényesített kiszolgáló-csomópontokkal.

A fürt létrehozásakor egy figyelmeztetés jelenik meg, amely szerint `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."` nyugodtan figyelmen kívül hagyhatja ezt a figyelmeztetést. Ez azért van, mert nem érhető el lemez a tanúsító fürthöz, amelyet később fog létrehozni. 

> [!NOTE]
> Ha a kiszolgálók statikus IP-címeket használnak, módosítsa az alábbi parancsot a statikus IP-cím megjelenítéséhez a következő paraméter hozzáadásával és az IP-cím megadásával: `–StaticAddress <X.X.X.X>;` .

```powershell
$ClusterName="cluster1" New-Cluster -Name $ClusterName –Node $ServerList –nostorage
```

A fürt már létrejött.

A fürt létrehozása után eltarthat egy ideig, amíg a fürt neve a DNS-en keresztül replikálódik a tartományon keresztül, különösen akkor, ha a munkacsoport-kiszolgálók újonnan lettek hozzáadva a Active Directoryhoz. Bár előfordulhat, hogy a fürt a Windows felügyeleti központban jelenik meg, előfordulhat, hogy még nem érhető el a kapcsolódás.

Ellenőrizze, hogy a fürt összes erőforrása online állapotban van-e:

```powershell
Get-Cluster -Name $ClusterName | Get-ClusterResource
```

Ha a fürt feloldása egy kis idő elteltével nem sikerül, a legtöbb esetben a fürt neve helyett a fürtözött kiszolgálók egyikének nevével lehet kapcsolatot létesíteni.

## <a name="step-5-set-up-sites-stretched-cluster"></a>5. lépés: helyek beállítása (kifeszített fürt)

Ez a feladat csak akkor érvényes, ha egy kifeszített fürtöt hoz létre két hely között. 

> [!NOTE]
> Ha korábban már beállította Active Directory helyeket és szolgáltatásokat, nem kell manuálisan létrehoznia a helyeket az alább leírtak szerint.

### <a name="step-51-create-sites"></a>5,1. lépés: helyek létrehozása

Az alábbi parancsmagban a *FaultDomain* egyszerűen egy hely egy másik neve. Ez a példa a "ClusterS1" kifejezést használja a kifeszített fürt neveként.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site1"
```

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site2"
```

A `Get-ClusterFaultDomain` parancsmag használatával ellenőrizze, hogy mindkét hely létrejött-e a fürthöz.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-52-assign-server-nodes"></a>5,2. lépés: kiszolgálói csomópontok kiosztása

Ezután a négy kiszolgáló-csomópontot rendeli hozzá a megfelelő helyekhez. Az alábbi példában a Kiszolgáló1 és a Kiszolgáló2 a hely1-hez van rendelve, míg a Server3 és a Server4 hozzá van rendelve a Site2.

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server1", "Server2" -Parent "Site1"
```

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server3", "Server4" -Parent "Site2"
```

A `Get-ClusterFaultDomain` parancsmag használatával ellenőrizze, hogy a csomópontok a megfelelő helyeken találhatók-e.

```powershell
Get-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-53-set-a-preferred-site"></a>5,3. lépés: előnyben részesített hely beállítása

Megadhat egy globális *előnyben részesített* helyet is, ami azt jelenti, hogy a megadott erőforrásoknak és csoportoknak az előnyben részesített helyen kell futniuk.  Ez a beállítás a hely szintjén definiálható a következő parancs használatával:  

```powershell
(Get-Cluster).PreferredSite = "Site1"
```

Az előnyben részesített helyek kiértékelése a következő előnyökkel jár:

- **Hideg indítás** – a rendszer az előnyben részesített helyre helyezi a virtuális gépeket

- **Kvórum szavazása**
  - A dinamikus kvórum használatával a súlyozás a passzív (replikált) helyről is csökken, így biztosítva, hogy az előnyben részesített hely megmaradjon, ha minden más dolog egyenlő. Emellett a kiszolgáló-csomópontok a passzív helyről is metszik a rendszer először az események, például aszimmetrikus hálózati csatlakozási hibák utáni Újracsoportosítás során.

  - Ha a kvórum nem tud kapcsolatba lépni a két hely között, akkor a rendszer automatikusan az előnyben részesített webhelyet választja. A passzív hely kiszolgálói csomópontjai elvesznek a fürt tagsága alól. Ez lehetővé teszi a fürt számára, hogy a szavazatok egyidejű 50%-os elvesztésével megmaradjanak.

Az előnyben részesített hely konfigurálható a fürt szerepkör vagy a csoport szintjén is. Ebben az esetben egy másik előnyben részesített hely konfigurálható az egyes virtuálisgép-csoportokhoz. Ez lehetővé teszi, hogy a hely aktív legyen, és az adott virtuális gépek számára legyen előnyben részesíteni.

## <a name="step-6-enable-storage-spaces-direct"></a>6. lépés: a Közvetlen tárolóhelyek engedélyezése

A fürt létrehozása után használja a (z) `Enable-ClusterStorageSpacesDirect` parancsmagot, amely engedélyezi a közvetlen tárolóhelyek, és automatikusan végrehajtja a következőket:

- **Tárolási készlet létrehozása:** Létrehoz egy olyan tárolót a fürthöz, amelynek neve például "Cluster1 Storage pool".

- **Fürt teljesítményének előzményei lemez létrehozása:** Létrehoz egy fürtözött teljesítménybeli virtuális lemezt a Storage-készletben.

- **Adatkezelési és naplózási kötetek létrehozása:** Létrehoz egy adatkötetet és egy naplózási kötetet a tárolóban.

- **Közvetlen tárolóhelyek gyorsítótárak konfigurálása:** Ha egynél több adathordozó (meghajtó) típus érhető el a Közvetlen tárolóhelyekhoz, akkor a leggyorsabb gyorsítótárazási eszköz (olvasás és írás a legtöbb esetben).

- **Rétegek létrehozása:** Két szintet hoz létre alapértelmezett szintként. Az egyik a Capacity (Kapacitás), a másik pedig a Performance (Teljesítmény) réteg lesz. A parancsmag elemzi az eszközöket, és beállítja az eszköztípusok és a rugalmasság megfelelő kombinációját az egyes rétegeken.

A kiterjesztett fürtök esetében a `Enable-ClusterStorageSpacesDirect` parancsmag a következőket is végrehajtja:

- Ellenőrizze, hogy a helyek beállítása megtörtént-e
- Határozza meg, hogy mely csomópontok mely helyeken találhatók
- Meghatározza, hogy az egyes csomópontok milyen tárterületet kínálnak
- Ellenőrzi, hogy a tárolási replika szolgáltatás telepítve van-e az egyes csomópontokon
- Létrehoz egy tárolási készletet az egyes helyekhez, és azonosítja azt a hely nevével
- Adatés naplózási kötetek létrehozása az egyes tárolóhelyeken – egy helyenként

A következő parancs engedélyezi a Közvetlen tárolóhelyek. A Storage-készlet rövid nevét is megadhatja, ahogy az itt látható:

```powershell
Enable-ClusterStorageSpacesDirect -PoolFriendlyName "$ClusterName Storage Pool" -CimSession $ClusterName
```

A tárolási készletek megtekintéséhez használja a következőt:

```powershell
Get-StoragePool -CimSession $session
```

Gratula, már létrehozott egy fürtöt.

## <a name="after-you-create-the-cluster"></a>A fürt létrehozása után

Most, hogy elkészült, még néhány fontos feladatot végre kell hajtania:

- Állítson be egy tanúsító fürtöt. Lásd: [tanúsító fürt beállítása](../manage/witness.md).
- Hozza létre a köteteket. Lásd: [kötetek létrehozása](../manage/create-volumes.md).
- A kiterjesztett fürtök esetében hozzon létre köteteket, és állítsa be a replikálást a Storage-replika használatával. Lásd: [kötetek létrehozása és replikáció beállítása a kifeszített fürtökhöz](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>További lépések

- Regisztrálja a fürtöt az Azure-ban. Lásd: az [Azure-regisztráció kezelése](../manage/manage-azure-registration.md).
- Végezze el a fürt végső érvényesítését. Lásd: [Azure stack HCI-fürt ellenőrzése](validate.md)
