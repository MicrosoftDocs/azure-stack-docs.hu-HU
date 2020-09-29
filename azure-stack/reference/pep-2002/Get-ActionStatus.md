---
title: Get-ActionStatusprivileged végpont Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-ActionStatus
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 35d857ff8f118e587246f6d10d7f1453b9dec193
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487265"
---
# <a name="get-actionstatus"></a>Get-ActionStatus

## <a name="synopsis"></a>Áttekintés
A művelethez tartozó legújabb művelet állapotának beolvasása a megadott függvény nevével.

## <a name="syntax"></a>Szintaxis

```
Get-ActionStatus [[-ActionType] <Object>] [[-FunctionName] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
A művelethez tartozó legújabb művelet állapotának beolvasása a megadott függvény nevével.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
Get-ActionStatus -FunctionName "Start-SecretRotation"
```

## <a name="parameters"></a>Paraméterek

### <a name="-functionname"></a>-Függvénynév
Annak a függvénynek a neve, amelynek az állapotát le kell kérdezni.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-actiontype"></a>– Művelettípus
A művelet műveletének típusa.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>– AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
