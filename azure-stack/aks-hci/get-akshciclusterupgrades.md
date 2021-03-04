---
title: Get-AksHciClusterUpgrades
author: jessicaguan
description: Az Get-AksHciClusterUpgrades PowerShell-parancs beolvassa az Azure Kubernetes Service-fürt elérhető Kubernetes-frissítéseit.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 3d5f4a6b67345baaa8df9b9eccc26d0a0503dafd
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873828"
---
# <a name="get-akshciclusterupgrades"></a>Get-AksHciClusterUpgrades

## <a name="synopsis"></a>Áttekintés
Szerezze be a rendelkezésre álló Kubernetes-frissítéseket egy Azure Kubernetes Service-fürthöz.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciClusterUpgrades -name <String>
                          
```

## <a name="description"></a>Leírás
Szerezze be a rendelkezésre álló Kubernetes-frissítéseket egy Azure Kubernetes Service-fürthöz.

## <a name="examples"></a>Példák

```powershell
PS C:\> Get-AksHciClusterUpgrades -name mycluster
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A fürt alfanumerikus neve.

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
