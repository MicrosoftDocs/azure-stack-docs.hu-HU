---
title: AzureStack rendszerjogosultságú végpontjának leállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – stop-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 946d31132e4a916574e8fb051e9761f81eb7cdf2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563421"
---
# <a name="stop-azurestack"></a>Leállítás – AzureStack

## <a name="synopsis"></a>Áttekintés
Leállítja az összes Azure Stack hub-szolgáltatást.

## <a name="syntax"></a>Szintaxis

```
Stop-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Leállítja az összes Azure Stack szolgáltatást, és leállítja azokat a fizikai számítógépeket, amelyeken Azure Stack hub fut.

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