---
title: GraphApplication-jogosultságú végpont eltávolítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Remove-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 164a20928f312d2ffa4dea73f54c05f41809ebf5
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562871"
---
# <a name="remove-graphapplication"></a>Remove-GraphApplication

## <a name="synopsis"></a>Áttekintés
A Remove-GraphApplication egy burkoló függvény, amely AD FS Graph-parancsmagokat hív meg AD FS.

## <a name="syntax"></a>Szintaxis

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
A AD FS Remove-GraphApplicationGroup meghívásával eltávolítja a megadott alkalmazást a AD FS gépen.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>Paraméterek

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
A törlendő alkalmazás azonosítója

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