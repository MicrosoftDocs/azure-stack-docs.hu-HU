---
title: Get-AksHciKubernetesVersion
author: jessicaguan
description: A Get-AksHciKubernetesVersion PowerShell-parancs felsorolja a felügyelt Kubernetes-fürtök létrehozásához rendelkezésre álló verziókat.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 76b87c1ddef2943181d30d43cf0bc06da644b110
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873908"
---
# <a name="get-akshcikubernetesversion"></a>Get-AksHciKubernetesVersion

## <a name="synopsis"></a>Áttekintés
A felügyelt Kubernetes-fürt létrehozásához elérhető verziók felsorolása.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciKubernetesVersion
```

## <a name="description"></a>Leírás
A felügyelt Kubernetes-fürt létrehozásához elérhető verziók felsorolása.

## <a name="examples"></a>Példák

### <a name="example"></a>Példa 
```powershell
PS C:\> Get-AksHciKubernetesVersion
```

```Output
Linux {v1.16.10, v1.16.15, v1.17.11, v1.17.13...}
Windows {v1.18.8, v1.18.10}
```
