---
title: Start-SecretRotation privilegizált Endpoint parancsmag Azure Stack hubhoz
description: A PowerShell Azure Stack hub privilegizált végpontjának referenciája – Start-SecretRotation
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 8a3dee9ad26c37626c0f3383b874f149be4fac01
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742615"
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
Meghívja a Secret rotációs folyamatot egy Azure Stack hub rendszer infrastruktúra-titkaihoz. Alapértelmezés szerint csak a külső hálózati infrastruktúra-végpontok tanúsítványait forgatja. További részletekért tekintse [meg a titkok Elforgatása Azure stack központban](../../operator/azure-stack-rotate-secrets.md) című témakört.

## <a name="parameters"></a>Paraméterek

### <a name="-internal"></a>– Belső
A belső hálózati infrastruktúra-végpontok titkainak elforgatása.

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

```console
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun
```

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

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
