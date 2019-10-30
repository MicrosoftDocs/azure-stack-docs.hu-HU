---
title: Két Azure stack összekötése a VNET-társításon keresztül | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható két Azure-verem a VNET-társításon keresztül.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 91a90146f1f62ebea980a785d8a8e347f04fe137
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064712"
---
# <a name="vnet-peering-in-azure-stack-with-vms"></a>virtuális társhálózatok létesítése virtuális gépekkel Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ugyanahhoz a Azure Stack-környezethez két Azure Stack virtuális hálózatok is csatlakozhat egymáshoz. Jelenleg nem lehet csatlakozni Azure Stack virtuális hálózatok a beépített [Virtual Network átjáró](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences)használatával. A két Azure Stack virtuális hálózatok közötti VPN-alagút létrehozásához NVA-készülékeket kell használnia. Ebben a cikkben a sablonra vonatkozó hivatkozásokban két Windows Server 2016 virtuális gép van telepítve az RRAS szolgáltatással. A két RRAS-kiszolgáló úgy van konfigurálva, hogy S2SVPN IKEv2-alagutat hozzon létre két virtuális hálózatok között. A megfelelő NSG és UDR szabályok úgy jönnek létre, hogy lehetővé tegyék az alhálózatok közötti útválasztást az egyes **belső**VNET. 

Ez az üzembe helyezési minta az alapja, amely lehetővé teszi, hogy a VPN-alagutak ne csak Azure Stack példányon belül jöjjenek létre, hanem Azure Stack példányok és más erőforrások, például a Windows RRAS S2S VPN-alagutak használatát használó helyszíni hálózatok számára is. 

A sablonokat az [Azure intelligens Edge Pattern GitHub](https://github.com/lucidqdreams/azure-intelligent-edge-patterns
) -tárház **lucidqdreams** villájában találja. A sablon a **S2SVPNTunnel** mappában található.

![helyettesítő szöveg](./media/azure-stack-network-howto-vnet-peering/overview.png)

## <a name="requirements"></a>Követelmények

- ASDK vagy Azure Stack integrált rendszer a legújabb frissítésekkel. 
- Szükséges Azure Stack Marketplace-elemek:
    -  Windows Server 2016 Datacenter (a legújabb Build ajánlott)
    -  Custom Script Extension

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

- A _artifactsLocation és a _artifactsLocationSasToken paraméterek használatával saját blob Storage-fiókját és SAS-tokenjét is használhatja
- Ennek a sablonnak két kimenete van a INTERNALSUBNETREFVNET1 és a INTERNALSUBNETREFVNET2, amely a belső alhálózatok erőforrás-azonosítói, ha ezt szeretné használni egy folyamat típusú telepítési mintában.

A sablon a VNet elnevezési és IP-címzési alapértelmezett értékeit biztosítja. A rendszergazdának (rrasadmin) jelszót kell használnia, és a saját tárolási blobját is használhatja SAS-token használatával. Ügyeljen arra, hogy ezeket az értékeket a jogi tartományokon belül tárolja, mivel a telepítés sikertelen lehet. A PowerShell DSC-csomag minden RRAS virtuális gépen fut, és az Útválasztás és az összes szükséges függő szolgáltatás és szolgáltatás telepítése történik. Ezt a DSC-t szükség esetén tovább lehet testreszabni. Az egyéni szkriptek bővítménye a következő parancsfájlt futtatja, és `Add-Site2Site.ps1` konfigurálja a VPNS2S-alagutat a két RRAS-kiszolgáló között egy megosztott kulccsal. Az egyéni szkriptek bővítményének részletes kimenetét megtekintve megtekintheti a VPN-alagút konfigurációjának eredményét.

![helyettesítő szöveg](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.png)

## <a name="next-steps"></a>Következő lépések

[Különbségek és szempontok Azure Stack hálózatkezeléshez](azure-stack-network-differences.md)  