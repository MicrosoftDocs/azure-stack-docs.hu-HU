---
title: Start-AzureStack Kiemelt végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-Start-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2a8814182d518c7327a64324cdc844018e989f18
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485849"
---
# <a name="start-azurestack"></a>Start – AzureStack

## <a name="synopsis"></a>Áttekintés
Elindítja az összes Azure Stack hub-szolgáltatást.

## <a name="syntax"></a>Szintaxis

```
Start-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Elindítja az összes Azure Stack hub-szolgáltatást.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```powershell
PS C:\> 
```



## <a name="parameters"></a>Paraméterek

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
Az a maximális időtartam, ameddig a végrehajtás le lesz állítva.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
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
