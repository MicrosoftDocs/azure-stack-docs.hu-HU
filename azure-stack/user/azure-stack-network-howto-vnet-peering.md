---
title: Két Azure Stack hub-virtuális hálózatok összekötése
description: Ismerkedjen meg a két Azure Stack hubok összekapcsolásával.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b6488b98317decf6b592339bfa66d15834f1f55d
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543391"
---
# <a name="vnet-to-vnet-connectivity-with-rras"></a>VNet-VNet kapcsolat létesítése az RRAS használatával

Ugyanahhoz a Azure Stack hub-környezethez két Azure Stack hub-virtuális hálózatok is csatlakoztathat egymáshoz. Jelenleg nem lehet csatlakoztatni Azure Stack hub-virtuális hálózatok a beépített [Virtual Network átjáró](./azure-stack-network-differences.md)használatával. A két Azure Stack hub-virtuális hálózatok közötti VPN-alagút létrehozásához NVA-készülékeket kell használnia. Ebben a cikkben a sablonra vonatkozó hivatkozásokban két Windows Server 2016 virtuális gép van telepítve az RRAS szolgáltatással. A két RRAS-kiszolgáló úgy van konfigurálva, hogy S2SVPN IKEv2-alagutat hozzon létre két virtuális hálózatok között. A megfelelő NSG és UDR szabályok úgy jönnek létre, hogy lehetővé tegyék az alhálózatok közötti útválasztást az egyes **belső** VNET. 

Ez az üzembe helyezési minta az az alap, amely lehetővé teszi, hogy a VPN-alagutak ne csak Azure Stack hub-példányon belül jöjjenek létre, hanem Azure Stack hub-példányok és más erőforrások, például a Windows RRAS S2S VPN-alagutak használatát használó helyszíni hálózatok számára is. 

A sablonokat az [Azure intelligens Edge Pattern GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
) -tárházában találja. A sablon a **S2SVPNTunnel** mappában található.

![A diagram egy olyan implementációt mutat be, amely VPN-alagutat biztosít két virtuális hálózatok között. Minden VNET van egy RRAS-kiszolgáló, valamint egy belső alhálózat és egy alagút-alhálózat is.](./media/azure-stack-network-howto-vnet-peering/overview.svg)

## <a name="requirements"></a>Követelmények

- Központi telepítés a legújabb frissítésekkel. 
- Szükséges Azure Stack hub Marketplace-elemek:
    -  Windows Server 2016 Datacenter (a legújabb Build ajánlott)
    -  Egyéni szkriptbővítmény

## <a name="things-to-consider"></a>Megfontolandó dolgok

- A rendszer a sablon alagút-alhálózatára alkalmazza a hálózati biztonsági csoportot. Javasoljuk, hogy a belső alhálózatot minden VNet egy további NSG biztonságossá tegye.
- A rendszer RDP-megtagadási szabályt alkalmaz az alagút NSG, és engedélyezni kell, hogy a virtuális gépeket a nyilvános IP-címen keresztül kívánja elérni
- Ez a megoldás nem veszi figyelembe a DNS-feloldást.
- A VNet-név és a vmName kombinációjának 15 karakternél rövidebbnek kell lennie
- Ez a sablon úgy van kialakítva, hogy a VNet1 és a VNet2 testreszabott VNet-neveket rendelkezzen
- Ez a sablon BYOL Windows-t használ
- Az erőforráscsoport törlésekor a jelenleg (1907) elemnél manuálisan le kell választania a NSG az alagút-alhálózatból, hogy a törlési erőforráscsoport befejeződjön.
- Ez a sablon DS3v2 virtuális gépet használ. Az RRAS szolgáltatás a belső Windows-SQL Server telepíti és futtatja. Ez memóriabeli problémákat okozhat, ha a virtuális gép mérete túl kicsi. A virtuális gép méretének csökkentése előtt ellenőrizze a teljesítményt.
- Ez nem egy magasan elérhető megoldás. Ha még több megoldásra van szüksége, egy második virtuális gépet is hozzáadhat, manuálisan kell módosítania az útvonalat az útválasztási táblázatban a másodlagos csatoló belső IP-címére. Emellett a több alagutat is konfigurálnia kell a többhöz való kapcsolódáshoz.

## <a name="options"></a>Beállítások

- A blob Storage-fiókját és az SAS-tokent a _artifactsLocation és a _artifactsLocationSasToken paraméterek használatával is használhatja
- Ennek a sablonnak két kimenete van a INTERNALSUBNETREFVNET1 és a INTERNALSUBNETREFVNET2, amely a belső alhálózatok erőforrás-azonosítói, ha ezt szeretné használni egy folyamat típusú telepítési mintában.

A sablon a VNet elnevezési és IP-címzési alapértelmezett értékeit biztosítja. A rendszergazdának (rrasadmin) jelszót kell használnia, és a saját tárolási blobját is használhatja SAS-token használatával. Ügyeljen arra, hogy ezeket az értékeket a jogi tartományokon belül tárolja, mivel a telepítés sikertelen lehet. A PowerShell DSC-csomag minden RRAS virtuális gépen fut, és az Útválasztás és az összes szükséges függő szolgáltatás és szolgáltatás telepítése történik. Ezt a DSC-t szükség esetén tovább lehet testreszabni. Az egyéni szkriptek bővítménye a következő parancsfájlt futtatja, és a `Add-Site2Site.ps1` két RRAS-kiszolgáló között megosztott kulccsal konfigurálja a VPNS2S-alagutat. Az egyéni szkriptek bővítményének részletes kimenetét megtekintve megtekintheti a VPN-alagút konfigurációjának eredményét.

![A S2SVPNTunnel című diagram két, helyek közötti VPN-alagúthoz csatlakoztatott virtuális hálózatok mutat be.](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.svg)

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
