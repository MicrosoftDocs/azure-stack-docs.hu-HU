---
title: Set-telemetria
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Bezárás – PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ad20bb8ab278e1cb6f46d4fb245ea31b3e00f61a
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742678"
---
# <a name="set-telemetry"></a>Set-telemetria

## <a name="synopsis"></a>Áttekintés
Engedélyezheti vagy letilthatja a telemetria-adatok átvitelét a Microsoftnak.

## <a name="syntax"></a>Szintaxis

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Leírás
A set-telemetria parancsmaggal szabályozhatja, hogy a rendszer az telemetria adatait elküldi-e a Microsoftnak a beállításjegyzék megfelelő beállításának módosításával.

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

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
