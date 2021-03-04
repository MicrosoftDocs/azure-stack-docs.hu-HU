---
title: Get-AksHciLogs
author: jessicaguan
description: A Get-AksHciLogs PowerShell-parancs létrehoz egy tömörített mappát, amely az összes hüvelyből származó naplókat használ.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: ef78efb91a6a8b5c0c91e815cb8aedd0a57adfe8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873903"
---
# <a name="get-akshcilogs"></a>Get-AksHciLogs

## <a name="synopsis"></a>Áttekintés
Hozzon létre egy tömörített mappát az összes hüvelyből származó naplókkal. 

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciLogs
```

## <a name="description"></a>Leírás
Hozzon létre egy tömörített mappát az összes hüvelyből származó naplókkal. Ez a parancs létrehoz egy kimeneti zip-mappát `akshcilogs.zip` , amelyet az AK-ban Azure stack HCI munkakönyvtárban kell létrehozni. A fájl teljes elérési útja `akshcilogs.zip` a Futtatás utáni kimenet lesz  `Get-AksHciLogs` (például `C:\AksHci\0.9.6.3\akshcilogs.zip` , hol `0.9.6.3` található az AK a Azure stack HCI kiadási számnál).

## <a name="examples"></a>Példák

### <a name="example"></a>Példa
```powershell
PS C:\> Get-AksHciLogs
```
