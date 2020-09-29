---
title: AzureStackDiagnosticLog rendszerjogosultságú végpontjának küldése Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Send-AzureStackDiagnosticLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2b62eb35f096ab5b38ee37ff776435bb90b7c210
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486189"
---
# <a name="send-azurestackdiagnosticlog"></a>Küldés – AzureStackDiagnosticLog

## <a name="synopsis"></a>Áttekintés
Azure Stack hub diagnosztikai naplókat küld a Microsoftnak.

## <a name="syntax"></a>Szintaxis

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Kérést küld a Bridge Service-nek a bélyegzőhöz tartozó diagnosztikai naplók feltöltésére.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

Az alábbi példa az utolsó 4 órás naplókat küldi el a Microsoftnak.

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>2\. példa
Az alábbi példa az elmúlt 4 órás támogatási szolgáltatást küldi, és a Microsoft számára naplózott.
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>Paraméterek

### <a name="-filterbyrole"></a>-FilterByRole
Választható.
Szűrés infrastruktúra-szerepkör típusa alapján.

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

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
Választható.
Szűrés érték szerint – erőforrás-szolgáltatói típus hozzáadása.

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

### <a name="-fromdate"></a>– FromDate
Választható.
A naplók gyűjtéséhez használandó kezdési idő.

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

### <a name="-todate"></a>-ToDate
Választható.
A naplók gyűjtéséhez használt befejezési idő.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
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
