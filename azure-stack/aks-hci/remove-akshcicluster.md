---
title: Remove-AksHciCluster
description: A Remove-AksHciCluster PowerShell-parancs töröl egy felügyelt Kubernetes-fürtöt.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 868aa8a739c69ad6e29d73192f91c9bda73a3195
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873962"
---
# <a name="remove-akshcicluster"></a>Remove-AksHciCluster

## <a name="synopsis"></a>Áttekintés
Felügyelt Kubernetes-fürt törlése.

## <a name="syntax"></a>Syntax

### <a name="delete-a-managed-kubernetes-cluster"></a>Felügyelt Kubernetes-fürt törlése
```powershell
Remove-AksHciCluster -name 
                    [-force]   
```

## <a name="description"></a>Leírás
Felügyelt Kubernetes-fürt törlése.

## <a name="examples"></a>Példák

### <a name="delete-an-existing-managed-kubernetes-cluster"></a>Meglévő felügyelt Kubernetes-fürt törlése
```powershell
PS C:\> Remove-AksHciCluster -name myCluster
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
