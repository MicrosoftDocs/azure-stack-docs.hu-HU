---
title: Alaphelyzetbe állítás – DatacenterIntegrationConfiguration privilegizált végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – alaphelyzetbe állítás – DatacenterIntegrationConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 1fc28b06360de93ebef461d798f6004ad318341d
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486325"
---
# <a name="reset-datacenterintegrationconfiguration"></a>Alaphelyzetbe állítás – DatacenterIntegrationConfiguration

## <a name="synopsis"></a>Áttekintés
Az adatközpont-integráció változásainak visszaállítására szolgáló parancsfájl.

## <a name="syntax"></a>Szintaxis

```
Reset-DatacenterIntegrationConfiguration [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Az adatközpont-integráció változásainak visszaállítására szolgáló parancsfájl.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
Reset-DatacenterIntegrationConfiguration -TimeoutInSecs 2000
```

## <a name="parameters"></a>Paraméterek

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
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

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
