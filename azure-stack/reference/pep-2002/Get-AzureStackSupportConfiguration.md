---
title: Azure Stack hub AzureStackSupportConfiguration rendszerjogosultságú végpontjának beolvasása
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 97f6bd0fb751885a2a4a4553aba0d7a9329e5458
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487226"
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

### <a name="example-1"></a>1\. példa
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

## <a name="notes"></a>Jegyzetek
A támogatásához a virtuális gépnek internetkapcsolatra van szüksége.

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
