---
title: Helyek közötti VPN-kapcsolatok hibakeresése Azure Stack központban
description: A helyek közötti VPN-kapcsolat a helyszíni hálózat és egy Azure Stack hub virtuális hálózat között történő konfigurálása után elvégzendő hibaelhárítási lépések.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5277e908fa9f3c5d8fbcebf28b4d766045d03187
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342903"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>Helyek közötti VPN-kapcsolatok hibáinak megoldása

Ez a cikk a helyek közötti (S2S) VPN-kapcsolat helyszíni hálózat és egy Azure Stack hub virtuális hálózat közötti konfigurálását követően elvégezhető hibaelhárítási lépéseket ismerteti, és a kapcsolat hirtelen leáll, és nem lehet újracsatlakozni.

Ha a Azure Stack hub-probléma nem szerepel ebben a cikkben, látogasson el az [Azure stack hub MSDN fórumára](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Azure-támogatási kérelmet is küldhet. Tekintse meg [Azure stack hub támogatását](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="initial-troubleshooting-steps"></a>Első hibaelhárítási lépések

Az IPsec/IKEV2 Azure Stack hub alapértelmezett paraméterei [a 1910-es buildtel kezdődtek,](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters) forduljon a Azure stack hub-kezelőhöz a Build verziójával kapcsolatos további információkért.

> [!IMPORTANT]
> S2S-alagút használata esetén a csomagok további fejlécekkel vannak beágyazva. Ez a beágyazás növeli a csomag teljes méretét. Ezekben a forgatókönyvekben a TCP **MSS** -t a **1350**-es verzióban kell megfogni. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen az MTU-t **1400** bájtra állíthatja. További információ: a virtuális [hálózat tcpip-es teljesítményének finomhangolása](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

- Győződjön meg arról, hogy a VPN-konfiguráció útvonal-alapú (IKEv2). Azure Stack hub nem támogatja a házirend-alapú (IKEv1) konfigurációkat.

- Ellenőrizze, hogy az [ellenőrzött VPN-eszközt és az operációs rendszer verzióját használja-](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable)e. Ha az eszköz nem ellenőrzött VPN-eszköz, előfordulhat, hogy kapcsolatba kell lépnie az eszköz gyártójával, és meg kell tudnia, hogy van-e kompatibilitási probléma.

- A VPN társ IP-címeinek ellenőrzése:

  - Azure Stack hub **helyi hálózati átjáró** OBJEKTUMának IP-definíciójának meg kell egyeznie a helyszíni eszköz IP-címével.

  - A helyszíni eszközön beállított Azure Stack hub átjáró IP-definíciójának egyeznie kell az Azure Stack hub Gateway IP-címével.

## <a name="status-not-connected---intermittent-disconnects"></a>"Nincs csatlakoztatva" állapot – időszakos leválasztások

- Hasonlítsa össze a helyszíni VPN-eszköz megosztott kulcsát a AzSH virtuális hálózati VPN-kapcsolattal, hogy ellenőrizze a kulcsok egyezését. A AzSH VPN-kapcsolat megosztott kulcsának megtekintéséhez használja az alábbi módszerek egyikét:

  - **Azure stack hub bérlői portál**: lépjen a létrehozott VPN Gateway-helyek közötti kapcsolathoz. A **Beállítások** szakaszban válassza a **megosztott kulcs**elemet.

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN-kapcsolat":::

  - **Azure PowerShell**: használja a következő PowerShell-parancsot:

      ```powershell
      Get-AzureRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
      ```

## <a name="status-connected--traffic-not-flowing"></a>"Csatlakoztatott" állapot – nem áramló forgalom

- Ellenőrizze, és távolítsa el a felhasználó által definiált útválasztási (UDR) és hálózati biztonsági csoportokat (NSG) az átjáró alhálózatán, majd tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze a UDR vagy NSG alkalmazott beállításokat.

   Az átjáró-alhálózat felhasználó által megadott útvonala korlátozhatja a forgalmat, és engedélyezheti a többi forgalmat. Így úgy tűnik, hogy a VPN-kapcsolat nem megbízható a forgalom számára, és mások számára is jó.

- Győződjön meg arról, hogy a helyszíni VPN-eszköz külső csatolójának címe. 

  - Ha a VPN-eszköz internetre irányuló IP-címe a Azure Stack hub **helyi hálózati** definíciójában szerepel, időnként leválaszthatja a kapcsolatot.

  - Az eszköz külső felületének közvetlenül az interneten kell lennie. Nem lehet hálózati címfordítás vagy tűzfal az Internet és az eszköz között.

  - Ha úgy szeretné konfigurálni a tűzfal-fürtözést, hogy virtuális IP-címmel rendelkezzen, meg kell szüntetnie a fürtöt, és közvetlenül egy olyan nyilvános felületen kell közzétennie a VPN-berendezést, amelyen az átjáró csatolható.

- Ellenőrizze, hogy az alhálózatok pontosan egyeznek-e.

  - Ellenőrizze, hogy a virtuális hálózati címtartomány (ok) pontosan egyeznek-e a Azure Stack hub virtuális hálózat és a helyszíni definíciók között.

  - Győződjön meg arról, hogy az alhálózatok pontosan egyeznek a **helyi hálózati átjáró** és a helyszíni hálózat helyszíni definíciói között.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Ha a fenti lépések egyike sem oldja meg a problémát, hozzon létre egy [támogatási jegyet](../operator/azure-stack-manage-basics.md#where-to-get-support) , és használja a [on demand log Collection eszközt](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) a naplók biztosításához.
