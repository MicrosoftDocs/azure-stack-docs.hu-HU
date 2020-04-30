---
title: Azure Stack hub PrivilegedEndpoint Kiemelt jogosultságú végpontja
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Bezárás – PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b61955c6fce760654430b999c3b393e9093db535
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563454"
---
# <a name="start-secretrotation"></a>Start – SecretRotation

## <a name="synopsis"></a>Áttekintés
Elindít egy bélyegzőn a titkos elforgatást.

## <a name="syntax"></a>Szintaxis

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Meghívja a titkos kód elforgatási folyamatát.

## <a name="parameters"></a>Paraméterek

### <a name="-internal"></a>– Belső
 

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

### <a name="-pfxfilespath"></a>-PfxFilesPath
A külső tanúsítványok elforgatásához megosztott új pfx-fájlok elérési útja.
Kötelező, ha külső tanúsítványok elforgatása szükséges.

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

### <a name="-pathaccesscredential"></a>-PathAccessCredential
A PfxFilesPath eléréséhez szükséges hitelesítő adatok.
Kötelező, ha külső tanúsítványok elforgatása szükséges.

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

### <a name="-certificatepassword"></a>– CertificatePassword
A megadott új pfx-fájlok jelszava.
Kötelező, ha külső tanúsítványok elforgatása szükséges.
Ettől eltérő lehet a kezdeti központi telepítési időpontban megadott eredeti pfx-jelszótól.
A pfx-fájlokat a megfelelő HITELESÍTÉSSZOLGÁLTATÓI jelszóval hozza létre újra.

Használat::
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun

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

### <a name="-rerun"></a>– Újrafuttatás
 

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