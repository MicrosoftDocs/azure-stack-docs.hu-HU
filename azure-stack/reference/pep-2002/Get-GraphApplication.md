---
title: Azure Stack hub GraphApplication rendszerjogosultságú végpontjának beolvasása
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Get-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a100b974a3d622c0b9bb51d850d9a8435d72f549
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487090"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>Áttekintés
A Get-GraphApplication egy burkoló függvény, amely beolvassa a Graph alkalmazás információit a megadott alkalmazás nevéhez vagy azonosítóhoz.

## <a name="syntax"></a>Szintaxis

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Elindítja a Get-GraphApplicationGroup az ADFS-gépen a Graph-alkalmazás adatainak beszerzéséhez.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>Paraméterek

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Az alkalmazás AD FSban való regisztrálásának alkalmazás-azonosítója.

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

### <a name="-applicationname"></a>– ApplicationName
Az ADFS-ben regisztrált alkalmazás neve.

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
