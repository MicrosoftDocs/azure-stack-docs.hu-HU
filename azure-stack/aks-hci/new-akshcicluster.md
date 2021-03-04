---
title: New-AksHciCluster
author: jessicaguan
description: A New-AksHciCluster PowerShell-parancs egy új felügyelt Kubernetes-fürtöt hoz létre.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d4b374ca093c60018d1dc68c3d5a39db68456298
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873847"
---
# <a name="new-akshcicluster"></a>New-AksHciCluster

## <a name="synopsis"></a>Áttekintés
Hozzon létre egy új felügyelt Kubernetes-fürtöt.

## <a name="syntax"></a>Syntax

```powershell
New-AksHciCluster -name <String>
                 [-kubernetesVersion <String>]
                 [-controlPlaneNodeCount <int>]
                 [-linuxNodeCount <int>]
                 [-windowsNodeCount <int>]
                 [-controlPlaneVmSize <VmSize>]
                 [-loadBalancerVmSize <VmSize>]
                 [-linuxNodeVmSize <VmSize>]
                 [-windowsNodeVmSize <VmSize>]
                 [-vnet <Virtual Network>]
                 [-primaryNetworkPlugin <Network Plugin>]   
                 [-enableAdAuth]
                 [-enableSecretsEncryption]          
```

## <a name="description"></a>Leírás

Hozzon létre egy új Azure Kubernetes-szolgáltatást Azure Stack HCI-fürtön.

## <a name="examples"></a>Példák

### <a name="new-aks-hci-cluster-with-required-params"></a>Új AK – HCI-fürt kötelező paraméterekkel.

```powershell
PS C:\> New-AksHciCluster -name myCluster
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A Kubernetes-fürt neve.

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

### <a name="-kubernetesversion"></a>-kubernetesVersion
A telepíteni kívánt Kubernetes verziója. Az alapértelmezett érték a legújabb verzió. Az elérhető verziók listájának lekéréséhez futtassa a parancsot `Get-AksHciKubernetesVersion` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: v1.18.8 or later
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
A vezérlő síkja csomópontjainak száma. Az alapértelmezett érték 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
A Kubernetes-fürtben található Linux-csomópontok száma. Az alapértelmezett érték 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
A Kubernetes-fürtben lévő Windows-csomópontok száma. Az alapértelmezett érték a 0. Windows-csomópontokat csak akkor telepíthet, ha a Kubernetes v 1.18.8 vagy újabb verzióját futtatja.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
A vezérlési sík virtuális gép mérete. Az alapértelmezett érték Standard_A4_V2. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-loadbalancervmsize"></a>-loadBalancerVmSize
A terheléselosztó virtuális gépe mérete. Az alapértelmezett érték Standard_A4_V2. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodevmsize"></a>-linuxNodeVmSize
A Linux-csomópont virtuális gépe mérete. Az alapértelmezett érték Standard_K8S3_v1. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsvmsize"></a>-windowsVmSize
A Windows-csomópont virtuális gépe mérete. Az alapértelmezett érték Standard_K8S3_v1. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>– vnet
A New-AksHciNetworkSetting paranccsal létrehozott AksHciNetworkSetting objektum neve.

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

### <a name="-primarynetworkplugin"></a>-primaryNetworkPlugin
A központi telepítéshez használandó hálózati beépülő modul. Ez a paraméter a vagy a argumentumot veszi figyelembe `flannel` `calico` . A tarka csak Linux rendszerű fürtök esetében érhető el. Ha a többhelyes elemet választja a fürthöz, a Windows-csomópontok hozzáadása nem engedélyezett.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: flannel
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enableadauth"></a>-enableADAuth
Ezzel a jelzővel engedélyezheti a Active Directoryt a Kubernetes-fürtben.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablesecretsencryption"></a>-enableSecretsEncryption
Ezzel a jelzővel engedélyezheti a titkosítást a Kubernetes-titkokban.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
