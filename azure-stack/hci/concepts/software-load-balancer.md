---
title: Szoftver Load Balancer (SLB) az SDN-hez Azure Stack HCI-ben
description: Ebből a témakörből megtudhatja, hogyan Azure Stack a HCI-ben a szoftveresen definiált hálózatkezelés szoftvereit Load Balancer.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 35cf6d997a7200b762b8da052e5e30038f288bdc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254993"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>Mi a szoftver Load Balancer \( SLB \) for Sdn?

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

[A Azure stack HCI-ben a szoftveres hálózatkezelést (Sdn)](software-defined-networking.md) telepítő felhőalapú szolgáltatók (CSP-EK) és a szoftveres Load BALANCER (SLB) segítségével egyenletesen terjeszthetik a bérlői és bérlői ügyfél hálózati forgalmát a virtuális hálózati erőforrások között. A SLB lehetővé teszi, hogy több kiszolgáló ugyanazt a számítási feladatot működtesse, magas rendelkezésre állást és méretezhetőséget biztosítva.

A szoftver Load Balancer a következő képességeket tartalmazza:

- 4. réteg (L4) terheléselosztási szolgáltatások az Észak-és Dél-, valamint a keleti/nyugati TCP/UDP-forgalomhoz.

- Nyilvános hálózat és belső hálózati forgalom terheléselosztása.

- Dinamikus IP-címek (DIPs) támogatása a virtuális helyi hálózatok (VLAN-ok) és a Hyper-V hálózati virtualizálás használatával létrehozott virtuális hálózatok esetében.

- Állapot-mintavételi támogatás.

- Készen áll a felhőalapú méretezésre, többek között a kibővíthető képességre és a multiplexerek és a gazdagép-ügynökök felskálázására.

- Egy több-bérlős egyesített peremhálózat zökkenőmentesen integrálható az SDN-technológiákkal, például a RAS-átjáróval, az adatközpont-tűzfallal és az útvonal-tükrözéssel.

