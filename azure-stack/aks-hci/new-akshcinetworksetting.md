---
title: New-AksHciNetworkSetting
author: jessicaguan
description: A New-AksHciNetworkSetting PowerShell-parancs létrehoz egy objektumot egy új virtuális hálózat számára.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 5babbe99254b23d642696b7ce7c5b865105bd906
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873780"
---
# <a name="new-akshcinetworksetting"></a>New-AksHciNetworkSetting

## <a name="synopsis"></a>Áttekintés
Hozzon létre egy objektumot egy új virtuális hálózat számára.

## <a name="syntax"></a>Syntax
```powershell
New-AksHciNetworkSetting -vnetName <String>
                         -gateway <String>
                         -dnsServers <String[]>
                         -ipAddressPrefix <String>
                         -vipPoolStart <IP address>
                         -vipPoolEnd <IP address>
                         -k8sNodeIpPoolStart <IP address>
                         -k8sNodeIpPoolEnd <IP address>
                        [-vlanID <int>]
                    
```

## <a name="description"></a>Leírás
Hozzon létre egy virtuális hálózatot, amely beállítja a DHCP vagy a statikus IP-címet a vezérlő síkja, a terheléselosztó, az ügynök végpontjai és az összes Kubernetes-fürt csomópontjainak statikus IP-tartománya számára. Ez a parancsmag egy VirtualNetwork objektumot ad vissza, amely később a konfigurációs lépések során is felhasználható.

## <a name="examples"></a>Példák

### <a name="deploy-with-a-static-ip-environment"></a>Üzembe helyezés statikus IP-környezettel

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1" 
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16"
```

> [!NOTE]
> Az ebben a példában szereplő parancsban megadott értékeket testre kell szabni a környezetben.

### <a name="deploy-with-a-dhcp-environment"></a>Üzembe helyezés DHCP-környezettel

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" 
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData"
```

## <a name="parameters"></a>Paraméterek

### <a name="-vnetname"></a>– vnetName
A külső kapcsoló neve. Az elérhető kapcsolók neveinek listájának lekéréséhez futtassa a parancsot `Get-VMSwitch` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-gateway"></a>– átjáró
Az alhálózat alapértelmezett átjárójának IP-címe.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-dnsservers"></a>– dnsServers
Statikus IP-címmel rendelkező hálózat létrehozásakor szükséges. Az alhálózathoz használandó DNS-kiszolgálókat mutató IP-címek tömbje. Legalább egy és legfeljebb 3 kiszolgáló adható meg. azaz "8.8.8.8", "192.168.1.1".

```yaml
Type: System.String[]
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ipaddressprefix"></a>-ipAddressPrefix
A statikus IP-hozzárendeléshez használandó cím előtagja.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: True
Position: Named
Default value: external
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolstart"></a>-vipPoolStart
A VIP-készlet kezdő IP-címe. A címnek a DHCP-kiszolgáló által kiszolgált tartományon belül kell lennie, vagy az alhálózati CIDR megadott tartományon belül kell lennie. A rendszer a VIP-készlet IP-címeit fogja használni az API-kiszolgálóhoz és a Kubernetes-szolgáltatásokhoz. Ha DHCP-t használ, győződjön meg róla, hogy a virtuális IP-címei a DHCP IP-cím tartalékának részét képezik. Ha statikus IP-címet használ, győződjön meg arról, hogy a virtuális IP-címek ugyanahhoz az alhálózathoz tartoznak.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolend"></a>-vipPoolEnd
A VIP-készlet záró IP-címe. A címnek a DHCP-kiszolgáló által kiszolgált tartományon belül kell lennie, vagy az alhálózati CIDR megadott tartományon belül kell lennie. A rendszer a VIP-készlet IP-címeit fogja használni az API-kiszolgálóhoz és a Kubernetes-szolgáltatásokhoz. Ha DHCP-t használ, győződjön meg róla, hogy a virtuális IP-címei a DHCP IP-cím tartalékának részét képezik. Ha statikus IP-címet használ, győződjön meg arról, hogy a virtuális IP-címek ugyanahhoz az alhálózathoz tartoznak.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolstart"></a>-k8sNodeIpPoolStart
Egy virtuálisgép-készlet kezdő IP-címe. A címnek az alhálózat tartományában kell lennie. Ez a statikus IP-telepítések esetén szükséges.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolend"></a>-k8sNodeIpPoolEnd
Egy virtuálisgép-készlet záró IP-címe. A címnek az alhálózat tartományában kell lennie. Ez a statikus IP-telepítések esetén szükséges.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vlanid"></a>– vlanID
A megadott hálózat vLAN-azonosítója. Ha nincs megadva, a hálózat nem lesz címkézve.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

<!--- ### -macPoolName
The name of the MAC address pool that you wish to use for the Azure Kubernetes Service host VM. The pool will be created with the New-AksHciMacPoolSetting command.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
--->
