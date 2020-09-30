---
title: Többcsatornás SMB kezelése
description: Ebből a témakörből megtudhatja, hogyan használhatja a többcsatornás SMB-t a nagyobb teljesítmény és a hálózati hibatűrés érdekében.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585912"
---
# <a name="manage-smb-multichannel"></a>Többcsatornás SMB kezelése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows 10

A többcsatornás SMB a Server Message Block (SMB) 3,0 protokoll része, amely növeli a hálózati teljesítményt és a fájlkiszolgálók rendelkezésre állását.

A többcsatornás SMB lehetővé teszi, hogy a fájlkiszolgálók egyszerre több hálózati kapcsolatot használjanak. Ez megkönnyíti a hálózati sávszélesség és a hálózati hibatűrés összesítését, ha több elérési út is elérhető az SMB 3,0-ügyfél és az SMB 3,0-kiszolgáló között. Ez lehetővé teszi a kiszolgálói alkalmazások számára, hogy teljes mértékben kihasználhassa az összes rendelkezésre álló hálózati sávszélességet, és rugalmasabban megnehezítik a hálózati hibákat.

A többcsatornás SMB a következő képességeket biztosítja:

- **Nagyobb átviteli sebesség.** A fájlkiszolgáló egyidejűleg több kapcsolat használatával is továbbíthat további adatokat a nagy sebességű hálózati adapterekhez vagy több hálózati adapterhez.

- **Hálózati hibatűrés.** Ha az ügyfelek egyidejűleg több hálózati kapcsolatot is használnak, az ügyfelek megszakítás nélkül folytathatják a hálózati kapcsolat elvesztését.

- **Automatikus konfiguráció.** A többcsatornás SMB automatikusan felfedi több elérhető hálózati elérési utat, és szükség szerint dinamikusan hozzáadja a kapcsolatokat.

## <a name="requirements-for-smb-multichannel"></a>A többcsatornás SMB követelményei

Mivel a többcsatornás SMB alapértelmezés szerint engedélyezve van, nem kell további szerepköröket, szerepkör-szolgáltatásokat vagy szolgáltatásokat telepítenie. Az SMB-ügyfél automatikusan észleli és több hálózati kapcsolatot használ a konfiguráció azonosítása során. A többcsatornás SMB-nek azonban a következő követelményei vannak:

- Legalább két, Azure Stack HCI, Windows Server vagy Windows 10 operációs rendszert futtató számítógép.

