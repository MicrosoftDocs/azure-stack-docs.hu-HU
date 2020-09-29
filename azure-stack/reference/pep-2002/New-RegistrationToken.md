---
title: A New-RegistrationToken privilegizált végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-New-RegistrationToken
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b7150d07370f783542e9b3a0aabf86d541406a50
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486597"
---
# <a name="new-registrationtoken"></a>Új – RegistrationToken

## <a name="synopsis"></a>Áttekintés
Létrehoz egy új regisztrációs jogkivonatot.

## <a name="syntax"></a>Szintaxis

```
New-RegistrationToken [[-MsAssetTag] <Object>] [[-AgreementNumber] <Object>] [[-BillingModel] <Object>]
 [-UsageReportingEnabled] [-MarketplaceSyndicationEnabled] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-billingmodel"></a>-BillingModel
 

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

### <a name="-marketplacesyndicationenabled"></a>-MarketplaceSyndicationEnabled
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-usagereportingenabled"></a>-UsageReportingEnabled
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-agreementnumber"></a>-AgreementNumber
 

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

### <a name="-msassettag"></a>-MsAssetTag
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
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
