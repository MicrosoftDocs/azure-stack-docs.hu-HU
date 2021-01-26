---
title: Set-Telemetry
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-Set-Telemetry
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 89e9182dd5ebb7a7ab9da6e9d4e7b21da6d4ce9e
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810917"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>Áttekintés
Engedélyezheti vagy letilthatja a telemetria-adatok átvitelét a Microsoftnak.

## <a name="syntax"></a>Syntax

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Leírás
A Set-Telemetry parancsmaggal szabályozhatja, hogy a rendszer elküldi-e az telemetria-adatait a Microsoftnak a beállításjegyzék megfelelő beállításának módosításával.

Pontosabban, ez a parancsmag konfigurálja a tartományi csoportházirendet úgy, hogy a telemetria beállításazonosító értékét állítsa 0 értékre, és állítsa le a Windows UTC szolgáltatást az összes infrastruktúra-virtuális gépen és gazdagépen.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>2\. példa
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>Paraméterek

### <a name="-enable"></a>– Engedélyezés
Engedélyezi a telemetria-adatok átvitelét a Microsoftnak.

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

### <a name="-disable"></a>– Letiltás
Letiltja a telemetria adatok átvitelét a Microsoftnak.

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

## <a name="next-steps"></a>További lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
