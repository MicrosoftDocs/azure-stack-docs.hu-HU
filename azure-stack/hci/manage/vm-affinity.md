---
title: Virtuálisgép-affinitási szabályok beállítása a Windows PowerShell használatával
description: Megtudhatja, hogyan állíthat be virtuálisgép-affinitási szabályokat a Windows PowerShell használatával
author: v-dasis
ms.topic: how-to
ms.date: 10/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e2cb3fa0efb11664924431ed0434547c832ceab4
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060157"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>Kiszolgáló-és hely-affinitási szabályok létrehozása a virtuális gépekhez

> A következőre vonatkozik Azure Stack HCI, Version 20H2

A Windows felügyeleti központ vagy a Windows PowerShell használatával egyszerűen hozhat létre affinitási és affinitási szabályokat a fürtben lévő virtuális gépekhez (VM).

Az affinitás olyan szabály, amely kapcsolatot létesít két vagy több erőforráscsoport vagy szerepkör, például virtuális gépek (VM-EK) között, hogy azok együtt maradjanak ugyanazon a kiszolgálón, fürtön vagy helyen. Az affinitás az ellenkezője annak, hogy a megadott virtuális gépek vagy erőforráscsoportok egymástól egymástól távol maradnak, például két tartományvezérlő külön kiszolgálókon vagy külön helyeken, a vész-helyreállítás érdekében.

Az affinitási és az affinitási szabályok ugyanúgy használatosak, mint ahogy az Azure a Availability Zones-t használja. Az Azure-ban beállíthatja, hogy Availability Zones, hogy a virtuális gépek külön zónában maradjanak, és egymástól, vagy ugyanabban a zónában maradjanak egymással.  

Az affinitás és az affinitási szabályok használata esetén a fürtözött virtuális gépek bármelyike ugyanabban a fürtcsomóponton marad, vagy nem lehet együtt ugyanabban a fürtcsomóponton.  Így a virtuális gépek egy csomóponton kívülre való áthelyezésének egyetlen módja a manuális elvégzése.  A virtuális gépeket a saját tárolóval együtt is megtarthatja, például a VHDX található Fürt megosztott kötete (CSV).

Az affinitás és az affinitási szabályok kombinálásával egy kibővített fürtöt is beállíthat két hely között, és megtarthatja a virtuális gépeket a helyen.

## <a name="using-windows-admin-center"></a>A Windows felügyeleti központ használata

Alapszintű affinitási és affinitási szabályokat hozhat létre a Windows felügyeleti központban.

:::image type="content" source="media/vm-affinity/vm-affinity-rules.png" alt-text="Virtuális gépek képernyő" lightbox="media/vm-affinity/vm-affinity-rules.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat**területén válassza ki azt a kiszolgálót vagy fürtöt, amelyhez létre szeretné hozni a virtuálisgép-szabályt.
1. Az **eszközök**területen válassza a **Beállítások**lehetőséget.
1. A **Beállítások**területen válassza az **affinitási szabályok**lehetőséget, majd válassza a **szabály létrehozása** az **affinitási szabályok**alatt lehetőséget.
1. A **szabály neve**alatt adja meg a szabály nevét.
1. A szabály típusa területen válassza a **közösen (azonos kiszolgáló)** vagy a **(különböző kiszolgálókon)** lehetőséget, hogy a virtuális gépeket ugyanazon a kiszolgálón vagy különböző kiszolgálókon helyezze el.
1. A **hatókör**területen válassza ki azokat a virtuális gépeket, amelyekre ez a szabály vonatkozik. A **Hozzáadás** gomb használatával további virtuális gépeket adhat hozzá a szabályhoz.
1. Ha elkészült, kattintson a **szabály létrehozása**elemre.
1. Egy szabály törléséhez egyszerűen jelölje ki, majd kattintson a **szabály törlése**elemre.

## <a name="using-windows-powershell"></a>A Windows PowerShell használata

A Windows PowerShell használatával összetettebb szabályokat hozhat létre, mint a Windows felügyeleti központot. A szabályokat általában egy távoli számítógépről kell beállítania, nem pedig a fürtben lévő gazdagépen. Ezt a távoli számítógépet felügyeleti számítógépnek nevezzük.

Ha felügyeleti számítógépről futtat Windows PowerShell-parancsokat, a vagy a `-Name` `-Cluster` paramétert adja meg a kezelt fürt nevével. Ha szükséges, meg kell adnia a teljes tartománynevet (FQDN) is, ha a `-ComputerName` paramétert használja a kiszolgálói csomóponthoz.

