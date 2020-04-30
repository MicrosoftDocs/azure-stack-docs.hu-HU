---
title: Azure Stack hub AzureStackSupportConfiguration rendszerjogosultságú végpontjának beolvasása
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562552"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>Áttekintés
A támogatási szolgáltatás konfigurációs beállításainak beolvasása.

## <a name="syntax"></a>Szintaxis

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>Leírás
Támogatási szolgáltatás konfigurációs beállításai.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa
Az alábbi példa beolvassa a regisztráció részleteit, ha a bélyegző regisztrálva van vagy más null értékű.

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>Paraméterek

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
Választható.
Internetkapcsolat szükséges.
Beolvassa a regisztrációs azonosító objektum AZONOSÍTÓját.

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

## <a name="notes"></a>Megjegyzések
A támogatásához a virtuális gépnek internetkapcsolatra van szüksége.

## <a name="next-steps"></a>További lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).