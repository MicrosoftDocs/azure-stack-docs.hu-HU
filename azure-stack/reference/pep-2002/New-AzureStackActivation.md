---
title: A New-AzureStackActivation privilegizált végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-New-AzureStackActivation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fdd30f54973d5e1d6485e89b30c1ae811c950822
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563058"
---
# <a name="new-azurestackactivation"></a>Új – AzureStackActivation

## <a name="synopsis"></a>Áttekintés
Azure Stack hub aktiválása.

## <a name="syntax"></a>Szintaxis

```
New-AzureStackActivation [[-TimeoutInSeconds] <Object>] [[-ActivationKey] <Object>] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-activationkey"></a>-ActivationKey
 

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

### <a name="-timeoutinseconds"></a>– TimeoutInSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 1000
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