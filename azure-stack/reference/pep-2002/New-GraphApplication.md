---
title: A New-GraphApplication privilegizált végpontja Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-New-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b0a9aeb2e7ec1035f24d89409f5fa23ef13500fb
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486614"
---
# <a name="new-graphapplication"></a>Új – GraphApplication

## <a name="synopsis"></a>Áttekintés
A New-GraphApplication egy burkoló függvény, amely AD FS Graph-parancsmagokat hív meg AD FS.

## <a name="syntax"></a>Szintaxis

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>Leírás
Meghívja a New-GraphApplicationGroup on AD FS új alkalmazás AD FS gépen való hozzáadásához.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-Name
Az alkalmazás neve, amely legfeljebb 50 karakter hosszú lehet, a függvény a következőt fogja módosítani `Azurestack-$Name-$({guid}::{NewGuid}())` :, és visszaadja.

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

### <a name="-generateclientsecret"></a>-GenerateClientSecret
 

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
