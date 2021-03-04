---
title: Update-AksHciCluster
author: jessicaguan
description: A Update-AksHciCluster PowerShell-parancs egy felügyelt Kubernetes-fürtöt egy újabb Kubernetes vagy operációsrendszer-verzióra frissít.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a8fc5c1e87ed78891333af7a3a998767110428ea
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873772"
---
# <a name="update-akshcicluster"></a>Update-AksHciCluster

## <a name="synopsis"></a>Áttekintés
Felügyelt Kubernetes-fürt frissítése újabb Kubernetes vagy operációsrendszer-verzióra.

## <a name="syntax"></a>Syntax

```powershell
Update-AksHciCluster -name <String>
                    [-kubernetesVersion <String>]
                    [-operatingSystem]
```

## <a name="description"></a>Leírás
Felügyelt Kubernetes-fürt frissítése újabb Kubernetes vagy operációsrendszer-verzióra.

## <a name="examples"></a>Példák

### <a name="example"></a>Példa
```powershell
PS C:\> Update-AksHciCluster -clusterName myCluster -kubernetesVersion v1.18.8 -operatingSystem
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
Azon Kubernetes verziója, amelyre frissíteni kíván.

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

### <a name="-operatingsystem"></a>– operatingSystem
Akkor használja ezt a jelzőt, ha frissíteni szeretné az operációs rendszer következő verziójára.

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

