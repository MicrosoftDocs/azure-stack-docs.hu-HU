---
title: Transzparens proxy Azure Stack hub integrált rendszerekhez
description: Azure Stack hub integrált rendszerek transzparens tulajdonságának áttekintése.
author: PatAltimore
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: a7fc47edf63a83e1ee05c46b03d8533787b1983c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840694"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Transzparens proxy Azure Stack hubhoz

Egy transzparens proxy (más néven elfogó, beágyazott vagy kényszerített proxy) elfogja a normál kommunikációt a hálózati rétegben anélkül, hogy speciális ügyfél-konfigurációra lenne szükség. Az ügyfeleknek nem kell megismerniük a proxy létezését.

Ha az adatközpontban az összes forgalom proxy használatára van szüksége, egy transzparens proxyt kell konfigurálnia, hogy az összes forgalmat feldolgozza a házirendnek megfelelően, ha elválasztja a hálózati zónák közötti forgalmat.

## <a name="traffic-types"></a>Forgalmi típusok

Azure Stack hub kimenő forgalmát bérlői forgalomként vagy infrastrukturális forgalomként kategorizáljuk.

A bérlői forgalmat a bérlők virtuális gépek, terheléselosztó, VPN-átjárók, app Services stb. útján generálják.

Az infrastruktúra-forgalom az infrastruktúra- `/27` szolgáltatásokhoz hozzárendelt nyilvános virtuális IP-címkészlet első tartományából származik, például az identitás, a javítás és a frissítés, a használati metrikák, a Piactéri hírszolgáltatás, a regisztráció, a naplók gyűjtése, a Windows Defender stb. alapján. A szolgáltatásokból érkező adatforgalmat az Azure- [végpontok](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)irányítják át. Az Azure nem fogadja el a proxy vagy TLS/SSL által lefoglalt forgalom által módosított forgalmat. Ennek az az oka, hogy Azure Stack hub nem támogatja a natív proxy-konfigurációt.

Transzparens proxy konfigurálásakor dönthet úgy, hogy az összes kimenő forgalmat vagy csak az infrastruktúra-forgalmat küldi el a proxyn keresztül.

## <a name="partner-integration"></a>Partnerintegráció

A Microsoft a piacvezető proxy-szállítókkal együttműködve ellenőrzi Azure Stack hub használati eseteit egy transzparens proxy-konfigurációval. A következő ábra egy példa Azure Stack hub hálózati konfigurációt a HA proxys szolgáltatással. A külső proxy eszközöket a Border Devices-től északra kell helyezni.

![Hálózati diagram a Border Devices előtti proxy előtt](./media/azure-stack-transparent-proxy/proxy.svg)

 Emellett a határokon átnyúló eszközöket úgy kell konfigurálni, hogy a következő módszerek egyikével irányítsák át a Azure Stack hub forgalmát:
- Az Azure Stack hub összes kimenő forgalmának átirányítása a proxy eszközökre
- Irányítsa az összes kimenő forgalmat az `/27` Azure stack hub virtuális IP-készlet első tartományából a proxy eszközökre házirend alapú útválasztás útján.  

