---
title: Kiszolgáló összekötése az Azure Virtual Network – Azure hálózati adapter
description: Ez a cikk azokat a követelményeket és lépéseket ismerteti, amelyekkel az Azure hálózati adapter használatával csatlakozhat egy kiszolgálóhoz egy Azure-Virtual Networkhoz.
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 523ee87a5673a79dbb17c18251fc8837b9d2f7e4
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573649"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>A kiszolgáló Azure-Virtual Networkhoz való összekapcsolásához használja az Azure hálózati adapterét

>A következőkre vonatkozik: Windows Server 2019, Windows Server 2016, Windows Server 2012 R2

A helyszíni környezetben és a többfelhős környezetekben futó nagy számítási feladatokhoz a Microsoft Azure rendszerű virtuális gépekhez (VM-EK) való kapcsolódásra van szükség. Ha egy kiszolgálót Azure-Virtual Network szeretne összekapcsolásra, több lehetőség közül választhat, beleértve a helyek közötti VPN-t, az Azure Express útvonalat és a pont – hely VPN-t.

A Windows felügyeleti központ és az Azure hálózati adapter egykattintásos élményt biztosít a kiszolgáló virtuális hálózattal való összekapcsolásához [pont – hely típusú VPN-](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) kapcsolat használatával. A folyamat automatizálja a virtuális hálózati átjáró és a helyszíni VPN-ügyfél konfigurálását.

## <a name="when-to-use-azure-network-adapter"></a>Mikor kell használni az Azure hálózati adaptert
Az Azure hálózati adapter pont – hely típusú VPN-kapcsolat akkor hasznos, ha távoli helyről, például fiókirodából, áruházból vagy más helyről szeretne csatlakozni a virtuális hálózathoz. A helyek közötti VPN helyett az Azure hálózati adaptert is használhatja, ha csak néhány kiszolgálót szeretne csatlakozni egy virtuális hálózathoz. Az Azure hálózati adapterek kapcsolatai nem igényelnek VPN-eszközt vagy nyilvános IP-címet.

### <a name="requirements"></a>Követelmények
A virtuális hálózathoz való kapcsolódáshoz az Azure hálózati adapter használatával a következők szükségesek:
- Legalább egy aktív előfizetéssel rendelkező Azure-fiók.
- Egy meglévő virtuális hálózat.
- Az Azure-beli virtuális hálózathoz csatlakozni kívánó célkiszolgáló internet-hozzáférése.
- Egy Windows felügyeleti központ Azure-hoz való kapcsolódása.
  További tudnivalókért tekintse meg az [Azure-integráció konfigurálását](/windows-server/manage/windows-admin-center/azure/azure-integration)ismertető témakört.
- A Windows felügyeleti központ legújabb verziója.
  További információ: [Windows felügyeleti központ](https://www.microsoft.com/windows-server/windows-admin-center).

> [!NOTE]
> Nem szükséges telepíteni a Windows felügyeleti központot azon a kiszolgálón, amelyhez csatlakozni szeretne az Azure-hoz. Ezt azonban egyetlen kiszolgálói helyzetben is megteheti.

## <a name="add-an-azure-network-adapter-to-a-server"></a>Azure hálózati adapter hozzáadása egy kiszolgálóhoz
Az Azure hálózati adapter konfigurálásához nyissa meg a hálózati bővítményt a Windows felügyeleti központban.

A Windows felügyeleti központban:
1. Navigáljon az Azure hálózati adapterhez hozzáadni kívánt virtuális gépeket üzemeltető kiszolgálóra.
1. Az **eszközök**területen válassza a **hálózatok**elemet.
1. Válassza az **Azure hálózati adapter hozzáadása**lehetőséget.
1. Az **Azure hálózati adapter hozzáadása** panelen adja meg a következő szükséges adatokat, majd válassza a **Létrehozás**lehetőséget:
    - **Előfizetés**
    - **Hely**
    - **Virtual Network**
    - **Átjáró-alhálózat** (ha nem létezik)
    - **ÁTJÁRÓ SKU** (ha nem létezik)
    - **Ügyfél címterület**

        Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely VPN-kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak dinamikusan IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azt a virtuális hálózatot, amelyhez csatlakozni szeretne. Javasoljuk, hogy a magánhálózatok (10. x. x. x, 192.168. x. x vagy 172.16.0.0 – 172.31.255.255) számára kijelölt tartományokban lévő IP-címeket használja.

    - **Hitelesítési tanúsítvány**

        Az Azure tanúsítványok használatával hitelesíti a virtuális hálózathoz csatlakozó ügyfeleket pont – hely típusú VPN-kapcsolaton keresztül. A főtanúsítvány nyilvánoskulcs-információit a rendszer feltölti az Azure-ba. Ezt követően a főtanúsítványt az Azure "megbízhatónak" tekinti a virtuális hálózathoz pont – hely kapcsolathoz. Az ügyféltanúsítványt a megbízható főtanúsítványból kell létrehozni, és az ügyfél-kiszolgálón kell telepíteni. Az ügyféltanúsítvány segítségével hitelesítheti az ügyfelet, amikor kezdeményezi a csatlakozást a virtuális hálózathoz.
    
        További tudnivalókat a [pont – hely típusú VPN-kapcsolat konfigurálása egy VNet a natív Azure tanúsítványalapú hitelesítés használatával](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)című szakaszban talál: Azure Portal.

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Az Azure hálózati adapter hozzáadása panel a Windows felügyeleti központban.":::

> [!NOTE]
> A virtuális hálózaton belül futó hálózati berendezések, például a VPN Gateway és Application Gateway további költségeket is igénybe. További információ: [Virtual Network díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/).

Ha nincs meglévő Azure Virtual Network-átjáró, a Windows felügyeleti központ létrehoz egyet. A telepítési folyamat akár 25 percet is igénybe vehet. Az Azure hálózati adapter létrehozása után a virtuális hálózatban lévő virtuális gépeket közvetlenül a kiszolgálóról érheti el.

Ha nincs szüksége a kapcsolatra, a **hálózatok**területen válassza ki a leválasztani kívánt Azure hálózati adaptert, majd a felső menüben válassza a **Leválasztás**lehetőséget, majd a **VPN-megerősítő leválasztása** előugró ablakban válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>Következő lépések
Az Azure Virtual Networkról további információt a következő témakörben talál:

- [Azure Virtual Network – Gyakori kérdések (GYIK)](/azure/virtual-network/virtual-networks-faq)
