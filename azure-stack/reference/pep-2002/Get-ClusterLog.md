---
title: Get-ClusterLog Kiemelt végpont Azure Stack hub-hoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-Get-ClusterLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: c8da0312a9c0d3f3250ed603523fd28b13dab38e
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415368"
---
# <a name="get-clusterlog"></a>Get-ClusterLog

## <a name="syntax"></a>Szintaxis

```
Get-ClusterLog [-SkipClusterState] [-InputObject <Object>] [-Cluster <Object>] [-NetworkDiagnostics]
 [-Destination <Object>] [-NetworkDiagnosticsLevel <Object>] [[-Node] <Object>] [-TimeSpan <Object>]
 [-PerformanceHistoryTimeFrame <Object>] [-Health] [-ExportClusterPerformanceHistory] [-UseLocalTime] [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-cluster"></a>– Fürt
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-destination"></a>– Cél
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-exportclusterperformancehistory"></a>-ExportClusterPerformanceHistory
Exportálja a ClusterPorformanceHistory-adatkészletet.

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

### <a name="-health"></a>– Állapot
A fürt állapotának naplóinak előállítása.

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

### <a name="-inputobject"></a>– Inputobject elemnél
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnostics"></a>-NetworkDiagnostics
A fürt hálózati diagnosztikai naplóinak előállítása.

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

### <a name="-networkdiagnosticslevel"></a>-NetworkDiagnosticsLevel
Meghatározza a hálózati diagnosztikai naplók mélységének szintjét.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-node"></a>– Csomópont
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-performancehistorytimeframe"></a>-PerformanceHistoryTimeFrame
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipclusterstate"></a>-SkipClusterState
A fürt állapotával kapcsolatos információk beolvasása nélkül hozhatja elő a fürtöt.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: scs

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timespan"></a>– TimeSpan
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Span

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-uselocaltime"></a>-UseLocalTime
A fürt naplójának előállítása a GMT helyett helyi idő használatával.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: lt

Required: False
Position: Named
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

## <a name="next-steps"></a>További lépések

[https://go.microsoft.com/fwlink/?LinkId=216212](/powershell/module/failoverclusters/get-clusterlog)

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).