- Az alábbi konfigurációk legalább egyike:

    - Több hálózati adapter

    - Egy vagy több olyan hálózati adapter, amely támogatja a fogadó oldali skálázást (RSS)

    - Több összevont hálózati adapter (lásd: [hálózati](#nic-teaming)adapterek összevonása)

    - Egy vagy több olyan hálózati adapter, amely támogatja a távoli közvetlen memória-hozzáférést (RDMA)

## <a name="configure-smb-multichannel"></a>Többcsatornás SMB konfigurálása

Ez a szakasz néhány olyan konfigurációs beállítást ismertet, amely a többcsatornás SMB üzembe helyezését mutatja be a hálózati adapterek tömbje segítségével. Ezek a konfigurációk csak példa konfigurációk. Számos más lehetséges konfiguráció is létezik, amelyek nem szerepelnek ebben a témakörben.

### <a name="single-rss-capable-network-adapter"></a>Egyetlen RSS-kompatibilis hálózati adapter

Ebben a tipikus konfigurációban az SMB-ügyfél és az SMB-kiszolgáló egyetlen 10 Gigabit Ethernet (10 GbE) hálózati adapter használatával van konfigurálva. Ha az SMB-t többcsatornás SMB nélkül telepíti, és ha csak egy SMB-munkamenet van létrehozva, akkor az SMB egyetlen TCP/IP-kapcsolatot hoz létre. Ez a konfiguráció csak egyetlen CPU-mag használatával jár együtt a zsúfoltsággal, különösen ha sok kis I/O-művelet végrehajtása történik. Ezért a teljesítmény szűk keresztmetszetének lehetősége jelentős.

A legtöbb aktuális hálózati adapter a fogadó oldali skálázás (RSS) nevű képességet kínálja, amely lehetővé teszi, hogy több kapcsolat automatikusan elosztva több CPU-mag között. Ha azonban egyetlen kapcsolatokat használ, az RSS nem tud segíteni. Ha a többcsatornás SMB-t RSS-kompatibilis hálózati adapterrel használja, az SMB több TCP/IP-kapcsolatot hoz létre az adott munkamenethez. Ez a konfiguráció elkerüli a potenciális szűk keresztmetszetet egyetlen CPU-mag esetében, ha sok kis I/O műveletre van szükség.

### <a name="multiple-network-adapters"></a>Több hálózati adapter

Ebben a konfigurációban az SMB-ügyfél és az SMB-kiszolgáló több 10 GbE hálózati adapter használatával van konfigurálva. Ha az SMB-t többcsatornás SMB nélkül telepíti, és ha csak egy SMB-munkamenet van létrehozva, az SMB az egyik rendelkezésre álló hálózati adaptert használja egyetlen TCP/IP-kapcsolat létrehozásához. Ebben az esetben nem lehet összesíteni a több hálózati adapter sávszélességét; Ha például két GbE hálózati adaptert használ, nem érhet el 2 Gigabit másodpercenként (Gbps). A hiba akkor is lehetséges, ha a kiválasztott hálózati adapter le van választva vagy le van tiltva.

Ha az SMB többcsatornás SMB-vel van telepítve, akkor az SMB több TCP/IP-kapcsolatot hoz létre egyetlen munkamenethez, legalább egy kapcsolattal, ha a hálózati adapterek RSS-kompatibilisek. Ez a konfiguráció lehetővé teszi az SMB számára az elérhető hálózati adapterek sávszélességének használatát, és lehetővé teszi, hogy az SMB-ügyfél megszakítás nélkül folytatódjon, ha a hálózati adapter meghibásodik.

### <a name="nic-teaming"></a>Hálózati adapterek összevonása

Azure Stack HCI és a Windows Server lehetővé teszi, hogy több hálózati adaptert egyetlen hálózati adapterhez egyesítse a hálózati adapterek összevonása nevű szolgáltatás használatával. Bár a csapat mindig biztosít hibatűrést, ha az SMB-t többcsatornás SMB nélkül telepíti, az SMB csak egy TCP/IP-kapcsolatokat hoz létre egy csoporton belül. Ez a konfiguráció korlátozásokat vezet be a felhasznált CPU-magok száma, valamint a csapat sávszélességének maximális kihasználtsága tekintetében.

Ha az SMB többcsatornás SMB-vel van telepítve, az SMB több TCP/IP-kapcsolatot hoz létre egyetlen munkamenet számára, hogy jobb egyensúlyt érjen el a CPU-magok között, és jobban használhassa a rendelkezésre álló sávszélességet. A hálózati adapterek összevonása továbbra is biztosítja a feladatátvételi képességet, ami gyorsabban működik, mint a többcsatornás SMB használata. Javasoljuk továbbá a hálózati adapterek összevonását, mivel feladatátvételi képességeket biztosít más olyan munkaterhelésekhez, amelyek nem támaszkodnak az SMB-re, mert ezek a munkaterhelések nem részesülhetnek a többcsatornás SMB feladatátvételi képességeiben.

Ha a hálózati adapterek dedikált készletét használja Közvetlen tárolóhelyek forgalomhoz, ahogy az Azure Stack HCI-vel is történik, a hálózati adapterek összevonása szigorúan opcionális, és nem biztosít semmiféle jelentős előnyt vagy hátrányt.

>[!IMPORTANT]
>A Windows Server 2012 R2-es és korábbi verzióiban ne használjon hálózati adapterek összevonását, ha a hálózati adapterek RDMA képességeit szeretné használni. Ezeken az operációs rendszereken a RDMA-kompatibilis hálózati adapterek csapata mindig nem RDMA-kompatibilisként van jelenteni, mert az összevonás letiltja a hálózati adapter RDMA képességét.

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>Egy vagy több RDMA-kompatibilis hálózati adapter

A többcsatornás SMB észleli a hálózati adapterek RDMA képességeit, ami lehetővé teszi, hogy az SMB Direct szolgáltatás a RDMA-en keresztül közvetlen SMB-szolgáltatást. A többcsatornás SMB nélkül az SMB szabványos TCP/IP-kapcsolatot használ a RDMA-kompatibilis hálózati adapterek esetében, ahol az összes hálózati adapter olyan TCP/IP-veremet biztosít, amely párhuzamosan van az új RDMA-veremmel.

Ha az SMB többcsatornás SMB-vel van telepítve, az SMB észleli a hálózati adapterek RDMA képességét, és több RDMA-kapcsolatot hoz létre az adott munkamenethez, és két RDMA kapcsolattal rendelkezik. Ez a konfiguráció lehetővé teszi az SMB számára, hogy kihasználhassa a RDMA-kompatibilis hálózati adapterek által kínált nagy átviteli sebességet, kis késést és alacsony CPU-kihasználtságot. Emellett hibatűrést is biztosít, ha több RDMA-illesztőt használ.

>[!IMPORTANT]
>A RDMA-kapcsolat létrejötte után az eredeti protokoll egyeztetésének TCP/IP-kapcsolata már nincs használatban. Azonban ez a kapcsolat akkor marad fenn, ha egy másik RDMA-kapcsolat meghiúsul.

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>Többcsatornás SMB, RDMA-kompatibilis hálózati adapterek és hálózati adapterek összevonásának kompatibilitása

Az alábbi táblázat összefoglalja a többcsatornás SMB, a RDMA (SMB Direct) és a hálózati adapterek összevonása során elérhető különböző képességeket.

| Konfiguráció                                           | Teljesítmény | Hibatűrés az SMB esetében | Hibatűrés nem SMB esetén | Alacsonyabb CPU-kihasználtság |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| Egyetlen hálózati adapter (RSS nélkül)                         | *          |                         |                             |                       |
| Több hálózati adapter (RSS nélkül)                      | **         | *                       |                             |                       |
| Több hálózati adapter (nincs RSS) a hálózati adapterek összevonásával     | **         | **                      | *                           |                       |
| Egyetlen hálózati adapter RSS-sel                         | *          |                         |                             |                       |
| Több hálózati adapter RSS-sel                      | **         | *                       |                             |                       |
| Több hálózati adapter RSS-és hálózati adapterek összevonásával      | **         | **                      | *                           |                       |
| Egyetlen RDMA-kompatibilis hálózati adapter                     | *          |                         |                             | *                     |
| Több RDMA-kompatibilis hálózati adapter                  | ***        | *                       |                             | *                     |
| Több, a hálózati adapterek összevonásával rendelkező RDMA-kompatibilis hálózati adapter | ***        | **                      | *                           | *                     |

Ha a Windows Server 2016-es vagy újabb verzióját futtatja, az ideális megoldás, ha több RDMA-kompatibilis hálózati adaptert használ, és egyesíti a NIC-összevonást a többcsatornás SMB használatával. Ez a kombináció biztosítja a legjobb átviteli sebességet, hibatűrést biztosít az SMB-t és más protokollokat használó alkalmazásokhoz, és a legalacsonyabb CPU-hatást gyakorolja.

A fent említettek szerint, ha RDMA-kompatibilis hálózati adaptereket használ a Windows Server 2012 R2-es vagy korábbi verziókban, a hálózati adapterek összevonása nem jó megoldás, mert letiltja a hálózati adapter RDMA képességét.

### <a name="example-configurations-without-smb-multichannel"></a>Példa konfigurációk többcsatornás SMB nélküli konfigurálására

Ha egyetlen hálózati adaptert szeretne használni RSS nélkül, akkor nem használ több hálózati kapcsolatot, így a többcsatornás SMB használata nem támogatott. Továbbá, ha különböző sebességű hálózati adaptereket kíván használni, a többcsatornás SMB automatikusan kiválasztja a leggyorsabb hálózati adaptert. Ennek az az oka, hogy az azonos típusú (például RDMA, RSS vagy sem) hálózati adaptereket a többcsatornás SMB egyszerre használja. A lassabb hálózati adapterek üresjáratban vannak.

## <a name="disable-smb-multichannel"></a>Többcsatornás SMB letiltása
Általában nem kell letiltania a többcsatornás SMB-t. Ha azonban le szeretné tiltani a többcsatornás SMB-t, például egy tesztkörnyezetben, használja a következő Windows PowerShell-eljárásokat.

Először kapcsolódjon az egyik kiszolgálóhoz egy PowerShell-munkamenet megnyitásával:

```PowerShell
Enter-PSSession <server-name>
```

A többcsatornás SMB letiltásához használja a következő parancsmagot:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

Ha le szeretné tiltani a többcsatornás SMB-t az ügyféloldali oldalon, használja a következő parancsmagot:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>A többcsatornás SMB letiltása az ügyfélen vagy a kiszolgálón blokkolja mindkét rendszer használatát.

## <a name="re-enable-smb-multichannel"></a>Többcsatornás SMB újbóli engedélyezése

Ha letiltotta a többcsatornás SMB-t, és újra engedélyezni szeretné, kövesse az alábbi eljárásokat.

Ha újra engedélyezni szeretné a többcsatornás SMB használatát a kiszolgálói oldalon, használja a következő parancsmagot:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

Ha újra engedélyezni szeretné a többcsatornás SMB-t az ügyféloldali oldalon, használja a következő parancsmagot:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>Újra engedélyeznie kell a többcsatornás SMB-t az ügyfélen és a kiszolgálón is, ha újra szeretné használni.

## <a name="test-smb-multichannel"></a>Többcsatornás SMB tesztelése

Ez a szakasz néhány forgatókönyvet ismertet a többcsatornás SMB teszteléséhez, amely magában foglalja a fájlmásolás összehasonlítását, majd a többcsatornás SMB nélküli használatát, és szándékosan a hálózati adapterek meghibásodását a fájlmásolási művelet során.

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>Fájlmásolás összehasonlítása többcsatornás SMB-vel és anélkül

A többcsatornás SMB által biztosított megnövekedett átviteli sebesség méréséhez hajtsa végre az alábbi teszteket. Az egyes tesztek előtt indítsa újra a kiszolgálót és az ügyfelet, és győződjön meg arról, hogy hasonló körülmények között működnek.

1. Konfigurálja a többcsatornás SMB-t a korábban leírt konfigurációk közül.

1. Mérje meg, hogy mennyi időt vesz igénybe a hosszú ideig futó fájlmásolás-művelet végrehajtása a többcsatornás SMB használatával.

1. Többcsatornás SMB letiltása. Útmutatásért lásd: [többcsatornás SMB letiltása](#disable-smb-multichannel).

1. Mérje meg, hogy mennyi időt vesz igénybe ugyanazon fájlmásolás a többcsatornás SMB nélküli végrehajtásához.

1. Engedélyezze újra a többcsatornás SMB-t. Útmutatásért lásd: [többcsatornás SMB újbóli engedélyezése](#re-enable-smb-multichannel).

1. Hasonlítsa össze a két eredményt.

>[!IMPORTANT]
>A gyorsítótárazás teljesítményre gyakorolt hatásának elkerülése érdekében először másolja ki a nagy mennyiségű adat méretét, amely meghaladja a rendelkezésre álló memória méretét. Ezután végezze el újra a másolási műveletet az első másolási művelettel, amely bemelegítőként szolgál. Csak a második másolási művelet ideje.

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>A többcsatornás SMB-vel rendelkező fájlmásolás során az egyik hálózati adapter meghibásodik

A többcsatornás SMB feladatátvételi funkciójának megerősítése:

1. Győződjön meg arról, hogy a többcsatornás SMB több hálózati adapter konfigurációjában működik.

1. Végezzen el egy hosszú ideig futó fájlmásolás-műveletet.

1. A fájlmásolási művelet futtatása közben szimulálja a hálózati elérési utak egyikének meghibásodását az egyik kábel leválasztásával, vagy az egyik hálózati adapter letiltásával.

1. Győződjön meg arról, hogy a fájlmásolás továbbra is a fennmaradó hálózati adaptert használja, és fájlmásolási hibák nélkül működik.

Győződjön meg arról, hogy nincsenek más, a kapcsolat nélküli hálózati kapcsolat elérési útját használó munkaterhelések. Ezzel a figyelmeztetéssel elkerülhető a többcsatornás SMB-t nem használó munkaterhelések lehetséges meghibásodása.

## <a name="verify-that-smb-multichannel-works"></a>A többcsatornás SMB működésének ellenőrzése

A többcsatornás SMB működésének ellenőrzéséhez kövesse az alábbi eljárást.

1. A hálózati adapter konfigurációjának ellenőrzéséhez írja be a következőt a Windows PowerShellben az SMB-kiszolgálón és az SMB-ügyfélen.

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. Annak ellenőrzéséhez, hogy engedélyezve van-e a többcsatornás SMB, győződjön meg róla, hogy az SMB helyesen azonosítja a hálózati adaptereket, és hogy a hálózati adapter RSS-és RDMA képességei helyesen vannak azonosítva, tegye a következőket:

   Az SMB-ügyfélen írja be a következőt a Windows PowerShellbe:

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   Az SMB-kiszolgálón írja be a következőt a Windows PowerShellbe:

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. Az SMB-ügyfélen végezzen el egy hosszú ideig futó fájlmásolási műveletet, amely létrehoz egy folyamatban lévő munkamenetet az SMB-kiszolgálóval. A másolási művelet futtatása közben írja be a következőt a Windows PowerShellben annak ellenőrzéséhez, hogy a Kapcsolódás az SMB megfelelő verzióját használja-e, és hogy a többcsatornás SMB működik-e.

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>További lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Fájlmegosztás áttekintése a Windows Server SMB 3 protokolljának használatával](/windows-server/storage/file-server/file-server-smb-overview)
- [Egyszerűsített többcsatornás SMB és többadapteres fürthálózatok](/windows-server/failover-clustering/smb-multichannel)
- [SMB Direct](/windows-server/storage/file-server/smb-direct)