### <a name="new-powershell-cmdlets"></a>Új PowerShell-parancsmagok

A fürtökhöz tartozó affinitási szabályok létrehozásához használja az alábbi új PowerShell-parancsmagokat:

#### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

A **`New-ClusterAffinityRule`** parancsmag új szabályok létrehozására szolgál.  Ezzel a paranccsal megadhatja a szabály nevét, valamint a szabály típusát, ahol:

**`-Name`** a szabály neve.

**`-RuleType`** értékek `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`

Példa:

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

#### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

A **`Set-ClusterAffinityRule`** parancsmag egy szabály engedélyezésére vagy letiltására szolgál, ahol:

**`-Name`** az engedélyezni vagy letiltani kívánt szabály neve.

**`-Enabled`** | **`Disabled`** engedélyezheti vagy letilthatja a szabályt

Példa:

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

#### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

A **`Get-ClusterAffinityRule`** parancsmag a megadott szabály és a típus megjelenítésére szolgál.  Ha **`-Name`** nincs megadva, a rendszer az összes szabályt listázza.

Példa:

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

A parancsmag egy virtuálisgép- **`Add-ClusterGroupToAffinityRule`** szerepkör vagy-csoportnév hozzáadására szolgál egy adott affinitási szabályhoz, ahol:

**`-Groups`** a szabályhoz hozzáadandó csoport vagy szerepkör neve

**`-Name`** a hozzáadni kívánt szabály neve

Példa:

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

#### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

A **`Add-ClusterSharedVolumeToAffinityRule`** lehetővé teszi, hogy a virtuális gépek együtt maradjanak a VHDX található fürt megosztott kötete, ahol:

**`-ClusterSharedVolumes`** a szabályhoz hozzáadni kívánt CSV-lemez (ek)

**`-Name`** a hozzáadni kívánt szabály neve

Példa:

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

#### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

A **`Remove-ClusterAffinityRule`** törli a megadott szabályt, ahol a a **`-Name`** szabály neve.

Példa:

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

A eltávolítja a virtuálisgép- **`Remove-ClusterGroupFromAffinityRule`** csoportot vagy szerepkört egy adott szabályból, de nem tiltja le vagy nem törli a szabályt, ahol:

**`-Name`** a szabály neve.

**`-Groups`** azok a csoportok vagy szerepkörök, amelyeket el szeretne távolítani a szabályból.

Példa:

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

#### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

A **`Remove-ClusterSharedVolumeFromAffinityRule`** parancsmag használatával eltávolíthatja a fürt megosztott köteteit egy adott szabályból, de nem tiltja le vagy nem törli a szabályt, ahol:

**`-ClusterSharedVolumes`** a szabályból eltávolítani kívánt CSV-lemez (ek)

**`-Name`** a hozzáadni kívánt szabály neve

Példa:

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="existing-powershell-cmdlets"></a>Meglévő PowerShell-parancsmagok

Az új parancsmagok megjelenése után további új kapcsolók is bővültek néhány meglévő parancsmaggal.

#### <a name="move-clustergroup"></a>Move-ClusterGroup

Az új `-IgnoreAffinityRule` kapcsoló figyelmen kívül hagyja a szabályt, és áthelyezi a fürtözött erőforráscsoportot egy másik fürtcsomópont-csomópontra. További információ erről a parancsmagról: [Move-clustergroup felsorolásban találhatók](/powershell/module/failoverclusters/move-clustergroup).

Példa:

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> Ha egy áthelyezési szabály érvényes (támogatott), a rendszer az összes érintett csoportot és szerepkört is áthelyezi.  Ha egy virtuális gép áthelyezése egy olyan szabályt ismer, amely még egyszer is megsérti a szabályt, akkor a `-IgnoreAffinityRule` kapcsoló használatával engedélyezze az áthelyezést. Ebben az esetben a rendszer a virtuális gép megsértésére vonatkozó figyelmeztetést jelenít meg. Ezután szükség szerint engedélyezheti a szabályt.

#### <a name="start-clustergroup"></a>Start-ClusterGroup

Az új `-IgnoreAffinityRule` kapcsoló figyelmen kívül hagyja a szabályt, és az aktuális helyen online állapotba hozza a fürtözött erőforráscsoportot. További információ erről a parancsmagról: [Start-clustergroup felsorolásban találhatók](/powershell/module/failoverclusters/start-clustergroup).

Példa:

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="affinity-rule-examples"></a>Példák affinitási szabályokra

