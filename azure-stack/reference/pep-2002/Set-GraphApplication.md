---
title: GraphApplication rendszerjogosultságú végpont beállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-set-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 44d1f75f243bb23e3a1823d96e7a3d877568abb9
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486240"
---
# <a name="set-graphapplication"></a>Set-GraphApplication

## <a name="synopsis"></a>Áttekintés
A set-GraphApplication egy burkoló függvény, amely AD FS Graph-parancsmagokat hív meg AD FS.

## <a name="syntax"></a>Szintaxis

```
Set-GraphApplication [-ClientCertificates <Object>] [-ClientRedirectUris <Object>]
 [-ApplicationIdentifier <Object>] [-ChangeClientSecret] [-ResetClientSecret] [-AsJob]
```

## <a name="description"></a>Leírás
Meghívja a set-GraphApplicationGroup on AD FS alkalmazást egy alkalmazás AD FS gépen való módosításához.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Paraméterek

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
 

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

### <a name="-clientredirecturis"></a>-ClientRedirectUris
Új alkalmazás létrehozásához használt átirányítási URI AD FSban.

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

### <a name="-clientcertificates"></a>-ClientCertificates
Új alkalmazás létrehozásához használt tanúsítvány AD FSban.

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

### <a name="-resetclientsecret"></a>-ResetClientSecret
 

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

### <a name="-changeclientsecret"></a>-ChangeClientSecret
 

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
