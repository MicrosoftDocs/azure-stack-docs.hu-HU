---
title: Set-AksHciClusterNodeCount
author: jessicaguan
description: A Set-AksHciClusterNodeCount PowerShell-parancs a fürtben lévő vezérlő sík-csomópontok vagy munkavégző csomópontok számát méretezi.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 1dff2962ee1997d6eaa696f6e0eeadf7ef2d59e1
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873951"
---
# <a name="set-akshciclusternodecount"></a>Set-AksHciClusterNodeCount

## <a name="synopsis"></a>Áttekintés
A vezérlő sík csomópontjainak vagy a munkavégző csomópontok számának méretezése a fürtben.

## <a name="syntax"></a>Syntax

### <a name="scale-control-plane-nodes"></a>Vezérlési sík csomópontjainak méretezése
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -controlPlaneNodeCount <int> 
```

### <a name="scale-worker-nodes"></a>Munkavégző csomópontok méretezése
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

## <a name="description"></a>Leírás
A vezérlő sík csomópontjainak vagy a munkavégző csomópontok számának méretezése a fürtben. A vezérlési sík csomópontjait és a munkavégző csomópontokat egymástól függetlenül kell méretezni.

## <a name="examples"></a>Példák

### <a name="scale-control-plane-nodes"></a>Vezérlési sík csomópontjainak méretezése
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -controlPlaneNodeCount 3
```

### <a name="scale-worker-nodes"></a>Munkavégző csomópontok méretezése
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -linuxNodeCount 2 -windowsNodeCount 2
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A Kubernetes-fürt alfanumerikus neve.

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

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
A vezérlő síkja csomópontjainak száma. Nincs alapértelmezett érték.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: none
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
A Kubernetes-fürtben található Linux-csomópontok száma. Az alapértelmezett érték 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
A proxykiszolgáló hitelesítéséhez használt tanúsítvány.
 
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

### <a name="-windowsnodecount"></a>-windowsNodeCount
A Kubernetes-fürtben lévő Windows-csomópontok száma. Az alapértelmezett érték 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
