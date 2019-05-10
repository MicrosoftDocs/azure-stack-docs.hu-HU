---
title: Azure Stack-site-to-site VPN-kapcsolatok konfigurálása |} A Microsoft Docs
description: A site-to-site VPN- vagy VNet – VNet kapcsolatokhoz az Azure Stackben IPsec/IKE-házirend megismerése
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: ab5b0b5ac0e67a2a625285bd37a04b084fa8da0f
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65386605"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>IPsec/IKE-házirend site-to-site VPN-kapcsolatok konfigurálása

Ez a cikk lépésről lépésre bemutatja egy IPsec/IKE-házirend konfigurálása a site-to-site (S2S) VPN-kapcsolatok az Azure Stackben.

>[!NOTE]
> Fut az Azure Stack-build **1809** vagy újabb verzióját a funkció használatához.  Ha meg van nyitva egy build 1809 előtt, frissítse az Azure Stack-rendszer a legújabb buildre használja ezt a szolgáltatást, vagy kövesse a cikkben ismertetett lépések megkísérlése előtt.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN-átjárók IPsec és az IKE szabályzat paraméterei

A szabványos IPsec és az IKE protokoll különböző kombinációival titkosítási algoritmusok széles skáláját támogatja. Mely paraméterek támogatottak az Azure Stackben, olvassa el [IPsec/IKE-paraméterek](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), ami segít a megfelelőségi és biztonsági követelmények teljesülnek.

Ez a cikk útmutatást a létrehozása és a egy IPsec/IKE-házirend konfigurálása és a egy új vagy meglévő kapcsolatot a alkalmazni.

## <a name="considerations"></a>Megfontolandó szempontok

Ezek a szabályzatok használatakor, vegye figyelembe az alábbi fontos szempontokat:

- Az IPsec/IKE-házirend csak a működik a *Standard* és *HighPerformance* (útvonalalapú) átjáró-termékváltozatokat használják.

- Csak meg **egy** szabályzat együttes egy adott kapcsolathoz.

- Meg kell adnia mind az IKE (elsődleges mód), mind az IPsec (gyors mód) minden algoritmust és paramétert. A részleges házirend-megadás nem engedélyezett.

- Tekintse meg a VPN-eszköz szállítói annak érdekében, hogy a helyszíni VPN-eszközök az szabályzat támogatott előírásoknak való. Hely – hely kapcsolatok nem létesíthető, ha a házirendek nem kompatibilisek.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>1. rész – a munkafolyamat létrehozása és IPsec/IKE-szabályzat beállítása

Ez a szakasz ismerteti a munkafolyamatot hozhat létre, és a egy helyek közötti VPN-kapcsolat IPsec/IKE szabályzat frissítése szükséges:

1. Hozzon létre egy virtuális hálózat és VPN-átjáró.

2. Hozzon létre egy helyi hálózati átjáró létesítmények közötti kapcsolathoz.

3. Hozzon létre egy IPsec/IKE-házirendet a kijelölt algoritmusokkal és paraméterekkel.

4. Hozzon létre egy IPSec-kapcsolat az IPsec/IKE-házirendet.

5. Adjon hozzá/frissítéséhez/eltávolításához egy létező kapcsolat egy IPsec/IKE-szabályzat.

Ez a cikk segít az utasításokat, telepítése és konfigurálása az IPsec/IKE-szabályzatok, az alábbi ábrán látható módon:

