---
title: Azure Stack hub AzureStackStampInformation rendszerjogosultságú végpontjának beolvasása
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-AzureStackStampInformation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3318ce344804174fe3252808d5857e93b8113e22
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562574"
---
# <a name="get-azurestackstampinformation"></a>Get-AzureStackStampInformation

## <a name="synopsis"></a>Áttekintés
A bélyegző információinak beolvasása.

## <a name="syntax"></a>Szintaxis

```
Get-AzureStackStampInformation [-AsJob]
```

## <a name="description"></a>Leírás
Importálja az ECE-ügyféloldali modult, és elindítja a műveleti tervet az ECE szolgáltatás felhőalapú szerepkörén.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa
```
Get-AzureStackStampInformation
```

## <a name="parameters"></a>Paraméterek

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