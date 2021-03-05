---
title: SDN-infrastruktúra üzembe helyezése az SDN Express használatával
description: Az Sdn-infrastruktúra üzembe helyezésének megismerése az SDN Express használatával
author: v-dasis
ms.topic: how-to
ms.date: 03/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: d42647faa9b45b696323ca4f3157ce6dea709272
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186789"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>SDN-infrastruktúra üzembe helyezése az SDN Express használatával

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ebben a témakörben egy teljes körű szoftveres hálózati (SDN) infrastruktúrát helyez üzembe az SDN Express PowerShell-parancsfájlok használatával. Az infrastruktúra tartalmazhat egy magas rendelkezésre állású (HA) hálózati vezérlőt (NC), egy magas rendelkezésre állású szoftvert Load Balancer (SLB) és egy magas rendelkezésre állású átjárót (GW) is.  A parancsfájlok támogatják a többfázisú telepítést, ahol csak a hálózati vezérlő összetevőt telepítheti a minimális hálózati követelményekkel rendelkező alapvető funkciók eléréséhez.

Az SDN-infrastruktúrát System Center Virtual Machine Manager (VMM) használatával is üzembe helyezheti. További információ: [az Sdn-erőforrások kezelése a VMM-hálóban](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Előkészületek

Az SDN üzembe helyezésének megkezdése előtt tervezze meg és konfigurálja a fizikai és a gazda hálózati infrastruktúrát. Hivatkozzon a következő cikkekre:

- [Fizikai hálózati követelmények](../concepts/physical-network-requirements.md)
- [Gazdagép hálózati követelményei](../concepts/host-network-requirements.md)
- [Fürt létrehozása a Windows felügyeleti központtal](../deploy/create-cluster.md)
- [Fürt létrehozása a Windows PowerShell használatával](../deploy/create-cluster-powershell.md)
- [Szoftveresen definiált hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md)

Nem kell telepítenie az összes SDN-összetevőt. Tekintse meg a [szoftver által meghatározott hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című szakaszos [üzembe helyezés](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) szakaszt a szükséges infrastruktúra-összetevők meghatározásához, majd futtassa a parancsfájlokat ennek megfelelően.

Győződjön meg arról, hogy minden gazdagépen telepítve van a Azure Stack HCI operációs rendszer. Lásd: [a Azure stack HCI operációs rendszer üzembe helyezése](../deploy/operating-system.md) .

## <a name="requirements"></a>Követelmények

Sikeres SDN-telepítés esetén a következő követelményeknek kell teljesülniük:

- Minden gazdagép-kiszolgálónak engedélyezve kell lennie a Hyper-V-nek
- Minden gazdagép-kiszolgálónak csatlakoznia kell Active Directory
- Létre kell hozni egy virtuális kapcsolót
- A fizikai hálózatot konfigurálni kell a konfigurációs fájlban definiált alhálózatok és VLAN-ok számára.
- A konfigurációs fájlban megadott VHDX-fájlnak elérhetőnek kell lennie azon a központi telepítési számítógépen, amelyen az SDN Express parancsfájl fut

## <a name="create-the-vdx-file"></a>A VDX-fájl létrehozása

Az SDN egy VHDX-fájlt használ, amely a Azure Stack HCI operációs rendszert (OS) tartalmazza az SDN virtuális gépek (VM-EK) létrehozásának forrásaként. A VHDX operációs rendszerének verziószámának meg kell egyeznie a Azure Stack HCI Hyper-V gazdagépek által használt verzióval. Ezt a VHDX-fájlt minden SDN infrastruktúra-összetevő használja.

Ha letöltötte és telepítette az Azure Stack HCI operációs rendszert egy ISO-ból, a [Convert-WindowsImage ](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0) segédprogram használatával létrehozhatja a VHDX-fájlt.

Az alábbi példa egy példát mutat be a használatával `Convert-WindowsImage` :

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>A GitHub-adattár letöltése

Az SDN Express parancsfájl fájljai a GitHubban élnek. Az első lépés a szükséges fájlok és mappák beszerzése a telepítési számítógépre.

1. Nyissa meg a [Microsoft Sdn GitHub](https://github.com/microsoft/SDN) -tárházat.

1. A tárházban bontsa ki a **kód** legördülő listát, majd válassza a **klón** vagy a **zip letöltése** lehetőséget, hogy letöltse az Sdn-fájlokat a kijelölt központi telepítési számítógépére.

    > [!NOTE]
    > A kijelölt központi telepítési számítógépnek Windows Server 2016 vagy újabb rendszert kell futtatnia.

1. Bontsa ki a ZIP-fájlt, és másolja a `SDNExpress` mappát az üzembe helyezési számítógép `C:\` mappájába.

## <a name="edit-the-configuration-file"></a>A konfigurációs fájl szerkesztése

A PowerShell `MultiNodeSampleConfig.psd1` konfigurációs adatfájlja tartalmazza a különböző paraméterek és konfigurációs beállítások bemenetként az Sdn Express-parancsfájlhoz szükséges paramétereket és beállításokat. Ez a fájl konkrét információkkal szolgál arról, hogy mit kell kitölteni az alapján, hogy csak a hálózati vezérlő összetevőt, vagy a szoftveres terheléselosztó és az átjáró összetevőit telepíti. Részletes információkat a [szoftveres hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című témakörben talál.

Navigáljon a `C:\SDNExpress\scripts` mappához, és nyissa meg a `MultiNodeSampleConfig.psd1` fájlt a kedvenc szövegszerkesztőben. Adott paraméterérték módosítása az infrastruktúra és a központi telepítés méretének megfelelően:

### <a name="general-settings-and-parameters"></a>Általános beállítások és paraméterek

A beállításokat és paramétereket az SDN általában az összes üzemelő példányhoz használja. Konkrét javaslatokért lásd az [Sdn-infrastruktúra virtuálisgép-szerepkörre vonatkozó követelményeit](../concepts/plan-software-defined-networking-infrastructure.md#sdn-infrastructure-vm-role-requirements).

- **VHDPath** – a VHD-fájl elérési útja, amelyet az Sdn infrastruktúra összes virtuális gépe használ (NC, SLB, GW)
- **VHDFile** – az összes Sdn-infrastruktúra virtuális gépei által használt vhd-fájl neve
- **Jelenlegi** -fájl elérési útja az Sdn-infrastruktúra virtuális gépei számára
- **JoinDomain** – a tartomány, amelyhez az Sdn infrastruktúra virtuális gépei csatlakoznak
- **SDNMacPoolStart** – a Mac-címkészlet kezdeti címe az ügyfél-munkaterhelési virtuális gépekhez
- **SDNMacPoolEnd** -cél Mac-készlet címe az ügyfelek számítási feladatainak virtuális gépei számára
- Az NC által a Hyper-V-gazdagépek, a SLB és a GW-összetevők kezelésére használt **ManagementSubnet** hálózati alhálózat
- **ManagementGateway** – a felügyeleti hálózat átjárójának címe
- **ManagementDNS** – DNS-kiszolgáló a felügyeleti hálózathoz
- **ManagementVLANID** – a felügyeleti hálózat VLAN-azonosítója
- **DomainJoinUsername** – rendszergazdai Felhasználónév
- **LocalAdminDomainUser** – rendszergazdai jelszó
- **Előkészítésekor** – a felügyeleti ügyfelek (például a Windows felügyeleti központ) által használt DNS-név az NC-vel való kommunikációhoz
- **HyperVHosts** – a hálózati vezérlő által felügyelt gazdagép-kiszolgálók
- **NCUsername** – hálózati vezérlő fiók felhasználóneve
- **Termékkulcs** -TERMÉKKULCS az Sdn infrastruktúra virtuális gépei számára
- **SwitchName** – csak akkor szükséges, ha egynél több virtuális kapcsoló található a Hyper-V-gazdagépeken
- **VMMemory** – az infrastruktúra-virtuális gépekhez rendelt memória (GB-ban). Az alapértelmezett érték 4 GB
- **VMProcessorCount** – infrastruktúra-virtuális gépekhez rendelt processzorok száma. Az alapértelmezett érték 8
- **Területi beállítás** – ha nincs megadva, a rendszer az üzembe helyezési számítógép területi beállítását használja
- **Időzóna** – ha nincs megadva, a rendszer az üzembe helyezési számítógép helyi időzónáját használja

A jelszavakat igény szerint is megadhatja, ha titkosított szövegként kódolt biztonságos karakterláncként van tárolva.  A jelszavak csak abban az esetben lesznek használatban, ha az SDN Express-parancsfájlok ugyanazon a számítógépen futnak, ahol a jelszavak titkosítottak, ellenkező esetben a következő jelszavakat fogja kérni:

- **DomainJoinSecurePassword** – tartományi fiókhoz
- **LocalAdminSecurePassword** – helyi rendszergazdai fiókhoz
- **NCSecurePassword** – hálózati vezérlő fiókhoz

> [!NOTE]
> Az SDN Express-parancsfájl nem támogatja a DHCP-címzést. Győződjön meg arról, hogy az SDN-infrastruktúra összes virtuális gépe MAC-címe kívül esik a `SDNMACPool` paraméter tartományán.

### <a name="network-controller-vm-section"></a>Hálózati vezérlő virtuális gép szakasza

Az SDN esetében legalább három hálózati vezérlő virtuális gépet ajánlott használni.

A `NCs = @()` szakasz a hálózati vezérlő virtuális gépekhez használatos. Győződjön meg arról, hogy az egyes NC virtuális gépek MAC-címe kívül esik az `SDNMACPool` Általános beállításokban felsorolt tartományon:

- **Számítógépnév** – NC virtuális gép neve
- **Állomásnév** – annak a kiszolgálónak az állomásneve, AMELYEN az NC virtuális gép található
- **ManagementIP** -felügyeleti hálózati IP-cím az NC virtuális géphez
- **MACAddress** – az NC virtuális gép MAC-címe

### <a name="software-load-balancer-vm-section"></a>Szoftver Load Balancer VM szakasz

Az SDN esetében legalább három szoftver Load Balancer virtuális gép ajánlott.

A `Muxes = @()` szakasz a SLB virtuális gépekhez használatos. Győződjön meg arról, hogy az egyes SLB virtuális gépek MAC-címe kívül esik az `SDNMACPool` Általános beállításokban felsorolt tartományon. Hagyja üresen ezt a szakaszt ( `Muxes = @()` ), ha nem telepíti a SLB összetevőt:

- **Számítógépnév** – a SLB virtuális gép neve
- **Állomásnév** – a SLB virtuális gépet tartalmazó kiszolgáló állomásneve
- **ManagementIP** -felügyeleti hálózati IP-cím a SLB virtuális géphez
- **MACAddress** – a SLB virtuális gép MAC-címe
- **PAIPAddress** -szolgáltató hálózati IP-címe (PA) a SLB virtuális géphez
- **PAMACAddress** -szolgáltató hálózati IP-címe (PA) a SLB virtuális géphez

### <a name="gateway-vm-section"></a>Átjáró virtuális gép szakasza

Az SDN-hez legalább három átjárót tartalmazó virtuális gép (két aktív és egy redundáns) ajánlott.

A `Gateways = @()` szakasz az átjáró virtuális gépekhez használatos. Győződjön meg arról, hogy az egyes átjáró virtuális gépek MAC-címe kívül esik az `SDNMACPool` Általános beállításokban felsorolt tartományon. Hagyja üresen ezt a szakaszt ( `Gateways = @()` ), ha nem telepíti az átjáró összetevőt:

- **Számítógépnév** – átjáró virtuális gép neve
- **Állomásnév** – az átjáró virtuális gépet tartalmazó kiszolgáló állomásneve
- **ManagementIP** -felügyeleti hálózati IP-cím az átjáró virtuális géphez
- **MACAddress** – MAC-címe az átjáró virtuális géphez
- **FrontEndIp** -szolgáltató hálózati ELŐTÉRi IP-címe az átjáró virtuális géphez
- **FrontEndMac** -szolgáltató hálózati ELŐTÉR-MAC-címe az átjáró virtuális géphez
- **BackEndMac** -szolgáltató hálózati háttér-MAC-címe az átjáró virtuális géphez

### <a name="additional-settings-for-slb-and-gateway"></a>A SLB és az átjáró további beállításai

A SLB és az átjáró virtuális gépek a következő további paramétereket használják. Ha nem helyez üzembe SLB-vagy átjáró-virtuális gépeket, hagyja üresen ezeket az értékeket:

- **SDNASN** – az Sdn által a hálózati kapcsolókhoz használt autonóm rendszer száma (ASN)
- **RouterASN** – az ÁTJÁRÓ útválasztó ASN-je
- **RouterIPAddress** – átjáró-útválasztó IP-címe
- **PrivateVIPSubnet** – virtuális IP-cím (VIP) a magánhálózati alhálózat számára
- **PublicVIPSubnet** – a nyilvános ALHÁLÓZAT virtuális IP-címe

A következő további paramétereket csak az átjáró virtuális gépei használják. Ha nem telepít átjáró virtuális gépeket, hagyja üresen ezeket az értékeket:

- **PoolName** – az összes átjárót használó virtuális gépek által használt készlet neve
- **GRESubnet** – VIP-alhálózat GRE számára (GRE-kapcsolatok használata esetén)
- **Kapacitás** /kbit/s a készletben lévő egyes ÁTJÁRÓk virtuális gépe esetében

### <a name="settings-for-tenant-overlay-networks"></a>Bérlői átfedésben lévő hálózatok beállításai

A következő paraméterek használhatók, ha átfedő virtualizált hálózatokat helyez üzembe és kezel a bérlők számára. Ha hálózati vezérlőt használ a hagyományos VLAN-hálózatok kezelésére, akkor ezek az értékek üresen maradhatnak.

- **PASubnet** – a szolgáltatói cím (PA) hálózatának alhálózata
- **PAVLANID** – a PA-hálózat VLAN-azonosítója
- **PAGateway** – a PA hálózati átjáró IP-címe
- **PAPoolStart** – a PA hálózati készlet kezdő IP-címe
- **PAPoolEnd** -végpont IP-címe a PA hálózati készlethez

## <a name="run-the-deployment-script"></a>Az üzembe helyezési szkript futtatása

Az SDN Express-parancsfájl üzembe helyezi a megadott SDN-infrastruktúrát. A szkript befejezésekor az SDN-infrastruktúra készen áll a virtuális gépek számítási feladatainak üzembe helyezésére.

1. Tekintse át a `README.md` fájlt az üzembe helyezési parancsfájl futtatásával kapcsolatos további információkért.  

1. Futtassa a következő parancsot egy olyan felhasználói fiókból, amely rendszergazdai hitelesítő adatokkal rendelkezik a fürt kiszolgálóihoz:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Az NC virtuális gépek létrehozása után konfigurálja a dinamikus DNS-frissítéseket a hálózati vezérlő fürtjének neveként a DNS-kiszolgálón. További információ: [a DNS dinamikus frissítéseinek konfigurálása](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003).

## <a name="next-steps"></a>Következő lépések

A virtuális gépek kezelése. További információ: [virtuális gépek kezelése](../manage/vm.md).