További információ: a jelen témakör [szoftverek Load Balancer szolgáltatásai](#bkmk_features) .

> [!NOTE]
> A hálózati vezérlő nem támogatja a VLAN-ok bérlős. A VLAN-ok azonban a szolgáltató által felügyelt számítási feladatokhoz, például az adatközpont-infrastruktúrához és a nagy sűrűségű webkiszolgálók SLB használhatók.

A szoftveres Load Balancer használatával a SLB virtuális gépek (VM-EK) használatával a terheléselosztási funkciókat a többi virtuálisgép-munkaterheléshez használt Hyper-V számítási kiszolgálókon is kibővítheti. Emiatt a szoftveres terheléselosztás támogatja a CSP-műveletekhez szükséges terheléselosztási végpontok gyors létrehozását és törlését. Emellett a szoftveres terheléselosztás a fürtön belül több tízezer gigabájtot is támogat, egyszerű kiépítési modellt biztosít, és könnyen kibővíthető.

## <a name="how-software-load-balancer-works"></a>A szoftveres Load Balancer működése

A szoftver Load Balancer úgy működik, hogy a virtuális IP-címeket (VIP) olyan DIPsra rendeli, amely az adatközpontban található erőforrások felhőalapú szolgáltatásának részét képezi.

A VIP-címek egyetlen IP-cím, amelyek nyilvános hozzáférést biztosítanak egy elosztott terhelésű virtuális gépek készletéhez. A VIP-címek például az interneten elérhető IP-címek, így a bérlők és a bérlők ügyfelei csatlakozni tudnak a felhőalapú adatközpontban található bérlői erőforrásokhoz.

A DIPs egy elosztott terhelésű készlet virtuális gépei IP-címe a VIP mögött. A DIPs a felhőalapú infrastruktúrában van hozzárendelve a bérlői erőforrásokhoz.

A VIP-EK a SLB Multiplexerben (MUX) találhatók.  A MUX egy vagy több virtuális gépet tartalmaz.  A hálózati vezérlő minden egyes VIP-MUX biztosít, és mindegyik MUX Border Gateway Protocol (BGP) használatával hirdeti meg az egyes VIP-ket a fizikai hálózaton lévő útválasztók számára/32 útvonalként.  A BGP lehetővé teszi a fizikai hálózati útválasztók számára a következőket:

- Ismerje meg, hogy minden MUX elérhető-e VIP-cím, még akkor is, ha a MUXes a 3. rétegbeli hálózat különböző alhálózatán vannak.

- A virtuális IP-címek terhelését az összes rendelkezésre álló MUXes egyenlő díjszabású többutas (ECMP) útvonalon keresztül oszthatja szét.

- A MUX hibájának vagy eltávolításának automatikus észlelése, valamint a sikertelen MUX való adatküldés leállítása.

- A sikertelen vagy eltávolított MUX terhelésének elosztása az egészséges MUXes.

Ha a nyilvános forgalom az internetről érkezik, a SLB MUX megvizsgálja a forgalmat, amely a virtuális IP-címet tartalmazza célhelyként, és leképezi és átírja a forgalmat úgy, hogy az egy adott DIP-ra fog érkezni. A bejövő hálózati forgalom esetében ezt a tranzakciót egy kétlépéses folyamat hajtja végre, amely a MUX virtuális gépek és a Hyper-V-gazdagép között oszlik meg, ahol a cél DIP található:

1. Terheléselosztás – a MUX a virtuális IP-címet használja a DIP kiválasztásához, beágyazza a csomagot, és továbbítja a forgalmat arra a Hyper-V-gazdagépre, ahol a DIP található.

2. Hálózati címfordítás (NAT) – a Hyper-V-gazdagép eltávolítja a csomagból a beágyazást, lefordítja a VIP-t egy DIP-re, újraképezi a portokat, és továbbítja a csomagot a DIP virtuális géphez.

A MUX tudja, hogyan képezhető le a virtuális IP-címek a megfelelő DIPshoz a hálózati vezérlő használatával meghatározott terheléselosztási házirendek miatt. Ezek a szabályok a következők: protokoll, előtér-port, háttér-port és terjesztési algoritmus (5, 3 vagy 2 rekordok).

Ha a bérlői virtuális gépek válaszolnak, és a kimenő hálózati forgalmat visszaküldik az internetre vagy a távoli bérlői helyekre, mert a NAT-t a Hyper-V-gazdagép hajtja végre, a forgalom megkerüli a MUX, és közvetlenül a peremhálózati útválasztóhoz irányítja a Hyper-V gazdagépet. Ezt a MUX megkerülő folyamat neve Direct Server Return (DSR).

A kezdeti hálózati forgalom létrehozása után a bejövő hálózati forgalom teljesen megkerüli a SLB MUX.

Az alábbi ábrán egy ügyfélszámítógép DNS-lekérdezést hajt végre egy vállalati SharePoint-webhely IP-címéhez – ebben az esetben a contoso nevű kitalált vállalatot. A következő folyamat történik:

1. A DNS-kiszolgáló visszaadja a VIP-107.105.47.60 az ügyfélnek.

2. Az ügyfél HTTP-kérést küld a VIP-nek.

3. A fizikai hálózat több elérési úttal rendelkezik, amelyekkel elérheti a VIP-t bármely MUX.  Az egyes útválasztók a ECMP használatával veszik fel az elérési út következő szegmensét, amíg a kérelem meg nem érkezik egy MUX.

4. A kérést fogadó MUX ellenőrzi a konfigurált házirendeket, és úgy látja, hogy a virtuális hálózaton két, a 10.10.10.5 és a 10.10.20.5 rendelkezésre álló DIPs van, hogy kezelni tudja a kérést a VIP 107.105.47.60

5. A MUX kijelöli a DIP-10.10.10.5, és a VXLAN használatával beágyazza a csomagokat, hogy az a gazdagép fizikai hálózati címe alapján küldje el a DIP-t tartalmazó gazdagépnek.

6. A gazdagép megkapja a beágyazott csomagot, és megvizsgálja azt. Eltávolítja a beágyazást, és átírja a csomagot úgy, hogy a cél most a virtuális IP-cím helyett 10.10.10.5 DIP, majd elküldi a forgalmat a DIP virtuális gépre.

7. A kérelem eléri a contoso SharePoint-webhelyet a 2. kiszolgálófarm területén. A kiszolgáló létrehoz egy választ, és elküldi az ügyfélnek a saját IP-címe forrásként való használatával.

8. A gazdagép elfogja a virtuális kapcsolón a kimenő csomagot, amely arra a célra emlékszik, hogy az ügyfél most már a cél, az eredeti kérést a VIP-nek.  A gazdagép újraírja a csomag forrását úgy, hogy a VIP legyen, így az ügyfél nem látja a DIP-címet.

9. A gazdagép továbbítja a csomagot közvetlenül a fizikai hálózat alapértelmezett átjárójának, amely a szabványos útválasztási táblázat használatával továbbítja a csomagot az ügyfélnek, amely végül megkapja a választ.

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="Szoftveres terheléselosztási folyamat" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>Belső adatközpont-forgalom terheléselosztása

Ha az adatközpontba belső hálózati adatforgalmat (például a különböző kiszolgálókon futó bérlői erőforrások és az ugyanazon virtuális hálózat tagjai közötti) terheléselosztást végez, a Hyper-V virtuális kapcsoló, amelyhez a virtuális gépek csatlakoznak, NAT-t hajt végre.

A belső forgalom terheléselosztásával az első kérést a MUX küldi el és dolgozza fel, amely kiválasztja a megfelelő DIP-t, majd átirányítja a forgalmat a DIP felé. Ettől a ponttól kezdve a létrehozott forgalmi folyamat megkerüli a MUX, és közvetlenül a virtuális gépről a virtuális gépre kerül.

### <a name="health-probes"></a>Állapotminták

A szoftveres Load Balancer a hálózati infrastruktúra állapotának ellenőrzésére, beleértve a következőket is:

- TCP-mintavétel portra

- HTTP-mintavétel a port és az URL-cím között

A hagyományos terheléselosztó berendezéstől eltérően, ahol a mintavétel a berendezésből származik, és a huzalon halad a DIP-ban, a SLB mintavétel azon a gazdagépen történik, ahol a DIP található, és közvetlenül a SLB gazdagép ügynökéről a DIP-be kerül, és tovább terjeszti a munkát a gazdagépek között.

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>Szoftver-Load Balancer infrastruktúra
A szoftveres Load Balancer konfigurálásához először telepítenie kell a hálózati vezérlőt és egy vagy több SLB MUX virtuális gépet.

Emellett konfigurálnia kell a Azure Stack HCI-gazdagépeket az SDN-kompatibilis Hyper-V virtuális kapcsolóval, és meg kell győződnie arról, hogy a SLB-gazdagép ügynöke fut. A gazdagépeket kiszolgáló útválasztóknak támogatniuk kell a ECMP Útválasztás és Border Gateway Protocol (BGP) protokollt, és úgy kell konfigurálni, hogy a SLB MUXes fogadjanak el BGP-társi kérelmeket.

Az alábbi ábra áttekintést nyújt a SLB-infrastruktúráról.

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="Szoftver-Load Balancer infrastruktúra" border="false":::

A következő szakaszokban további információkat talál a szoftver Load Balancer infrastruktúra ezen elemeiről.

### <a name="network-controller"></a>Hálózati vezérlő
A hálózati vezérlő üzemelteti a SLB-kezelőt, és a következő műveleteket hajtja végre a szoftver Load Balancer:

- A irányú API-n keresztül elérhető SLB-parancsokat dolgozza fel a Windows felügyeleti központ, a System Center, a Windows PowerShell vagy más hálózatkezelési alkalmazás használatával.

- Kiszámítja a terjesztési házirendet Azure Stack HCI-gazdagépekre és SLB-MUXes.

- A szoftver Load Balancer-infrastruktúrájának állapotát adja meg.

A Windows felügyeleti központ vagy a Windows PowerShell segítségével telepítheti és konfigurálhatja a hálózati vezérlőt és más SLB-infrastruktúrát.

### <a name="slb-mux"></a>SLB MUX
A SLB MUX feldolgozza a bejövő hálózati forgalmat, és leképezi a VIP-ket a DIPsba, majd a megfelelő DIP-re továbbítja a forgalmat. Mindegyik MUX a BGP-t is használja a virtuális IP-útvonalak közzétételére a peremhálózati útválasztók számára. A BGP életben tartása értesíti a MUXes, ha egy MUX meghibásodik, ami lehetővé teszi, hogy az aktív MUXes MUX meghibásodása esetén újraterjessze a terhelést. Ez lényegében biztosítja a terheléselosztó terheléselosztását.

### <a name="slb-host-agent"></a>SLB-gazda ügynök
A szoftver Load Balancer központi telepítésekor a SLB-gazdagép ügynökét a Windows felügyeleti központot, a System centert, a Windows PowerShellt vagy egy másik felügyeleti alkalmazást kell használnia az összes gazdagépen.

A SLB-gazda ügynök figyeli a SLB házirend-frissítéseket a hálózati vezérlőről. Emellett a gazdagép-ügynök programjai a helyi számítógépen konfigurált, SDN-kompatibilis Hyper-V virtuális kapcsolókra vonatkozó szabályokat is SLB.

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>SDN-kompatibilis Hyper-V virtuális kapcsoló
Ahhoz, hogy egy virtuális kapcsoló kompatibilis legyen a SLB, engedélyezni kell a virtuális szűrő platform (VFP) bővítményt a virtuális kapcsolón. Ezt automatikusan végrehajtja az SDN üzembe helyezési PowerShell-parancsfájlok, a Windows felügyeleti központ telepítése varázsló és a System Center Virtual Machine Manager (SCVMM) telepítése.

A VFP virtuális kapcsolókon való engedélyezésével kapcsolatos információkért tekintse meg a [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) és az [enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps)Windows PowerShell-parancsokat.

Az SDN-kompatibilis Hyper-V virtuális kapcsoló a következő műveleteket hajtja végre a SLB:

- Feldolgozza a SLB adatelérési útját.

- Bejövő hálózati forgalom fogadása a MUX.

- Megkerüli a MUX a kimenő hálózati forgalom számára, és elküldi azt az útválasztónak a DSR használatával.

### <a name="bgp-router"></a>BGP-útválasztó
A BGP-útválasztó a következő műveleteket hajtja végre a szoftver Load Balancer:

- A bejövő forgalmat a ECMP használatával irányítja a MUX.

- A kimenő hálózati forgalom esetében a a gazdagép által megadott útvonalat használja.

- A SLB MUX származó VIP-címek útvonal-frissítéseinek figyelése.

- Eltávolítja a SLB MUXes a SLB-rotációból, ha az életben maradás sikertelen.

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>A szoftver Load Balancer funkciói
A következő szakaszok ismertetik a szoftverek Load Balancer funkcióit és képességeit.

### <a name="core-functionality"></a>Alapvető funkciók

- A SLB 4. rétegbeli terheléselosztási szolgáltatásokat biztosít az Észak-és Dél-, valamint a keleti/nyugati TCP/UDP-forgalomhoz.

- A SLB Hyper-V rendszerű virtualizált hálózatokon is használható.

- A SLB-t VLAN-alapú hálózattal használhatja az SDN-t használó Hyper-V virtuális kapcsolóhoz csatlakozó DIP virtuális gépekhez.

- Egy SLB-példány több bérlőt is képes kezelni.

- A SLB és a DIP a DSR által megvalósított skálázható és kis késleltetésű visszatérési útvonalat támogatja.

- SLB függvények, ha a Switch Embedded Teaming (SET) vagy egy root input/output Virtualization (SR-IOV) használatát is használja.

- A SLB tartalmazza a Internet Protocol Version 6 (IPv6) és a 4-es verziójú (IPv4) támogatást.

- Helyek közötti átjáró esetén a SLB olyan NAT-funkciókat biztosít, amelyek lehetővé teszik az összes helyek közötti kapcsolat használatát egyetlen nyilvános IP-cím kihasználása érdekében.

### <a name="scale-and-performance"></a>Méretezés és teljesítmény

- Készen áll a felhőalapú méretezésre, beleértve a kibővíthető és a MUXes és a gazdagép-ügynökökre vonatkozó vertikális Felskálázási képességet.

- Egy aktív SLB-kezelő hálózati vezérlő modul nyolc MUX-példányt képes támogatni.

### <a name="high-availability"></a>Magas rendelkezésre állás

- Egy aktív/aktív konfigurációban több mint két csomópontra is telepíthet SLB.

- A MUXes a MUX-készletből a SLB szolgáltatás befolyásolása nélkül adhatók hozzá és távolíthatók el. Ez fenntartja a SLB rendelkezésre állását, ha az egyes MUXes javítás alatt állnak.

- Az egyes MUX-példányok 99%-os üzemidővel rendelkeznek.

- Az állapot-figyelési adategységek a felügyeleti entitások számára érhetők el.

## <a name="next-steps"></a>További lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Hálózati vezérlő – áttekintés](network-controller-overview.md)
- [Hálózati vezérlő telepítése a Windows PowerShell használatával](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [SDN Azure Stack HCI-ben](software-defined-networking.md)