---
title: A hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása Azure Stack központban
description: A virtuális gépekkel vagy VPN-kapcsolattal kapcsolatos problémák és hibák elhárítása harmadik féltől származó hálózati virtuális berendezés (NVA) használatakor Microsoft Azure Stack hub-ban.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342892"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>A hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása

Előfordulhat, hogy a virtuális gép vagy a VPN-kapcsolat problémáit és hibáit a Microsoft Azure Stack hub harmadik féltől származó hálózati virtuális berendezésének (NVA) használatakor tapasztalja. Ez a cikk alapvető lépéseket biztosít a NVA-konfigurációk alapszintű Azure Stack hub-platformra vonatkozó követelményeinek érvényesítéséhez.

A harmadik féltől származó NVA és az Azure Stack hub platformmal való integrálásának technikai támogatását a NVA gyártója biztosítja.

> [!NOTE]
> Ha van olyan kapcsolat vagy útválasztási probléma, amely egy NVA tartalmaz, forduljon közvetlenül [a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

Ha a Azure Stack hub NVA probléma nem szerepel ebben a cikkben, akkor hozzon létre egy [Azure stack hub támogatási jegyet](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Ellenőrzőlista a NVA-gyártóval való hibaelhárításhoz

- Szoftverfrissítések a NVA VM-szoftverekhez.
- Szolgáltatásfiók beállítása és funkciója.
- Felhasználó által megadott útvonalak (UDR-EK) a virtuális hálózati alhálózatokon, amelyek közvetlen forgalmat NVA.
- UDR a NVA-ból közvetlen forgalmat lebonyolító virtuális hálózati alhálózatokon.
- Útválasztási táblák és szabályok a NVA belül (például a NIC1 és a NIC2 között).
- A hálózati forgalom fogadásának és küldésének ellenőrzéséhez NVA hálózati adapterek nyomkövetése.

## <a name="basic-troubleshooting-steps"></a>Alapvető hibaelhárítási lépések

- Keresse meg az alapkonfigurációt.
- NVA teljesítményének keresése.
- Speciális hálózati hibaelhárítás.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Az Azure-beli NVA minimális konfigurációs követelményeinek ellenõrzése

Minden NVA rendelkezik az Azure Stack hub megfelelő működéséhez szükséges alapszintű konfigurációs követelményekkel. A következő szakasz az alapvető konfigurációk ellenőrzésének lépéseit ismerteti. További információért [forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> S2S-alagút használata esetén a csomagok további fejlécekkel vannak beágyazva. Ez a beágyazás növeli a csomag teljes méretét. Ezekben a forgatókönyvekben a TCP **MSS** -t a **1350**-es verzióban kell megfogni. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen az MTU-t **1400** bájtra állíthatja. További információ: a virtuális [hálózat tcpip-es teljesítményének finomhangolása](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>Győződjön meg arról, hogy engedélyezve van-e az IP-továbbítás a NVA

#### <a name="use-the-azure-stack-hub-portal"></a>Az Azure Stack hub portál használata

- Keresse meg a NVA erőforrást az Azure Stack hub portálon, válassza a **hálózatkezelés**lehetőséget, majd válassza ki a hálózati adaptert.
- A **hálózati adapter** lapon válassza az **IP-konfiguráció**lehetőséget.
- Győződjön meg arról, hogy az IP-továbbítás engedélyezve van.

#### <a name="use-powershell"></a>A PowerShell használata

- Futtassa a következő parancsot (a zárójeles értékeket cserélje le az adataira):

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- Keresse meg a **EnableIPForwarding** tulajdonságot.
- Ha az IP-továbbítás nincs engedélyezve, futtassa a következő parancsokat az engedélyezéséhez:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>Győződjön meg arról, hogy a forgalom átirányítható-e a NVA.

- A NVA irányuló forgalom átirányítására konfigurált virtuális gép megkeresése
- Futtassa a parancsot a `Tracert <Private IP of NVA>` Windows rendszerhez, vagy keresse meg a `Traceroute <Private IP of NVA>` NVA a következő ugrásként.
- Ha a NVA nem szerepel a következő ugrásként, ellenőrizze és frissítse a Azure Stack hub útválasztási táblázatokat.

Előfordulhat, hogy egyes vendég szintű operációs rendszerek tűzfallal rendelkeznek az ICMP-forgalom blokkolásához. Ezeket a tűzfalszabályok frissítését frissíteni kell az előző parancsok működéséhez.

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>Annak megkeresése, hogy a forgalom elérheti-e a NVA

- Keresse meg azt a virtuális gépet, amelyhez kapcsolódnia kell a NVA.
- A `ping (ICMP)` `Test-NetConnection (TCP) <Private IP of NVA>` Windows és a Linux rendszerű számítógépeken futtatva `Tcpping <Private IP of NVA>` ellenőrizhető, hogy a hálózati biztonsági csoportok blokkolják-e a forgalmat.
- Ha a forgalom le van tiltva, módosítsa a NSG, hogy engedélyezze a forgalmat.

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>Győződjön meg arról, hogy a NVA és a virtuális gépek figyelik-e a várható forgalmat

- Csatlakozzon a NVA RDP vagy SSH használatával, majd futtassa a következő parancsot:

   **Windows**:
   ```shell
   netstat -an
   ```

   **Linux**:
   ```shell
   netstat -an | grep -i listen
   ```

- Ha nem látja azt a TCP-portot, amelyet az eredmények között felsorolt NVA szoftver használ, konfigurálnia kell az alkalmazást a NVA és a virtuális gépen, hogy figyelje és reagáljon a portokat elérő forgalomra. [Szükség esetén forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>NVA teljesítményének megtekintése

### <a name="validate-vm-cpu"></a>Virtuális gép PROCESSZORának ellenőrzése

Ha a CPU-használat a 100 százalékhoz közeledik, akkor előfordulhat, hogy problémák lépnek fel a hálózati csomagok elejtésére. A CPU-tüske során vizsgálja meg, hogy a vendég virtuális gép melyik folyamata okozza a magas CPU-t, és ha lehetséges, csökkentse a problémát. Előfordulhat, hogy a virtuális gépet át kell méreteznie egy nagyobb SKU-méretre, vagy egy virtuálisgép-méretezési csoport esetén növelje a példányszámot. A problémák bármelyike esetén [a szükséges segítségért forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Virtuálisgép-hálózati statisztika ellenőrzése

Ha a virtuálisgép-hálózat tüskéket használ, vagy a magas kihasználtságú időszakokat jelenít meg, akkor előfordulhat, hogy a virtuális gép SKU-méretét is meg kell emelnie, hogy az adatátviteli kapacitás nagyobb legyen.

## <a name="advanced-network-administrator-troubleshooting"></a>A hálózati rendszergazda speciális hibaelhárítása

### <a name="capture-network-trace"></a>Hálózati nyomkövetés rögzítése

Rögzítse egyidejű hálózati nyomkövetést a forrásoldali virtuális gépen, a NVA és a cél virtuális gépen a [**PsPing**](/sysinternals/downloads/psping) vagy az **nmap**futtatása közben, majd állítsa le a nyomkövetést.

- Egyidejű hálózati nyomkövetés rögzítéséhez futtassa a következő parancsot:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- Használjon **PsPing** vagy **nmap** -t a forrás virtuális gépről a cél virtuális gépre (például: `PsPing 10.0.0.4:80` vagy `Nmap -p 80 10.0.0.4` ).

- Nyissa meg a hálózati nyomkövetést a célként megadott virtuális gépen a **tcpdump** vagy a kívánt csomagkapcsolt analizátor használatával. Alkalmazzon egy megjelenítési szűrőt a **PsPing** vagy **nmap** -t futtató forrásoldali virtuális gép IP-címéhez, például `IPv4.address==10.0.0.4` (Windows netmon) `tcpdump -nn -r vmtrace.cap src` vagy `dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Nyomkövetések elemzése

Ha nem látja a háttérbeli virtuális gép nyomkövetéséhez tartozó csomagokat, valószínűleg egy NSG vagy egy UDR zavarja, vagy a NVA útválasztási táblái helytelenek.

Ha úgy látja a csomagokat, amelyekhez nem érkezik válasz, akkor előfordulhat, hogy egy virtuálisgép-alkalmazást vagy egy tűzfal-problémát kell megválaszolnia. A problémák bármelyike esetén [a szükséges segítségért forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Ha a fenti lépések egyike sem oldja meg a problémát, hozzon létre egy [támogatási jegyet](../operator/azure-stack-manage-basics.md#where-to-get-support) , és használja a [on demand log Collection eszközt](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) a naplók biztosításához.
