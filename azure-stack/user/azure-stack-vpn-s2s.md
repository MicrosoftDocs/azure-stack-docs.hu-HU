---
title: IPsec/IKE-helyek közötti VPN-kapcsolatok konfigurálása Azure Stack hub-ban
description: 'További információk: az IPsec/IKE-szabályzat konfigurálása a helyek közötti VPN-vagy VNet-VNet kapcsolatokhoz Azure Stack központban.'
author: sethmanheim
ms.custom: contperfq4
ms.topic: article
ms.date: 05/21/2020
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 071f8285d4a9f989f295b4d87e3203250763760f
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546820"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Helyek közötti VPN-kapcsolatok IPsec/IKE-szabályzatának konfigurálása

Ez a cikk végigvezeti egy IPsec/IKE-szabályzat konfigurálásának lépésein a helyek közötti (S2S) VPN-kapcsolatokhoz Azure Stack központban.

>[!NOTE]
> A funkció használatához Azure Stack hub Build **1809** vagy újabb verziójának kell futnia.  Ha jelenleg a 1809 előtti buildet futtat, frissítse a Azure Stack hub rendszerét a legújabb buildre, mielőtt folytatná a jelen cikkben ismertetett lépéseket.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>IPsec-és IKE-házirend paraméterei a VPN-átjárók számára

