---
title: Azure Stack hub AzureStackStampInformation rendszerjogosultságú végpontjának beolvasása
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-AzureStackStampInformation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec2fb2a7e23cbce90d0ab8ebd75ed33a3e446013
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487209"
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

### <a name="example-1"></a>1\. példa
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


## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
