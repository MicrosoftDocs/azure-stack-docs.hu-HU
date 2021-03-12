---
title: Tűzfalak konfigurálása Azure Stack HCI-hez
description: Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszerhez tartozó tűzfalak konfigurálásához.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 03/1/2021
ms.openlocfilehash: aa09fd93e24a4c592c7bb8b0e05c140d975f0ad6
ms.sourcegitcommit: 0429d1f61f3d1fb6282fee67c45ae4e6fb3034c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103234722"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Tűzfalak konfigurálása Azure Stack HCI-hez

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszerhez tartozó tűzfalak konfigurálásához. Ez magában foglalja a kapcsolódási követelményeket, valamint azt, hogy a szolgáltatás címkéi milyen IP-címeket biztosítanak az Azure-ban az operációs rendszernek. A témakör a Microsoft Defender-tűzfal frissítésének lépéseit is tartalmazza.

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények
Azure Stack HCI-nek rendszeresen kapcsolódnia kell az Azure-hoz. A hozzáférés csak a következőre korlátozódik:
- Jól ismert Azure-beli IP-címek
- Kimenő irány
- 443-es port (HTTPS)

További információ: "Azure Stack HCI connectivity" című rész, [Azure stack HCI – gyakori kérdések](../faq.yml)

Ez a témakör azt ismerteti, hogyan lehet egy nagyszámú zárolt tűzfal-konfigurációt használni az összes célhelyre irányuló forgalom letiltására, kivéve az engedélyezési listán szereplőket.

   >[!IMPORTANT]
   > Ha a kimenő kapcsolatot a külső vállalati tűzfal vagy a proxykiszolgáló korlátozza, ügyeljen arra, hogy az alábbi táblázatban szereplő URL-címek ne legyenek letiltva. Kapcsolódó információk: az [Azure arc-kompatibilis kiszolgálók ügynökének áttekintését](/azure/azure-arc/servers/agent-overview#networking-configuration)ismertető témakör "hálózati konfiguráció" szakasza.

Ahogy az alábbi ábrán is látható, Azure Stack a HCI több tűzfallal is hozzáfér az Azure-hoz.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="A diagram megjeleníti Azure Stack HCI eléréséhez a szolgáltatás-címkézési végpontokat a tűzfalak 443-as (HTTPS) portján keresztül." lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Szolgáltatás-címkék használata
A *szolgáltatási címke* az adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatás címkéjén található IP-címeket, és automatikusan frissíti a szolgáltatási címkét az IP-címek változásával, hogy a frissítések minimálisra maradjanak. További információ: [Virtual Network Service Tags](/azure/virtual-network/service-tags-overview).

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Szükséges végponti napi hozzáférés (az Azure-regisztráció után)
Az Azure jól ismert IP-címeket tart fenn az Azure-szolgáltatások számára, amelyek szolgáltatás-címkék használatával vannak rendszerezve. Az Azure minden szolgáltatás IP-címének heti JSON-fájlját közzéteszi. Az IP-címek gyakran változnak, de évente néhány alkalommal változnak. A következő táblázat az operációs rendszer eléréséhez szükséges szolgáltatási címke végpontokat mutatja be.

| Leírás                   | Az IP-címtartomány szolgáltatási címkéje  | URL-cím                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | AzureActiveDirectory      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure Resource Manager        | AzureResourceManager      | `https://management.azure.com`                        |
| Azure Stack HCI Cloud Service | AzureFrontDoor. frontend   | `https://azurestackhci.azurefd.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | A használni kívánt funkcióktól függ:<br> Hibrid identitási szolgáltatás: `*.his.arc.azure.com`<br> Vendég konfigurációja: `*.guestconfiguration.azure.com`<br> **Megjegyzés:** További URL-címek várnak további funkciókat. |

## <a name="update-microsoft-defender-firewall"></a>A Microsoft Defender-tűzfal frissítése
Ez a szakasz bemutatja, hogyan konfigurálhatja a Microsoft Defender-tűzfalat úgy, hogy engedélyezze az operációs rendszerhez való kapcsolódáshoz a szolgáltatási címkéhez társított IP-címeket:

1. Töltse le a JSON-fájlt a következő erőforrásból az operációs rendszert futtató célszámítógépen: [Azure IP-tartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519).

1. A JSON-fájl megnyitásához használja a következő PowerShell-parancsot:

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. Egy adott szolgáltatási címke IP-címtartományok listájának lekérése, például a "AzureResourceManager" szolgáltatás címkéje:

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. Importálja az IP-címek listáját a külső vállalati tűzfalra, ha engedélyezési listát használ hozzá.

1. Hozzon létre egy tűzfalszabályot a fürt minden kiszolgálóján, hogy engedélyezze a kimenő 443 (HTTPS) forgalmat az IP-címtartományok listájára:

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>További végpont egy egyszeri Azure-regisztrációhoz
Az Azure regisztrációs folyamata során `Register-AzStackHCI` a vagy a Windows felügyeleti központ futtatásakor a parancsmag megpróbálja felvenni a kapcsolatot a PowerShell-Galéria annak ellenőrzéséhez, hogy rendelkezik-e a szükséges PowerShell-modulok legújabb verziójával, például az az és a AzureAD.

Bár a PowerShell-galéria az Azure-ban üzemelteti, jelenleg nincs szolgáltatás címkéje. Ha nem tudja futtatni a `Register-AzStackHCI` parancsmagot egy kiszolgáló-csomópontról, mert nincs internet-hozzáférés, javasoljuk, hogy töltse le a modulokat a felügyeleti számítógépére, majd manuálisan vigye át azokat a kiszolgálói csomópontra, amelyen a parancsmagot futtatni szeretné.

## <a name="set-up-a-proxy-server"></a>Proxykiszolgáló beállítása
Ha Azure Stack HCI-hez szeretne proxykiszolgálót beállítani, futtassa a következő PowerShell-parancsot rendszergazdaként a fürt minden kiszolgálóján:

```powershell
Set-WinInetProxy -ProxySettingsPerUser 0 -ProxyServer webproxy1.com:9090
```

Használja a `ProxySettingsPerUser 0` jelzőt, hogy a proxy konfigurációs kiszolgálóját felhasználónként nem, felhasználónként, ez az alapértelmezett érték legyen.

A proxy konfigurációjának eltávolításához futtassa a PowerShell-parancsot `Set-WinInetProxy` argumentumok nélkül.

Töltse le a WinInetProxy. psm1 szkriptet a következő címen: [PowerShell-Galéria | WinInetProxy. psm1 0.1.0](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0/Content/WinInetProxy.psm1).

   >[!NOTE]
   > A Windows felügyeleti központban a **proxy** beállítással átirányítja a Windows felügyeleti központ kimenő forgalmát (például a bővítmények letöltése, az Azure-hoz való csatlakozást stb.).

## <a name="network-port-requirements"></a>Hálózati portokra vonatkozó követelmények
Győződjön meg arról, hogy a megfelelő hálózati portok nyitva vannak az összes kiszolgáló-csomópont között egy helyen és a helyek között (a kifeszített fürtök esetében). Szüksége lesz a megfelelő tűzfal-és útválasztó-szabályokra az ICMP, az SMB (445-es port, a 5445-es port, az SMB Direct esetében) és a WS-MAN (port 5985) kétirányú forgalom engedélyezéséhez a fürt összes kiszolgálója között.

Ha a fürt létrehozásához a Windows felügyeleti központban a fürt létrehozása varázslót használja, a varázsló automatikusan megnyitja a megfelelő tűzfal-portokat a fürt minden kiszolgálóján a feladatátvételi fürtszolgáltatás, a Hyper-V és a tárolási replika számára. Ha az egyes kiszolgálókon eltérő tűzfalat használ, nyissa meg a következő portokat:

### <a name="failover-clustering-ports"></a>Feladatátvételi fürtszolgáltatás portjai
- ICMPv4 és ICMPv6
- 445-es TCP-port
- Dinamikus RPC-portok
- 135-es TCP-port
- 137-es TCP-port
- 3343-es TCP-port
- 3343-es UDP-port

### <a name="hyper-v-ports"></a>Hyper-V-portok
- 135-es TCP-port
- 80-es TCP-port (HTTP-kapcsolat)
- 443-es TCP-port (HTTPS-kapcsolat)
- 6600-es TCP-port
- 2179-es TCP-port
- Dinamikus RPC-portok
- RPC végpontleképező
- 445-es TCP-port

### <a name="storage-replica-ports-stretched-cluster"></a>Tárolási replika portjai (kifeszített fürt)
- 445-es TCP-port
- TCP 5445 (ha iWarp RDMA használ)
- 5985-es TCP-port
- ICMPv4 és ICMPv6 (ha a `Test-SRTopology` PowerShell-parancsmagot használja)

## <a name="next-steps"></a>Következő lépések
További információért lásd még:
- A Azure Stack HCI kapcsolódási szakasza [– Gyakori kérdések](../faq.yml)