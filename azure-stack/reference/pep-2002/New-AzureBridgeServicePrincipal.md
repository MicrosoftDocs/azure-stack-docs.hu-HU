---
title: A New-AzureBridgeServicePrincipal privilegizált végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-New-AzureBridgeServicePrincipal
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2aa3e3b3e2a04dba8e44396ecf3b4d3adbf1ded2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563091"
---
# <a name="new-azurebridgeserviceprincipal"></a>Új – AzureBridgeServicePrincipal

## <a name="synopsis"></a>Áttekintés
Létrehoz egy új szolgáltatásnevet a Azure Active Directoryban (Azure AD).

## <a name="syntax"></a>Szintaxis

```
New-AzureBridgeServicePrincipal [[-TenantId] <Object>] [[-AzureEnvironment] <Object>]
 [[-TimeoutInSeconds] <Object>] [[-RefreshToken] <Object>] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-azureenvironment"></a>– AzureEnvironment
 

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

### <a name="-tenantid"></a>– TenantId
 

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

### <a name="-refreshtoken"></a>-RefreshToken
 

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

### <a name="-timeoutinseconds"></a>– TimeoutInSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: 420
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