Az IPsec és az IKE protokoll szabványa számos titkosítási algoritmust támogat különböző kombinációkban. Ha szeretné megtekinteni, hogy mely paraméterek támogatottak Azure Stack hub-ban, hogy megfeleljen a megfelelőségi vagy biztonsági követelményeknek, tekintse meg az [IPSec/IKE paramétereit](azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

Ez a cikk útmutatást nyújt az IPsec/IKE-szabályzatok létrehozásáról és konfigurálásáról, és azt egy új vagy meglévő kapcsolódásra alkalmazza.

## <a name="considerations"></a>Megfontolandó szempontok

A szabályzatok használatakor vegye figyelembe a következő fontos szempontokat:

- Az IPsec/IKE-házirend csak a *standard* és a *HighPerformance* (Route-based) átjáró SKU-ban működik.

- Egy adott kapcsolathoz csak egy házirendet adhat meg.

- Meg kell adnia az összes algoritmust és paramétert mind az IKE (Main Mode), mind az IPsec (gyors mód) esetében. A részleges házirend-megadás nem engedélyezett.

- A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a helyi VPN-eszközökön támogatott-e a házirend. A helyek közötti kapcsolatokat nem lehet létrehozni, ha a házirendek nem kompatibilisek.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

- A Azure Resource Manager PowerShell-parancsmagok. A PowerShell-parancsmagok telepítésével kapcsolatos további információkért lásd: a [PowerShell telepítése Azure stack hubhoz](../operator/powershell-install-az-module.md).

## <a name="part-1---create-and-set-ipsecike-policy"></a>1. rész – IPsec/IKE-házirend létrehozása és beállítása

Ez a szakasz a helyek közötti VPN-kapcsolat IPsec/IKE-házirendjének létrehozásához és frissítéséhez szükséges lépéseket ismerteti:

1. Hozzon létre egy virtuális hálózatot és egy VPN-átjárót.

2. Helyi hálózati átjáró létrehozása a létesítmények közötti kapcsolatok létrehozásához.

3. IPsec/IKE-házirend létrehozása a kiválasztott algoritmusokkal és paraméterekkel.

4. Hozzon létre egy IPSec-kapcsolatokat az IPsec/IKE-házirenddel.

5. IPsec/IKE-házirend hozzáadása/frissítése/eltávolítása egy meglévő kapcsolatban.

Az ebben a cikkben szereplő utasítások segítséget nyújtanak az IPsec/IKE-szabályzatok beállításához és konfigurálásához az alábbi ábrán látható módon:

![IPsec/IKE-szabályzatok beállítása és konfigurálása](media/azure-stack-vpn-s2s/site-to-site.svg)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>2. rész – támogatott titkosítási algoritmusok és fő erősségek

A következő táblázat felsorolja a támogatott titkosítási algoritmusokat és a Azure Stack hub által konfigurálható fő erősségeket:

| IPsec/IKEv2                                          | Beállítások                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2-titkosítás                                     | AES256, AES192, AES128, DES3, DES                                        |
| IKEv2-integritás                                      | SHA384, MD5, SHA1, SHA256                                                |
| DH-csoport                                             | ECP384, DHGroup14, DHGroup2, DHGroup1, ECP256 *, DHGroup24*             |
| IPsec-titkosítás                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs |
| IPsec-integritás                                      | GCMAES256, GCMAES192, GCMAES128                                          |
| PFS-csoport                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, PFSMM, nincs                  |
| Gyorsmódú biztonsági társítás élettartama                                       | (Nem kötelező: Ha nincs megadva, az alapértelmezett értékek szerepelnek)<br />                         Másodperc (egész szám; min. 300/alapértelmezett érték: 27000 másodperc)<br />                         KB (egész szám; min. 1024/alapértelmezett érték: 102400000 KB) |
| Forgalomválasztó                                     | A házirend-alapú forgalmi választókat Azure Stack hub nem támogatja.         |

\* Ezek a paraméterek csak a 2002-es és újabb verziókon érhetők el. 

- A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

  - IKE titkosítási algoritmus (fő mód/1. fázis).
  - IKE integritási algoritmus (fő mód/1. fázis).
  - DH-csoport (fő mód/1. fázis).
  - IPsec titkosítási algoritmus (gyors mód/2. fázis).
  - IPsec-integritási algoritmus (gyors mód/2. fázis).
  - PFS-csoport (gyors mód/2. fázis).
  - Az SA-élettartamok csak a helyi specifikációk, és nem kell egyezniük.

- Ha a GCMAES IPsec titkosítási algoritmusként van használatban, ki kell választania ugyanazt a GCMAES algoritmust és a kulcs hosszát az IPsec-integritáshoz; például a GCMAES128 használata mindkettőhöz.

- Az előző táblázatban:

  - A IKEv2 felel meg a Main Mode vagy az 1. fázisnak.
  - Az IPsec megfelel a gyors vagy a 2. fázisnak.
  - A DH-csoport a főmódban vagy az 1. fázisban használt Diffie-Hellmen csoportot határozza meg.
  - A PFS-csoport a gyors módban vagy a 2. fázisban használt Diffie-Hellmen csoportot határozza meg.

- A IKEv2 fő módú SA élettartama 28 800 másodpercen belül megoldódott az Azure Stack hub VPN-átjárók esetében.

A következő táblázat felsorolja az egyéni házirend által támogatott megfelelő Diffie-Hellman csoportokat:

| Diffie-Hellman Group | DH-csoport   | PFS-csoport      | A kulcs hossza    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768 bites MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 bites MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 bites MODP |
| 19                   | ECP256    | ECP256        | 256 bites ECP   |
| 20                   | ECP384    | ECP384        | 384 bites ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 bites MODP |

\* Ezek a paraméterek csak a 2002-es és újabb verziókon érhetők el. 

További információ: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>3. rész – új helyek közötti VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

Ez a szakasz végigvezeti a helyek közötti VPN-kapcsolat IPsec/IKE-házirenddel való létrehozásának lépésein. A következő ábrán látható módon hozza létre a-kapcsolatokat:

![helyek közötti kapcsolat – szabályzat](media/azure-stack-vpn-s2s/site-to-site.svg)

A helyek közötti VPN-kapcsolatok létrehozásával kapcsolatos részletes útmutatásért lásd: [helyek közötti VPN-kapcsolat létrehozása](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-variables"></a>1. változók deklarálása

Ehhez a gyakorlathoz először deklarálja az alábbi változókat. Ügyeljen arra, hogy a helyőrzőket a saját értékeire cserélje le az éles környezetben történő konfiguráláskor:

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. kapcsolódjon az előfizetéshez, és hozzon létre egy új erőforráscsoportot

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [kapcsolódás Azure stack hubhoz a PowerShell-lel felhasználóként](azure-stack-powershell-configure-user.md).

Nyissa meg a PowerShell-konzolt, és kapcsolódjon a fiókjához; például:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

Az alábbi példa létrehozza a virtuális hálózatot, a **TestVNet1** , valamint a három alhálózatot és a VPN-átjárót. Az értékek behelyettesítése esetén fontos, hogy az átjáró alhálózatának **GatewaySubnet** nevezze el. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>2. lépés – helyek közötti VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-szabályzat létrehozása

Ez a példa egy IPsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, SA élettartam 14400 másodperc és 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Ha IPsec-GCMAES használ, ugyanazt a GCMAES algoritmust és a kulcs hosszát kell használnia az IPsec-titkosítás és az integritás esetében is.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. a helyek közötti VPN-kapcsolat létrehozása az IPsec/IKE-házirenddel

Hozzon létre egy helyek közötti VPN-kapcsolatokat, és alkalmazza a korábban létrehozott IPsec/IKE-szabályzatot:

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Ha egy kapcsolathoz IPsec/IKE-házirend van megadva, az Azure VPN Gateway csak az IPsec/IKE-javaslatot küldi el vagy fogadja el az adott kapcsolaton megadott titkosítási algoritmusokkal és fő erősségekkel. Győződjön meg arról, hogy a kapcsolat a helyszíni VPN-eszközt használja, vagy fogadja el a pontos szabályzat-kombinációt, ellenkező esetben a helyek közötti VPN-alagutat nem lehet létrehozni.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>4. rész – az IPsec/IKE-házirend frissítése egy kapcsolathoz

Az előző szakasz azt mutatta be, hogyan kezelhetők az IPsec/IKE-szabályzatok egy meglévő helyek közötti kapcsolathoz. Ez a szakasz végigvezeti a következő műveleteken a kapcsolatban:

- Egy kapcsolat IPsec/IKE-házirendjének megjelenítése.
- Az IPsec/IKE-házirend hozzáadása vagy frissítése egy kapcsolatban.
- Távolítsa el az IPsec/IKE-házirendet egy-kapcsolatban.

> [!NOTE]
> Az IPsec/IKE-házirend csak a *standard* és a *HighPerformance* Route-alapú VPN-átjárók esetében támogatott. Nem működik az *Alapszintű* átjáró SKU-on.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. a kapcsolat IPsec/IKE-szabályzatának megjelenítése

Az alábbi példa bemutatja, hogyan kérheti le az IPsec/IKE-házirendet egy adott kapcsolatban. A parancsfájlok az előző gyakorlatokból is folytatódnak:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs felsorolja a kapcsolatban konfigurált aktuális IPsec/IKE-házirendet, ha van ilyen. A következő példa a kapcsolatok kimenetét szemlélteti:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Ha nincs konfigurálva IPsec/IKE-házirend, a parancs `$connection6.policy` üres értéket kap. Ez nem jelenti azt, hogy az IPsec/IKE nincs konfigurálva a kapcsolatban; Ez azt jelenti, hogy nincs Egyéni IPsec/IKE-szabályzat. A tényleges kapcsolat a helyszíni VPN-eszköz és az Azure VPN Gateway között egyeztetett alapértelmezett házirendet használja.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. IPsec/IKE-házirend hozzáadása vagy frissítése a kapcsolatok számára

Az új szabályzat hozzáadásának vagy egy meglévő házirend frissítésének lépései azonosak: hozzon létre egy új szabályzatot, majd alkalmazza az új házirendet a kapcsolódásra:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

A kapcsolódás ismételt megadásával ellenőrizze, hogy a házirend frissítve van-e:

```powershell
$connection6 = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó sorból származó kimenetnek az alábbi példában látható módon kell megjelennie:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. IPsec/IKE-szabályzat eltávolítása egy kapcsolatban

Miután eltávolította az egyéni házirendet egy kapcsolatból, az Azure VPN Gateway visszavált az [alapértelmezett IPSec/IKE-javaslatra](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), és újraegyezteti a helyszíni VPN-eszközzel.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ugyanazzal a parancsfájllal ellenőrizhető, hogy a házirend el lett-e távolítva a kapcsolatban.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack Hub VPN Gateway-konfigurációs beállításai](azure-stack-vpn-gateway-settings.md)
