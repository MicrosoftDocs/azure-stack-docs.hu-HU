---
title: Azure Stack hub PrivilegedEndpoint Kiemelt jogosultságú végpontja
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Bezárás – PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: dafeb8c162ef79b5522a80d398f2bc440164cdc2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562970"
---
# <a name="register-customdnsserver"></a>Regisztráció – CustomDnsServer

## <a name="synopsis"></a>Áttekintés
Parancsfájl Azure Stack hub DNS-sel rendelkező egyéni DNS-kiszolgálók regisztrálásához. Ez a szkript Azure Stack hub DNS-ben hozza létre a feltételes továbbítást.

## <a name="syntax"></a>Szintaxis

```
Register-CustomDnsServer [[-TimeoutInSecs] <Object>] [[-CustomDomainName] <Object>]
 [[-CustomDnsIPAddresses] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Parancsfájl Azure Stack DNS-sel rendelkező egyéni DNS-kiszolgálók regisztrálásához.
Ez a szkript a feltételes továbbítást Azure Stack DNS-ben hozza létre

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa
```
Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "10.20.20.01","10.20.20.02" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Paraméterek

### <a name="-customdomainname"></a>-CustomDomainName
 

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

### <a name="-customdnsipaddresses"></a>-CustomDnsIPAddresses
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: 1000
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