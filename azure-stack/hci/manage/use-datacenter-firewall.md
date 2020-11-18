---
title: Adatközponti tűzfal használata az SDN-hez Azure Stack HCI-ben
description: Ebből a témakörből megtudhatja, hogyan kezdheti el az adatközponti tűzfalat Software-Defined hálózatkezeléshez Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 833780947bd698a0e39709668715372bd8508e90
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94881215"
---
# <a name="use-datacenter-firewall-for-software-defined-networking-in-azure-stack-hci"></a>Adatközponti tűzfal használata Software-Defined hálózatkezeléshez Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt a hozzáférés-vezérlési listák (ACL-ek) konfigurálásához az adatforgalom kezeléséhez a Windows PowerShell használatával a Azure Stack HCI-ben a szoftveresen definiált hálózatkezelés (SDN [) használatával.](../concepts/datacenter-firewall-overview.md) Az adatközpont tűzfalát az alhálózatra vagy hálózati adapterre alkalmazott ACL-ek létrehozásával engedélyezheti és konfigurálhatja. A jelen témakörben található parancsfájlok a **NetworkController** modulból exportált Windows PowerShell-parancsokat használják. Az ACL-ek konfigurálásához és kezeléséhez használhatja a Windows felügyeleti központot is.

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>Az adatközpont tűzfalának konfigurálása az összes forgalom engedélyezéséhez

Az SDN üzembe helyezése után tesztelje az alapszintű hálózati kapcsolatot az új környezetben. Ennek elvégzéséhez hozzon létre egy szabályt az adatközpont tűzfala számára, amely korlátozás nélkül engedélyezi az összes hálózati forgalmat.

Az alábbi táblázat bejegyzéseivel hozhat létre olyan szabályokat, amelyek engedélyezik az összes bejövő és kimenő hálózati forgalmat.

| Forrás IP-címe | Cél IP-címe | Protokoll | Forrásport | Célport | Irány | Műveletek | Prioritás |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   Mind    |     \*      |        \*        |  Bejövő  | Engedélyezés  |   100    |
|    \*     |       \*       |   Mind    |     \*      |        \*        | Kimenő  | Engedélyezés  |   110    |

---

Ebben a példában egy ACL-t hoz létre két szabállyal:

1. **AllowAll_Inbound** – lehetővé teszi, hogy az összes hálózati forgalom áthaladjon a hálózati adapterre, ahol az ACL konfigurálva van.
2. **AllowAllOutbound** – engedélyezi, hogy az összes forgalom kikerüljön a hálózati adapterről. A "AllowAll-1" erőforrás-AZONOSÍTÓval azonosított ACL mostantól készen áll a virtuális alhálózatokban és hálózati adapterekben való használatra.

Először kapcsolódjon a fürtcsomópontok egyikéhez egy PowerShell-munkamenet megnyitásával:

```PowerShell
Enter-PSSession <server-name>
```

Ezután futtassa a következő szkriptet az ACL létrehozásához:

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>A hálózati vezérlőhöz készült Windows PowerShell-parancs referenciája a [hálózati vezérlő parancsmagjai](/powershell/module/networkcontroller/)című témakörben található.

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>Az alhálózaton lévő adatforgalom korlátozására szolgáló ACL-ek használata
Ebben a példában egy ACL-t hoz létre, amely megakadályozza, hogy a 192.168.0.0/24 alhálózaton belül a virtuális gépek (VM-EK) kommunikálnak egymással. Ez a típusú ACL hasznos lehet arra, hogy korlátozza a támadók az alhálózaton belüli későbbi terjesztésének lehetőségét, miközben továbbra is lehetővé teszi a virtuális gépek számára az alhálózaton kívülről érkező kérések fogadását, valamint a más alhálózatokon lévő más szolgáltatásokkal való kommunikációt.

|   Forrás IP-címe    | Cél IP-címe | Protokoll | Forrásport | Célport | Irány | Műveletek | Prioritás |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   Mind    |     \*      |        \*        |  Bejövő  | Engedélyezés  |   100    |
|       \*       |  192.168.0.1   |   Mind    |     \*      |        \*        | Kimenő  | Engedélyezés  |   101    |
| 192.168.0.0/24 |       \*       |   Mind    |     \*      |        \*        |  Bejövő  | Letiltás  |   102    |
|       \*       | 192.168.0.0/24 |   Mind    |     \*      |        \*        | Kimenő  | Letiltás  |   103    |
|       \*       |       \*       |   Mind    |     \*      |        \*        |  Bejövő  | Engedélyezés  |   104    |
|       \*       |       \*       |   Mind    |     \*      |        \*        | Kimenő  | Engedélyezés  |   105    |