A minta szegélyének konfigurációját a jelen cikk [példa szegélyek konfigurálása](#example-border-configuration) című szakaszában találja.

Tekintse át a következő dokumentumokat az ellenőrzött transzparens proxy-konfigurációkhoz Azure Stack hubhoz:

- [Ellenőrzési pont biztonsági átjárójának transzparens proxyjának konfigurálása](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [A Sophos XG-tűzfal transzparens proxyjának konfigurálása](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)
- [Citrix ADC, Citrix Secure Web Gateway integrálása Azure Stack hubhoz](https://www.citrix.com/blogs/2021/02/19/integrating-citrix-adc-citrix-secure-web-gateway-with-azure-stack-hub/)

Azokban az esetekben, amikor a Azure Stack hub kimenő forgalmát egy explicit proxyn keresztül kell elvégeznie, a Sophos és az ellenőrzőpont-eszközök olyan kettős üzemmódú szolgáltatást biztosítanak, amely lehetővé teszi a forgalom meghatározott tartományait transzparens módban, míg más tartományok úgy konfigurálhatók, hogy explicit módon továbbítsák a forgalmat. Ezzel a szolgáltatással ezeket a proxy eszközöket úgy lehet konfigurálni, hogy csak az infrastruktúra-forgalmat továbbítsa az átlátszó proxyn keresztül, míg az összes bérlői forgalmat explicit módon küldik el.

> [!IMPORTANT]
> Az SSL-forgalom elfogása nem támogatott, és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. Az identitáshoz szükséges végpontokkal folytatott kommunikáció maximális támogatott időtúllépése 60 – 3 újrapróbálkozási kísérlet. További információ: [Azure stack hub tűzfal-integráció](azure-stack-firewall.md#ssl-interception).

## <a name="example-border-configuration"></a>Példa a szegélyek konfigurálására

A megoldás a házirend-alapú útválasztás (PBR) alapján történik, amely egy hozzáférés-vezérlési lista (ACL) által megvalósított rendszergazda által meghatározott feltételeket használ. Az ACL kategorizálja azt a forgalmat, amely az útvonal-leképezésben megvalósított proxy eszközök következő ugrási IP-címére van irányítva, nem pedig a normál útválasztást, amely kizárólag a cél IP-címen alapul. A 80-es és a 443-es portokra vonatkozó specifikus infrastruktúra-hálózati forgalom a szegélyek eszközeiről az átlátszó proxy üzembe helyezésére van irányítva. Az átlátszó proxy URL-szűrést végez, és az *egyik sem engedélyezett* forgalom el lett dobva.

A következő konfigurációs minta a Cisco Nexus 9508 váz.

Ebben az esetben az internet elérését igénylő forrás-infrastrukturális hálózatok a következők:

- Nyilvános VIP – első/27
- Infrastruktúra-hálózat – utolsó/27
- BMC-hálózat – utolsó/27

A következő alhálózatok fogadják a házirend-alapú útválasztást (PBR) a jelen forgatókönyvben:

| Network (Hálózat) | IP-címtartomány | PBR-kezelést fogadó alhálózat
|---------|----------|-------------------------------
| Nyilvános virtuális IP-készlet | 172.21.107.0/27 első/27 | 172.21.107.0/27 = 172.21.107.1 – 172.21.107.30
| Infrastruktúra-hálózat | 172.21.7.0/24 utolsó/27 | 172.21.7.224/27 = 172.21.7.225 – 172.21.7.254
| BMC-hálózat | 10.60.32.128/26. utolsó/27 | 10.60.32.160/27 = 10.60.32.161 – 10.60.32.190

### <a name="configure-border-device"></a>Szegély eszközének konfigurálása

Engedélyezze a PBR-t a parancs beírásával `feature pbr` . 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

Hozza létre az új ACL-t, amely a PBR-kezelést lekérdező forgalom azonosítására szolgál majd. Ez a forgalom webes forgalom (80-es HTTP-port, 443-es HTTPS-port) a tesztelési rackben lévő gazdagépekről/alhálózatokról, amelyek a jelen példában részletezett módon jelentkeznek. Az ACL neve például **PERMITTED_TO_PROXY_ENV1**.

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

A PBR-funkciók magját a **TRAFFIC_TO_PROXY_ENV1** route-map implementálja. A **pbr-Statistics** beállítás hozzáadásával engedélyezheti a szabályzat egyeztetési statisztikáit, hogy ellenőrizze a csomagok számát, amelyek nem kapják meg a pbr-továbbítást. A route-map 10. sorozata engedélyezi a PBR-t a Traffic Meeting ACL- **PERMITTED_TO_PROXY_ENV1** feltételeinek teljesítéséhez. Ezt a forgalmat a és a következő ugrási IP-címei továbbítják `10.60.3.34` `10.60.3.35` , amelyek a példánkban szereplő elsődleges/másodlagos proxy eszközök VIP-címei

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Az ACL-eket a **TRAFFIC_TO_PROXY_ENV1** route-map egyeztetési feltételeiként használják. Ha a forgalom megfelel a **PERMITTED_TO_PROXY_ENV1** ACL-nek, a pbr felülbírálja a normál útválasztási táblázatot, és ehelyett továbbítja a forgalmat a felsorolt IP-ugrásokra.
 
Az **TRAFFIC_TO_PROXY_ENV1** pbr-szabályzat olyan forgalomra vonatkozik, amely a CL04-gazdagépekről és a nyilvános VIP-címekről, illetve a DVM lévő HLH és beírja a szegélyt tartalmazó eszközt.

## <a name="next-steps"></a>Következő lépések

További információ a tűzfalak integrálásáról: [Azure stack hub tűzfal-integráció](azure-stack-firewall.md).
