---
title: SupportSession Kiemelt jogosultságú végpontjának zárolása Azure Stack hubhoz
description: Hivatkozás a PowerShell-Azure Stack privilegizált végpont – zárolás feloldása – SupportSession
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 7f035bf5aec66bd230506477c8001b7760c8f4df
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563289"
---
# <a name="unlock-supportsession"></a>Zárolás feloldása – SupportSession

## <a name="syntax"></a>Szintaxis

```
Unlock-SupportSession [[-ResponseToken] <Object>] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-responsetoken"></a>-ResponseToken


```yaml
Type: Object
Parameter Sets: (All)
Aliases:

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