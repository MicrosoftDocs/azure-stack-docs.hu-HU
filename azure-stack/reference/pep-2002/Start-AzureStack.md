---
title: Start-AzureStack Kiemelt végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-Start-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 70b0992239fc2f1f5cc5bb4b7ffb4b8615c94c0d
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563410"
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

### <a name="example-1"></a>1. példa
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

## <a name="next-steps"></a>További lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).