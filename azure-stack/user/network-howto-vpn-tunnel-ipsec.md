---
title: VPN-alagút létrehozása az IPSEC használatával Azure Stack hub-ban
description: Megtudhatja, hogyan hozhat létre VPN-alagutat az Azure Stack hub IPSEC szolgáltatásával.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: e3c75bcae503ac8aff234fa28a5e46e011f02f84
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885206"
---
# <a name="how-to-create-a-vpn-tunnel-using-ipsec--in-azure-stack-hub"></a>VPN-alagút létrehozása az IPSEC használatával Azure Stack hub-ban

Ebben a megoldásban a Azure Stack hub Resource Manager-sablonnal csatlakoztathat két Azure Stack hub-virtuális hálózatok ugyanazon a Azure Stack hub-környezetben belül. [Azure stack hub-virtuális hálózatok nem csatlakoztatható](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) a beépített Virtual Network átjáró használatával. Egyelőre a Network Virtual Appliances (NVA) s használatával VPN-alagutat kell létrehoznia két Azure Stack hub-virtuális hálózatok között. A megoldás sablonja két Windows Server 2016 virtuális gépet telepít, amelyeken az RRAS telepítve van. A megoldás úgy konfigurálja a két RRAS-kiszolgálót, hogy S2SVPN IKEv2 alagutat használjanak a két virtuális hálózatok között. A megfelelő NSG-és UDR-szabályok úgy jönnek létre, hogy lehetővé tegyék az alhálózatok közötti útválasztást az egyes, **belsőként** kijelölt VNET. 

Ez a megoldás az az alapja, amely lehetővé teszi, hogy a VPN-alagutak ne csak Azure Stack hub-példányon belül jöjjenek létre, hanem Azure Stack hub-példányok és más erőforrások, például a Windows RRAS S2S VPN-alagutak használatát használó helyszíni hálózatok számára is.

A sablonokat az [Azure intelligens Edge Pattern](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub-tárházában találja. A sablon az **RRAS-GRE-vnet-vnet** mappában található. 

![helyettesítő szöveg](./media/azure-stack-network-howto-vpn-tunnel-ipsec/overview.png)

## <a name="requirements"></a>Követelmények

- A legújabb frissítések alkalmazásával telepített rendszer. 
- Szükséges Azure Stack hub Marketplace-elemek:
    -  Windows Server 2016 Datacenter vagy Windows Server 2019 Datacenter (a legújabb Build ajánlott)
    -  Custom Script Extension

## <a name="things-to-consider"></a>Megfontolandó dolgok

- A rendszer a sablon alagút-alhálózatára alkalmazza a hálózati biztonsági csoportot.  Javasoljuk, hogy a belső alhálózatot minden VNet egy további NSG biztonságossá tegye.
- A rendszer RDP-megtagadási szabályt alkalmaz az alagút NSG, és engedélyezni kell, hogy a virtuális gépeket a nyilvános IP-címen keresztül kívánja elérni
- Ez a megoldás nem veszi figyelembe a DNS-feloldást.
- A VNet-név és a vmName kombinációjának 15 karakternél rövidebbnek kell lennie
- Ez a sablon úgy van kialakítva, hogy a VNet1 és a VNet2 testreszabott VNet-neveket rendelkezzen
- Ez a sablon BYOL Windows-t használ
- Az erőforráscsoport törlésekor a jelenleg (1907) elemnél manuálisan le kell választania a NSG az alagút-alhálózatból, hogy a törlési erőforráscsoport befejeződjön.
- Ez a sablon DS3v2 virtuális gépet használ.  Az RRAS szolgáltatás a belső Windows-SQL Server telepíti és futtatja.  Ez memóriabeli problémákat okozhat, ha a virtuális gép mérete túl kicsi.  A virtuális gép méretének csökkentése előtt ellenőrizze a teljesítményt.
- Ez nem egy magasan elérhető megoldás.  Ha még több megoldásra van szüksége, egy második virtuális gépet is hozzáadhat, manuálisan kell módosítania az útvonalat az útválasztási táblázatban a másodlagos csatoló belső IP-címére.  Emellett a több alagutat is konfigurálnia kell a többhöz való kapcsolódáshoz.

## <a name="optional"></a>Választható

- A blob Storage-fiókját és az SAS-tokent a _artifactsLocation és a _artifactsLocationSasToken paraméterek használatával is használhatja
- Ennek a sablonnak két kimenete van a INTERNALSUBNETREFVNET1 és a INTERNALSUBNETREFVNET2, amely a belső alhálózatok erőforrás-azonosítói, ha ezt szeretné használni egy folyamat típusú telepítési mintában.

Ez a sablon a VNet elnevezési és IP-címzési alapértelmezett értékeit tartalmazza.  A rendszergazdának (rrasadmin) jelszót kell használnia, és a saját tárolási blobját is használhatja SAS-token használatával.  Ügyeljen arra, hogy ezeket az értékeket a jogi tartományokon belül tárolja, mivel a telepítés sikertelen lehet.  A PowerShell DSC-csomag minden RRAS virtuális gépen fut, és az Útválasztás és az összes szükséges függő szolgáltatás és szolgáltatás telepítése történik.  Ezt a DSC-t szükség esetén tovább lehet testreszabni.  Az egyéni szkriptek bővítménye a következő parancsfájlt futtatja, és a Add-Site2SiteIKE. ps1 a két RRAS-kiszolgáló között megosztott kulccsal konfigurálja a VPNS2S-alagutat.  Az egyéni szkriptek bővítményének részletes kimenetét megtekintve megtekintheti a VPN-alagút konfigurációjának eredményét.

![helyettesítő szöveg](./media/azure-stack-network-howto-vpn-tunnel-ipsec/s2svpntunnel.png)

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[Több helyek közötti VPN-alagút beállítása](network-howto-vpn-tunnel.md)  
[VPN-alagút létrehozása a GRE használatával](network-howto-vpn-tunnel-gre.md)