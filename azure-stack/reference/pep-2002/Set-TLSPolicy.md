---
title: TLSPolicy rendszerjogosultságú végpont beállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-set-TLSPolicy
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a662ccbc76d25a936bd91bfe7ec499458c538321
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562695"
---
# <a name="set-tlspolicy"></a>Set-TLSPolicy

## <a name="syntax"></a>Szintaxis

```
Set-TLSPolicy [[-Version] <Object>] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-version"></a>– Verzió
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Accepted values: TLS_1.2, TLS_All

Required: False
Position: 0
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

## <a name="next-steps"></a>További lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).