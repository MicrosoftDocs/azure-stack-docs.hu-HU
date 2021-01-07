---
title: Tűzfalak konfigurálása Azure Stack HCI-hez
description: Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszerhez tartozó tűzfalak konfigurálásához.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 73e58c69295afc1a1bb106ea078e753647daa08d
ms.sourcegitcommit: c5d46662492887b70a599a60f3c3d27e3460a742
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97965494"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Tűzfalak konfigurálása Azure Stack HCI-hez

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszerhez tartozó tűzfalak konfigurálásához. Ez magában foglalja a kapcsolódási követelményeket, valamint azt, hogy a szolgáltatás címkéi milyen IP-címeket biztosítanak az Azure-ban az operációs rendszernek. A témakör a Microsoft Defender-tűzfal frissítésének lépéseit is tartalmazza.

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények
Azure Stack HCI-nek rendszeresen kapcsolódnia kell az Azure-hoz. A hozzáférés csak a következőre korlátozódik:
- Jól ismert Azure-beli IP-címek
- Kimenő irány
- 443-es port (HTTPS)

További információ: "Azure Stack HCI connectivity" című rész, [Azure stack HCI – gyakori kérdések](../faq.md)

Ez a témakör azt ismerteti, hogyan lehet egy nagyszámú zárolt tűzfal-konfigurációt használni az összes célhelyre irányuló forgalom letiltására, kivéve az engedélyezési listán szereplőket.

   >[!IMPORTANT]
   > Ha a kimenő kapcsolatot a külső vállalati tűzfal vagy a proxykiszolgáló korlátozza, ügyeljen arra, hogy az alábbi táblázatban szereplő URL-címek ne legyenek letiltva. Kapcsolódó információk: az [Azure arc-kompatibilis kiszolgálók ügynökének áttekintését](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#networking-configuration)ismertető témakör "hálózati konfiguráció" szakasza.


Ahogy az alábbi ábrán is látható, Azure Stack a HCI több tűzfallal is hozzáfér az Azure-hoz.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="A diagram megjeleníti Azure Stack HCI eléréséhez a szolgáltatás-címkézési végpontokat a tűzfalak 443-as (HTTPS) portján keresztül." lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Szolgáltatás-címkék használata
A *szolgáltatási címke* az adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatás címkéjén található IP-címeket, és automatikusan frissíti a szolgáltatási címkét az IP-címek változásával, hogy a frissítések minimálisra maradjanak. További információ: [Virtual Network Service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview).

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
Az Azure regisztrációs folyamata során `Register-AzStackHCI` a vagy a Windows felügyeleti központ futtatásakor a parancsmag megpróbálja felvenni a kapcsolatot a PowerShell-Galéria annak ellenőrzéséhez, hogy rendelkezik-e a szükséges PowerShell-modulok legújabb verziójával, például az az és a AzureAD. Bár a PowerShell-galéria az Azure-ban üzemelteti, jelenleg nincs szolgáltatás címkéje. Ha nem tudja futtatni a `Register-AzStackHCI` parancsmagot egy kiszolgáló-csomópontról, mert nincs internet-hozzáférés, javasoljuk, hogy töltse le a modulokat a felügyeleti számítógépére, majd manuálisan vigye át azokat a kiszolgálói csomópontra, amelyen a parancsmagot futtatni szeretné.

## <a name="next-steps"></a>További lépések
További információért lásd még:
- A Azure Stack HCI kapcsolódási szakasza [– Gyakori kérdések](../faq.md)
