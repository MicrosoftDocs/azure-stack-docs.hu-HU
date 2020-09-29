---
title: SyslogClient rendszerjogosultságú végpont beállítása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-set-SyslogClient
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f1c96a8e9ac8c3a5b67d32d94d93d08f689fb553
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485985"
---
# <a name="set-syslogclient"></a>Set-SyslogClient

## <a name="synopsis"></a>Áttekintés
A syslog-ügyfél végpont-tanúsítványának importálása és alkalmazása.

## <a name="syntax"></a>Szintaxis

```
Set-SyslogClient [-OutputSeverity <Object>] [-PfxBinary <Object>] [-RemoveCertificate] [-CertPassword <Object>]
 [-AsJob]
```

## <a name="parameters"></a>Paraméterek

### <a name="-pfxbinary"></a>-PfxBinary
A tanúsítvány bináris formátumú.
A **Get-Content** paranccsal kinyerheti a tanúsítványfájl bájtjainak tömbjét.

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

### <a name="-certpassword"></a>-CertPassword
A tanúsítvány jelszava biztonságos karakterláncként.

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

### <a name="-removecertificate"></a>-RemoveCertificate
 

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

### <a name="-outputseverity"></a>-OutputSeverity
 

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