![Állítsa be, és IPsec/IKE-szabályzatok konfigurálása](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>2. rész – támogatott titkosítási algoritmusokat és kulcserősségeket

Az alábbi táblázat a támogatott titkosítási algoritmusokat és kulcserősségeket konfigurálható az Azure Stack ügyfelei által:

| IPsec/IKEv2                                          | Beállítások                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2-titkosítás                                     | AES256, AES192, AES128, DES3, DES                                        |
| IKEv2-integritás                                      | SHA384, MD5, SHA1, SHA256                                                |
| DH-csoport                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| IPsec-titkosítás                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs |
| IPsec-integritás                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| PFS-csoport                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs                         |
| Gyorsmódú biztonsági társítás élettartama                                       | (Nem kötelező: alapértelmezett értékek vannak használt Ha nincs megadva)<br />                         Másodperc (egész; min. 300/alapértelmezett érték: 27000 másodperc)<br />                         Kilobájt (egész; min. 1024/alapértelmezett 102400000 KB) |
| Forgalomválasztó                                     | A házirend alapú Forgalomválasztóinak nem támogatottak az Azure Stackben.         |

- A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

  - IKE titkosítási algoritmus (elsődleges mód / 1. fázis)
  - IKE integritási algoritmus (elsődleges mód / 1. fázis)
  - DH-csoport (alapmódú / 1. fázis)
  - IPsec titkosítási algoritmus (gyors mód / 2. fázis)
  - IPsec integritási algoritmus (gyors mód / 2. fázis)
  - PFS-csoport (gyors mód / 2. fázis)
  - AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.

- Ha GCMAES IPsec titkosítási algoritmus meghajtóbetűjeleket használja, jelöljön ki a ugyanazt a GCMAES-algoritmust és az IPsec-integritás; kulcs hossza Ha például használatával GCMAES128 is.

- Az előző táblázatban:

  - Az IKEv2 fő módú vagy 1. fázis felel meg
  - IPsec gyors mód vagy a 2. fázis felel meg
  - DH-csoport megadja a Diffie-Hellmen használt fő módban vagy az 1. fázis
  - PFS-csoport megadva, a gyors mód vagy a 2. fázis használt Diffie-Hellmen csoport

- Az IKEv2 elsődleges módú SA élettartama 28 800 másodpercre az Azure Stackhez VPN-átjárókon van rögzítve.

A következő táblázat felsorolja a megfelelő, az egyéni házirend által támogatott Diffie-Hellman csoport:

| Diffie-Hellman Group | DHGroup   | PFSGroup      | Kulcshossz    |
|----------------------|-----------|---------------|---------------|
| 1.                    | DHGroup1  | PFS1          | 768 bites MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 bites MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 bites MODP |
| 19                   | ECP256    | ECP256        | 256 bites ECP   |
| 20                   | ECP384    | ECP384        | 384 bites ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 bites MODP |

További információkért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>3. rész – új site-to-site VPN-kapcsolat IPsec/IKE-szabályzat létrehozása

Ez a szakasz végigvezeti egy IPsec/IKE-házirendet a site-to-site VPN-kapcsolat létrehozásához szükséges lépéseket. Az alábbi lépéseket a kapcsolat létrehozásához, az alábbi ábrán látható módon:

![site-to-site-policy](media/azure-stack-vpn-s2s/site-to-site.png)

Részletes útmutató egy helyek közötti VPN-kapcsolat létrehozásához, lásd: [site-to-site VPN-kapcsolat létrehozása](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a következő előfeltételek vonatkoznak:

- Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetése, aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

- Az Azure Resource Manager PowerShell-parancsmagokat. Lásd: [Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md) a PowerShell-parancsmagok telepítéséről további információt.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-variables"></a>1. Változók deklarálása

Ebben a gyakorlatban a következő változók deklarálásával kezdjük. Ügyeljen arra, hogy éles üzemi környezetek részei konfigurálása során cserélje le a helyőrzőket a saját értékeit:

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Csatlakozás az előfizetéshez, és hozzon létre egy új erőforráscsoportot

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információkért lásd: [csatlakozhat az Azure Stack PowerShell felhasználói](azure-stack-powershell-configure-user.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

A következő parancs létrehozza a virtuális hálózat **TestVNet1**három alhálózatot, valamint a VPN-átjárót. Értékek behelyettesítésekor fontos, hogy Ön mindig az átjáróalhálózat neve kifejezetten **GatewaySubnet**. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>2. lépés – a site-to-site VPN-kapcsolat egy IPsec/IKE-szabályzat létrehozása

#### <a name="1-create-an-ipsecike-policy"></a>1. Egy IPsec/IKE-szabályzat létrehozása

Ez a példaszkript egy IPsec/IKE-házirendet hoz létre a következő algoritmusokat és paramétereket:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, SA élettartama 14400 másodperc és 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

GCMAES használja az IPsec esetében, ha az IPsec-titkosítás és integritásának a ugyanazt a GCMAES-algoritmust és kulcshosszt kell használnia.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. A site-to-site VPN-kapcsolatot az IPsec/IKE-szabályzat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot, és a alkalmazni a korábban létrehozott IPsec/IKE-házirendet.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Miután egy IPsec/IKE-házirendet egy kapcsolathoz van megadva, az Azure VPN gateway csak küldjön, vagy elfogadja az IPsec/IKE-megadott titkosítási algoritmusokkal és kulcserősségekkel, hogy a kapcsolat a. Győződjön meg arról, hogy a helyszíni VPN-eszköz kapcsolat használ, vagy fogadja el a pontos házirend kombináció, különben a site-to-site VPN-alagút nem létesíthető.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>4. rész - kapcsolat frissítés IPsec/IKE-szabályzat

Az előző szakaszban bemutatta, hogyan kezelheti az IPsec/IKE-házirendet egy létező helyek közötti kapcsolat. Az alábbi szakasz végigvezeti a kapcsolat a következő műveleteket:

1. Az IPsec/IKE szabályzat a kapcsolatok megjelenítése
2. Az IPsec/IKE szabályzat hozzáadásakor vagy a kapcsolatot
3. Az IPsec/IKE-házirendet eltávolítja a kapcsolat

> [!NOTE]
> A támogatott IPsec/IKE-házirend *Standard* és *HighPerformance* útvonalalapú VPN-átjárók csak. A nem működik a *alapszintű* átjáró-Termékváltozatot.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Az IPsec/IKE szabályzat a kapcsolatok megjelenítése

Az alábbi példa bemutatja a IPsec/IKE-szabályzat konfigurálva a kapcsolat. A parancsfájlok továbbra is az előző gyakorlatok:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs megjeleníti az aktuális IPsec/IKE-házirend a kapcsolatot, ha van ilyen konfigurálva. Az alábbi példában látható egy mintakimenet, a kapcsolat:

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

Ha nincs az IPsec/IKE-szabályzat konfigurálva, a parancs `$connection6.policy` egy üres visszatérési beolvasása. Ez nem jelenti azt, hogy az IPsec/IKE nincs konfigurálva a kapcsolat; Ez azt jelenti, hogy egy egyéni IPsec/IKE-házirend sem. A tényleges kapcsolati használja az alapértelmezett házirendet, a helyszíni VPN-eszköz és az Azure VPN-átjáró között.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Kapcsolat egy IPsec/IKE szabályzat hozzáadásakor vagy

Adjon hozzá egy új házirendet, vagy egy kapcsolat egy meglévő szabályzat frissítése szükséges lépések megegyeznek: hozzon létre egy új szabályzatot, majd a alkalmazni az új szabályzat a kapcsolatot.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

A kapcsolat ismét ellenőrizze, hogy ha a házirendet kaphat:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó sort kimenete az alábbi példában látható módon kell megjelennie:

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

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. A kapcsolat egy IPsec/IKE-házirend eltávolítása

Miután eltávolítja a kapcsolat egyéni házirendjét, az Azure VPN-átjáró visszaáll a [alapértelmezett IPsec/IKE javaslat](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), és újbóli egyeztetést végez, a helyszíni VPN-eszközzel.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Használhatja ugyanazt a parancsprogramot, ha a házirendet el lett távolítva a kapcsolat ellenőrzése.

## <a name="next-steps"></a>További lépések

- [VPN gateway konfigurációs beállításairól az Azure Stackhez](azure-stack-vpn-gateway-settings.md)
