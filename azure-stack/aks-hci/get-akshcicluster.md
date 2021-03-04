---
title: Get-AksHciCluster
author: jessicaguan
description: A Get-AksHciCluster PowerShell-parancs felsorolja a Kubernetes felügyelt fürtöket, beleértve az Azure Kubernetes Service hostt is.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 14d7f807ef04432e7ea8c3c57dbb4bb80c64731b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873799"
---
# <a name="get-akshcicluster"></a>Get-AksHciCluster

## <a name="synopsis"></a>Áttekintés
Kubernetes felügyelt fürtök listázása, beleértve az Azure Kubernetes Service hostt.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCluster [-name <String>]
```

## <a name="description"></a>Leírás
Kubernetes felügyelt fürtök listázása, beleértve az Azure Kubernete Service hostt.

## <a name="examples"></a>Példák

### <a name="list-all-kubernetes-clusters"></a>Az összes Kubernetes-fürt listázása
```powershell
PS C:\> Get-AksHciCluster
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A fürt neve.

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