Az affinitási szabályok olyan "közös" szabályok, amelyek ugyanazon a kiszolgálón, fürtön vagy helyen tárolják az erőforrásokat. Íme néhány gyakori forgatókönyv az affinitási szabályok beállításához.

### <a name="scenario-1"></a>1\. példa

Tegyük fel, hogy van egy SQL Server VM és egy webkiszolgáló virtuális gép. Ennek a két virtuális gépnek mindig ugyanazon a helyen kell maradnia, de nem feltétlenül kell a hely ugyanazon csomópontján lennie.  A használatával `SameFaultDomain` Ez lehetséges, ahogy az alábbi ábrán is látható:

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

A szabály és a konfigurálás módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a kimenet megtekintéséhez:

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>2\. példa

Használja ugyanazt a forgatókönyvet, kivéve azt, hogy a virtuális gépeknek ugyanazon a fürtcsomóponton kell lenniük, de nem feltétlenül ugyanazon a helyen. A használatával `SameNode` a következőképpen állíthatja be:

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

A szabály és a konfigurálás módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a kimenet megtekintéséhez:

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="anti-affinity-rule-examples"></a>Példa az affinitási szabályokra

Az affinitási szabályok olyan "Apart" szabályok, amelyek elkülönítik az erőforrásokat, és különböző kiszolgálókra, fürtökre vagy webhelyekre helyezik őket.

### <a name="scenario-1"></a>1\. példa
Két virtuális gép fut SQL Server ugyanazon a Azure Stack HCI többhelyes fürtön.  Minden virtuális gép sok memóriát, PROCESSZORt és tárterület-erőforrást használ.  Ha a két végpont ugyanazon a csomóponton fut, akkor a teljesítménnyel kapcsolatos problémákat okozhat a memóriában, a PROCESSZORban és a tárolási ciklusokban.  Ha a szabály típusaként egy affinitási szabályt használ `DifferentNode` , akkor ezek a virtuális gépek mindig a különböző fürtcsomópontok között maradnak.  

A példához tartozó parancsok a következőek:

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

A szabály és a konfigurálás módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a kimenet megtekintéséhez:

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>2\. példa

Tegyük fel, hogy van egy Azure Stack HCI kifeszített fürt két hellyel (tartalék tartományokkal). Két tartományvezérlőt szeretne megtartani külön helyeken. Ha szabály típusú affinitási szabályt használ `DifferentFaultDomain` , akkor ezek a tartományvezérlők mindig a különböző helyeken maradnak.  A példához tartozó parancsok a következőek:

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

A szabály és a konfigurálás módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a kimenet megtekintéséhez:

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="combined-rule-examples"></a>Példák egyesített szabályokra

Az affinitás és az affinitási szabályok kombinálásával könnyedén konfigurálhat különböző virtuálisgép-kombinációkat egy többhelyes fürtben.  Ebben az esetben minden helyhez három virtuális gép tartozik: SQL Server (SQL), webkiszolgáló (WEB) és tartományvezérlő (DC).  Mindegyik kombináció esetében használhatja az affinitási szabályokat, hogy mindegyiket `SameFaultDomain` ugyanazon a helyen tartsák.  Megadhatja az egyes helyek tartományvezérlőit az affinitási szabályokkal, és a tartományvezérlő `DifferentFaultDomain` virtuális gépeket külön helyeken is megtarthatja az alábbi ábrán látható módon:

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

A szabályok és azok konfigurálási módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a kapcsoló nélkül, és láthatja az `-Name` összes létrehozott szabályt és azok kimenetét.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>Tárolási affinitási szabályok

Egy virtuális gépet és annak VHDX is megtarthatja egy Fürt megosztott kötete (CSV) ugyanazon a fürtcsomóponton. Ez megtartja a CSV-átirányítás előfordulását, ami lelassíthatja a virtuális gépek indítását vagy leállítását.  A korábban összevont affinitás és affinitási forgatókönyv figyelembevételével megtarthatja az SQL virtuális gépet és a Fürt megosztott kötetet ugyanazon a fürtcsomóponton.  Ehhez használja a következő parancsokat:

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

A szabályok és azok konfigurálási módjának megtekintéséhez használja a **`Get-ClusterAffinityRule`** parancsmagot a-name kapcsoló nélkül, és tekintse meg a kimenetet.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kezelheti a virtuális gépeket. Lásd: [virtuális gépek kezelése Azure stack HCI-ben a Windows felügyeleti központ használatával](../manage/vm.md).
