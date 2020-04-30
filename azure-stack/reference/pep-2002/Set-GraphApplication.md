---
title: GraphApplication rendszerjogosultságú végpont beállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-set-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 745caa2016e4a8430d58d3644accbafe7e0923b6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562783"
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

### <a name="example-1"></a>1. példa
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

## <a name="next-steps"></a>További lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).