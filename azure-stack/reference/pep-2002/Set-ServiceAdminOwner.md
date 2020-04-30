---
title: ServiceAdminOwner rendszerjogosultságú végpont beállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-set-ServiceAdminOwner
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b44a15c2b5ae1569e747a0c371d11950b5a40297
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562772"
---
# <a name="set-serviceadminowner"></a>Set-ServiceAdminOwner

## <a name="synopsis"></a>Áttekintés
A szolgáltatás-rendszergazda frissítésére szolgáló parancsfájl.

## <a name="syntax"></a>Szintaxis

```
Set-ServiceAdminOwner [[-TimeoutInSecs] <Object>] [[-ServiceAdminOwnerUpn] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Parancsfájl a szolgáltatás-rendszergazdai egyszerű felhasználónév frissítéséhez.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa
```
Set-ServiceAdminOwner -NewServiceAdminUpn "administrator@contoso.com" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Paraméterek

### <a name="-serviceadminownerupn"></a>-ServiceAdminOwnerUpn
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

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