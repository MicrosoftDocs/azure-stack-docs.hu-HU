---
title: VPN-alagút létrehozása a GRE használatával Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre VPN-alagutat a GRE használatával Azure Stackban.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c9c599a7547b2000eb146a7e3b7783ba057d23b3
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167695"
---
# <a name="how-to-create-a-vpn-tunnel-using-gre-in-azure-stack"></a>VPN-alagút létrehozása a GRE használatával Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ebben a megoldásban a Azure Stack Resource Manager-sablonnal összekapcsolhat két Azure Stack virtuális hálózatok ugyanazon a Azure Stack környezetben. [Azure stack virtuális hálózatok nem csatlakoztatható](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) a beépített Virtual Network átjáró használatával. Egyelőre a Network Virtual Appliances (NVA) s használatával VPN-alagutat kell létrehoznia két Azure Stack virtuális hálózatok között. A megoldás sablonja két Windows Server 2016 virtuális gépet telepít, amelyeken az RRAS telepítve van. A megoldás úgy konfigurálja a két RRAS-kiszolgálót, hogy S2SVPN IKEv2 alagutat használjanak a két virtuális hálózatok között. A megfelelő NSG-és UDR-szabályok úgy jönnek létre, hogy lehetővé tegyék az alhálózatok közötti útválasztást az egyes, **belsőként** kijelölt VNET. 

Ez az üzembe helyezési minta az az alap, amely lehetővé teszi, hogy a VPN-alagutakat ne csak a Azure Stack-példányon belül hozza létre, hanem Azure Stack példányok és más erőforrások, például a helyszíni hálózatok és a Windows RRAS S2S VPN használatával is. Alagutak.

A sablonokat az [Azure intelligens Edge Pattern](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub-tárházában találja. A sablon az **RRAS-GRE-vnet-vnet** mappában található. 

![helyettesítő szöveg](./media/azure-stack-network-howto-vpn-tunnel-gre/overview.png)

## <a name="requirements"></a>Követelmények

- ASDK vagy Azure Stack integrált rendszer a legújabb frissítésekkel. 
- Szükséges Azure Stack Marketplace-elemek:
    -  Windows Server 2016 Datacenter (a legújabb Build ajánlott)
    -  Custom Script Extension

## <a name="things-to-consider"></a>Megfontolandó dolgok

- A rendszer a sablon alagút-alhálózatára alkalmazza a hálózati biztonsági csoportot. A belső alhálózat védelme minden VNet egy további NSG.
- A rendszer RDP-megtagadási szabályt alkalmaz az alagút NSG, és engedélyezni kell, hogy a virtuális gépeket a nyilvános IP-címen keresztül kívánja elérni
- Ez a megoldás nem veszi figyelembe a DNS-feloldást.
- A VNet-név és a vmName kombinációjának 15 karakternél rövidebbnek kell lennie
- Ez a sablon úgy van kialakítva, hogy a VNet1 és a VNet2 testreszabott VNet-neveket rendelkezzen
- Ez a sablon BYOL Windows-t használ
- Az erőforráscsoport törlésekor a jelenleg (1907) elemnél manuálisan le kell választania a NSG az alagút-alhálózatból, hogy a törlési erőforráscsoport befejeződjön.
- Ez a sablon DS3v2 virtuális gépet használ. Az RRAS szolgáltatás a belső Windows-SQL Server telepíti és futtatja. Ez memóriabeli problémákat okozhat, ha a virtuális gép mérete túl kicsi. A virtuális gép méretének csökkentése előtt ellenőrizze a teljesítményt.
- Ez nem egy magasan elérhető megoldás. Ha még több megoldásra van szüksége, egy második virtuális gépet is hozzáadhat, manuálisan kell módosítania az útvonalat az útválasztási táblázatban a másodlagos csatoló belső IP-címére. Emellett a több alagutat is konfigurálnia kell a többhöz való kapcsolódáshoz.

## <a name="options"></a>Beállítások

- A _artifactsLocation és a _artifactsLocationSasToken paraméterek használatával saját blob Storage-fiókját és SAS-tokenjét is használhatja
- Ennek a sablonnak két kimenete van a INTERNALSUBNETREFVNET1 és a INTERNALSUBNETREFVNET2, amely a belső alhálózatok erőforrás-azonosítói, ha ezt szeretné használni egy folyamat típusú telepítési mintában.

Ez a sablon a VNet elnevezési és IP-címzési alapértelmezett értékeit tartalmazza. A rendszergazdának (rrasadmin) jelszót kell használnia, és a saját tárolási blobját is használhatja SAS-token használatával. Ügyeljen arra, hogy ezeket az értékeket a jogi tartományokon belül tárolja, mivel a telepítés sikertelen lehet. A PowerShell DSC-csomag minden RRAS virtuális gépen fut, és az Útválasztás és az összes szükséges függő szolgáltatás és szolgáltatás telepítése történik. Ezt a DSC-t szükség esetén tovább lehet testreszabni. Az egyéni szkriptek bővítménye a következő parancsfájlt futtatja, és a Add-Site2SiteGRE. ps1 a két RRAS-kiszolgáló között megosztott kulccsal konfigurálja a VPNS2S-alagutat. Az egyéni szkriptek bővítményének részletes kimenetét megtekintve megtekintheti a VPN-alagút konfigurációjának eredményét.

![helyettesítő szöveg](./media/azure-stack-network-howto-vpn-tunnel-gre/s2svpntunnel.png)

## <a name="next-steps"></a>Következő lépések

[Különbségek és szempontok Azure Stack hálózatkezeléshez](azure-stack-network-differences.md)  
[Több helyek közötti VPN-alagút beállítása](network-howto-vpn-tunnel.md)