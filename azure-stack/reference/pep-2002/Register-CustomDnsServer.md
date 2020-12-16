---
title: Register-CustomDnsServer
description: Ismerkedjen meg az egyéni DNS-kiszolgálók regisztrálásához szükséges parancsfájllal Azure Stack hub DNS-Register-CustomDnsServer.
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 88d24d8ee32f82f9f19f256249241bf8b0060641
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598266"
---
# <a name="register-customdnsserver"></a>Register-CustomDnsServer

## <a name="synopsis"></a>Áttekintés
Parancsfájl Azure Stack hub DNS-sel rendelkező egyéni DNS-kiszolgálók regisztrálásához. Ez a szkript Azure Stack hub DNS-ben hozza létre a feltételes továbbítást.

## <a name="syntax"></a>Syntax

```
Register-CustomDnsServer [[-TimeoutInSecs] <Object>] [[-CustomDomainName] <Object>]
 [[-CustomDnsIPAddresses] <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Parancsfájl Azure Stack DNS-sel rendelkező egyéni DNS-kiszolgálók regisztrálásához.
Ez a szkript a feltételes továbbítást Azure Stack DNS-ben hozza létre

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
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

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
