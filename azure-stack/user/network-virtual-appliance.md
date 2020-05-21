---
title: A hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása Azure Stack központban
description: A virtuális gép vagy a VPN-kapcsolat problémáinak elhárítása Microsoft Azure Stack hub hálózati virtuális berendezésének (NVA) használata esetén.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: f933e9c4e70f533d4194b48c7b9e4d6e4bf380b0
ms.sourcegitcommit: d5d89bbe8a3310acaff29a7a0cd7ac4f2cf5bfe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83554964"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>A hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása

A Azure Stack hub hálózati virtuális berendezését (NVA) használó virtuális gépekkel és VPN-kapcsolatokkal kapcsolatos problémák merülhetnek fel.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel ellenőrizheti Azure Stack hub alapszintű platformra vonatkozó követelményeit a NVA-konfigurációkhoz.

A NVA szállítója technikai támogatást nyújt a NVA és az Azure Stack hub platformmal való integrálásához.

> [!NOTE]
> Ha van olyan kapcsolat vagy útválasztási probléma, amely egy NVA tartalmaz, forduljon közvetlenül [a NVA-gyártóhoz](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

Ha ez a cikk nem foglalkozik az Azure Stack hub-vel kapcsolatos NVA-problémával, hozzon létre egy [Azure stack hub támogatási jegyet](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>Ellenőrzőlista NVA-szállítóval való hibaelhárításhoz

- NVA VM-szoftverek frissítései.
- Szolgáltatásfiók beállítása és funkciója.
- Felhasználó által megadott útvonalak (UDR-EK) a virtuális hálózati alhálózatokon, amelyek a NVA irányítják a forgalmat.
- A UDR olyan virtuális hálózati alhálózatokon található, amelyek a NVA irányítják a forgalmat.
- Útválasztási táblák és szabályok a NVA belül (például a NIC1 és a NIC2 között).
- A hálózati forgalom fogadásának és küldésének ellenőrzéséhez NVA hálózati adapterek nyomkövetése.

## <a name="basic-troubleshooting-steps"></a>Alapvető hibaelhárítási lépések

1. Keresse meg az alapkonfigurációt.
1. NVA teljesítményének keresése.
1. Speciális hálózati hibaelhárítás.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Az Azure-beli NVA minimális konfigurációs követelményeinek ellenõrzése

Minden NVA meg kell felelnie az alapszintű konfigurációs követelményeknek, hogy az Azure Stack hub megfelelően működjön. Ez a szakasz az alapvető konfigurációk ellenőrzésének lépéseit mutatja be. További információért [forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Ha a csomagok egy S2S-alagutat használnak, további fejlécekkel vannak beágyazva. Ez a beágyazás növeli az egyes csomagok teljes méretét.
>
> Ebben az esetben a TCP MSS-t 1 350 bájtnál kell megfogni. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen az MTU-t 1 400 bájtra állíthatja.
>
> További információ: [virtuális hálózat TCP/IP-teljesítményének finomhangolása](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>Győződjön meg arról, hogy engedélyezve van-e az IP-továbbítás a NVA

#### <a name="use-the-azure-stack-hub-portal"></a>Az Azure Stack hub portál használata

1. Keresse meg a NVA erőforrást az Azure Stack hub portálon, válassza a **hálózatkezelés**lehetőséget, majd válassza ki a hálózati adaptert.
1. A **hálózati adapter** lapon válassza az **IP-konfiguráció**lehetőséget.
1. Győződjön meg arról, hogy az IP-továbbítás engedélyezve van.

#### <a name="use-powershell"></a>A PowerShell használata

1. Futtassa a következő parancsot. Cserélje le az értékeket a szögletes zárójelbe az adataival.

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

1. Keresse meg a **EnableIPForwarding** tulajdonságot.
1. Ha az IP-továbbítás nincs engedélyezve, futtassa a következő parancsokat az engedélyezéséhez:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Annak megállapítása, hogy a forgalom átirányítható-e a NVA

1. Keresse meg azt a virtuális gépet, amely a NVA irányuló forgalom átirányítására van konfigurálva.
1. Annak ellenőrzését, hogy a NVA a következő ugrás, futtassa a **tracert \< Private IP of \> NVA** for Windows vagy a **traceroute \< Private IP \> -NVA**.
1. Ha a NVA nem szerepel a következő ugrásként, ellenőrizze és frissítse a Azure Stack hub útválasztási táblázatokat.

Előfordulhat, hogy egyes vendég szintű operációs rendszerek rendelkeznek tűzfallal az ICMP-forgalom blokkolására szolgáló házirendekkel. Módosítsa az előző parancsokra vonatkozó tűzfalszabályok működését.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Annak megállapítása, hogy a forgalom elérheti-e a NVA

1. Keresse meg azt a virtuális gépet, amelyhez kapcsolódnia kell a NVA.
1. Győződjön meg arról, hogy bármely hálózati biztonsági csoport (NSG) blokkolja-e a forgalmat. Windows esetén futtassa a **ping** (ICMP) vagy a **test-NetConnection \< magánhálózati IP- \> NVA** (TCP). Linux esetén futtassa a **Tcpping \< magánhálózati IP-NVA \> **.
1. Ha a NSG blokkolja a forgalmat, módosítsa őket a forgalom engedélyezéséhez.

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>Győződjön meg arról, hogy a NVA és a virtuális gépek figyelik-e a várt forgalmat

1. Csatlakozzon a NVA RDP vagy SSH használatával, majd futtassa a következő parancsot:

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. Keresse meg a NVA szoftver által használt TCP-portokat, amelyek az eredmények között szerepelnek. Ha nem látja őket, konfigurálja az alkalmazást a NVA és a virtuális gépen, hogy figyelje és válaszoljon a portokat elérő forgalomra. [Segítségért forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>NVA teljesítményének megtekintése

### <a name="validate-vm-cpu-usage"></a>Virtuális gép CPU-használatának ellenőrzése

Ha a CPU-használat a 100 százalékhoz közeledik, akkor előfordulhat, hogy problémák lépnek fel a hálózati csomagok elejtésével kapcsolatban.

A CPU-tüske során vizsgálja meg, hogy a vendég virtuális gépen melyik folyamat okozza a magas CPU-használatot. Ezt követően enyhítse a használatot, ha lehetséges.

Előfordulhat, hogy át kell méreteznie a virtuális gépet egy nagyobb SKU-méretre, vagy egy virtuálisgép-méretezési csoportnál növelje a példányszámot.

Ha segítségre van szüksége, [forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Virtuálisgép-hálózati statisztika ellenőrzése

Ha a virtuálisgép-hálózat tüskéket használ, vagy magas kihasználtságú időszakokat mutat, érdemes lehet növelni a virtuális gép SKU-méretét a nagyobb átviteli sebesség érdekében.

## <a name="advanced-network-administrator-troubleshooting"></a>A hálózati rendszergazda speciális hibaelhárítása

### <a name="capture-a-network-trace"></a>Hálózati nyomkövetés rögzítése

A [**PsPing**](/sysinternals/downloads/psping) vagy az **nmap**futtatása közben rögzítse egyidejű hálózati nyomkövetést a forrás-és a cél virtuális gépeken, valamint a NVA. Ezután állítsa le a nyomkövetést.

1. Egyidejű hálózati nyomkövetés rögzítéséhez futtassa a következő parancsot:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Használjon **PsPing** vagy **nmap** -t a forrás virtuális gépről a cél virtuális gépre. Ilyenek például a **PsPing 10.0.0.4:80** vagy az **Nmap-p 80 10.0.0.4**.

3. Nyissa meg a hálózati nyomkövetést a célként megadott virtuális gépen a **tcpdump** vagy a kívánt csomagkapcsolt analizátor használatával. Alkalmazzon egy megjelenítési szűrőt a **PsPing** által futtatott vagy **nmap** -ból származó forrásoldali virtuális gép IP-címére. Egy Windows **netmon** -példa: **IPv4. címe = = 10.0.0.4**. A Linux-példák a **tcpdump-NN-r vmtrace. Cap src** és a **DST Host 10.0.0.4**.

### <a name="analyze-traces"></a>Nyomkövetések elemzése

Ha nem látja a csomagokat a háttérbeli virtuális gép nyomkövetéséhez, egy NSG vagy UDR valószínűleg zavaró, vagy a NVA útválasztási táblái helytelenek.

Ha úgy látja, hogy a csomagok beérkeznek, de válasz nélkül, lehetséges, hogy problémát kell megválaszolnia egy virtuálisgép-alkalmazással vagy tűzfallal.

Ha segítségre van szüksége, [forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Ha az előző lépések nem oldják meg a problémát, hozzon létre egy [támogatási jegyet](../operator/azure-stack-manage-basics.md#where-to-get-support) , és használja a [on demand log Collection eszközt](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) a naplók biztosításához.