---

A lenti példaként létrehozott, a (z) **-192-168-0-0 azonosítójú alhálózat** által azonosított ACL mostantól alkalmazható a "192.168.0.0/24" alhálózati címeket használó virtuális hálózati alhálózatra. A virtuális hálózati alhálózathoz csatolt bármely hálózati adapter automatikusan megkapja a fenti ACL-szabályokat.

Az alábbi példa egy olyan parancsfájlt mutat be, amely az ACL-t a hálózati vezérlő REST API használatával hozza létre:

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>Hozzáférés-vezérlési lista hozzáadása hálózati adapterhez

Miután létrehozott egy ACL-t, és hozzárendelte azt egy virtuális alhálózathoz, érdemes lehet felülbírálni az alapértelmezett ACL-t a virtuális alhálózaton egy egyedi hálózati adapterhez tartozó ACL-sel. Ebben az esetben a virtuális hálózat helyett közvetlenül a VLAN hálózatokhoz csatolt hálózati adapterekre alkalmazza a megadott ACL-eket. Ha a hálózati adapterhez csatlakoztatott virtuális alhálózathoz ACL-ek vannak beállítva, a rendszer mindkét ACL-t alkalmazza, és rangsorolja a hálózati adapter hozzáférés-vezérlési listáin a virtuális alhálózat ACL-jeit.

Ebben a példában bemutatjuk, hogyan adhat hozzá egy ACL-t egy virtuális hálózathoz.

>[!TIP]
>Lehetőség van arra is, hogy egy ACL-t is hozzáadhat a hálózati adapter létrehozásához.

1. Szerezze be vagy hozza létre azt a hálózati adaptert, amelyhez hozzá fogja adni az ACL-t.

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Szerezze be vagy hozza létre a hálózati adapterhez hozzáadni kívánt ACL-t.

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. Rendelje hozzá az ACL-t a hálózati adapter AccessControlList tulajdonságához.

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. Adja hozzá a hálózati adaptert a hálózati vezérlőhöz.

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>Hozzáférés-vezérlési lista eltávolítása hálózati adapterről

Ebben a példában bemutatjuk, hogyan távolíthat el egy ACL-t egy hálózati adapterről. Az ACL eltávolításával az alapértelmezett szabályok érvényesek a hálózati adapterre. Az alapértelmezett szabályok lehetővé teszik az összes kimenő forgalmat, de blokkolja az összes bejövő forgalmat. Ha engedélyezni szeretné az összes bejövő forgalmat, az előző [példát](#add-an-acl-to-a-network-interface) követve adjon hozzá egy ACL-t, amely engedélyezi az összes bejövő és kimenő forgalmat.

1. Szerezze be azt a hálózati adaptert, amelyről el kívánja távolítani az ACL-t.
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Rendeljen $null a ipConfiguration AccessControlList tulajdonságához.
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. Adja hozzá a hálózati adapter objektumot a hálózati vezérlőben.
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>Tűzfal naplózása

A tűzfal naplózása új képesség az adatközpont tűzfala számára, amely rögzíti az SDN tűzfalszabályok által feldolgozott folyamatokat. A rendszer minden olyan ACL-t rögzít, amelyen engedélyezve van a naplózás. A naplófájloknak olyan szintaxissal kell rendelkezniük, amely konzisztens az [Azure Network Watcher flow naplóival](/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Ezeket a naplókat diagnosztikai célból vagy a későbbi elemzések archiválására is felhasználhatják.

Itt látható egy példa a tűzfal naplózásának engedélyezésére a gazdagép-kiszolgálókon. Frissítse a változókat az elején, majd futtassa ezt egy Azure Stack HCI-fürtön, amelyen telepítve van a [hálózati vezérlő](../concepts/network-controller-overview.md) :

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

Ha engedélyezve van, egy új fájl jelenik meg minden egyes gazdagépen óránként egyszer, a megadott könyvtárban.  Rendszeresen fel kell dolgoznia ezeket a fájlokat, és el kell távolítania azokat a gazdagépekről.  Az aktuális fájl nulla hosszúságú, és a következő órában megjelenő jel szerint van zárolva:

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

Ezek a fájlok a folyamat eseményeinek sorát tartalmazzák, például:

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

Megjegyzés: a naplózás csak azokra a szabályokra kerül sor, amelyeken **engedélyezve** van a **naplózás** , például:

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Az adatközpont tűzfala – áttekintés](../concepts/datacenter-firewall-overview.md)
- [Hálózati vezérlő – áttekintés](../concepts/network-controller-overview.md)
- [SDN Azure Stack HCI-ben](../concepts/software-defined-networking.md)