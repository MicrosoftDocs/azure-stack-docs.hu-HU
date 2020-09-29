---
title: Azure Stack hub AzureStack Kiemelt jogosultságú végpontja
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-test-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 4b64f3fa38b474fddb930915059f6406d25fd3c8
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485951"
---
# <a name="test-azurestack"></a>Test-AzureStack

## <a name="synopsis"></a>Áttekintés
Ellenőrzi Azure Stack hub állapotát.

## <a name="syntax"></a>Szintaxis

```
Test-AzureStack [-BackupShareCredential <Object>] [-Include <Object>] [-DetailedResults]
 [-ServiceAdminCredentials <Object>] [-List] [-Group <Object>] [-DoNotDeployTenantVm] [-Repair]
 [-Ignore <Object>] [-Confirm] [-TimeoutSeconds <Object>] [-AdminCredential <Object>]
 [-BackupSharePath <Object>] [-WhatIf] [-UseRunners] [-DisableConsoleOutput] [-AsJob]
```

## <a name="description"></a>Leírás
Ha nem észlelhető hibák, a test-AzureStack függvény visszaadja `$true` .
Ha a rendszer hibákat észlel, visszatérhet `$false` .

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

A test-AzureStack futtatása operátor és felhasználói forgatókönyv nélkül:
```
[azs-ercs01\]: PS\>Test-AzureStack
```

A test-AzureStack futtatása operátorral és felhasználói forgatókönyvekkel:
```
[azs-ercs01\]: PS\>Test-AzureStack -ServiceAdminCredentials \<PSCredential\>
```

## <a name="parameters"></a>Paraméterek

### <a name="-serviceadmincredentials"></a>-ServiceAdminCredentials
 

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

### <a name="-donotdeploytenantvm"></a>-DoNotDeployTenantVm
 

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

### <a name="-admincredential"></a>-AdminCredential
 

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

### <a name="-backupsharepath"></a>-BackupSharePath
 

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

### <a name="-backupsharecredential"></a>-BackupShareCredential
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: [PSCredential]::Empty
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-list"></a>-Lista
 

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

### <a name="-ignore"></a>– Mellőzés
 

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

### <a name="-include"></a>-Belefoglalás
 

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

### <a name="-detailedresults"></a>-DetailedResults
 

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

### <a name="-timeoutseconds"></a>-TimeoutSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1800
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-group"></a>-Csoport
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Default
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-repair"></a>– Javítás
 

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

### <a name="-userunners"></a>-UseRunners
 

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

### <a name="-disableconsoleoutput"></a>-DisableConsoleOutput
 

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

### <a name="-whatif"></a>– WhatIf
Bemutatja, mi történne a parancsmag futtatásakor.
A parancsmag nem fut.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-confirm"></a>-Confirm
Jóváhagyást kér a parancsmag futtatása előtt.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

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

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
