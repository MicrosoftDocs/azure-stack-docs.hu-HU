---
title: Azure Stack hub PrivilegedEndpoint Kiemelt jogosultságú végpontja
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont – Bezárás – PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 1b0df5892149d8cd351e3a222b11cf63c9d37759
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562706"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>Áttekintés
Beállítja a syslog-kiszolgáló végpontját.

## <a name="syntax"></a>Szintaxis

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>2. példa
```
-NoEncryption
```

### <a name="example-3"></a>3. példa
```
-SkipCertificateCheck
```

### <a name="example-4"></a>4. példa
```
-SkipCNCheck
```

### <a name="example-5"></a>5. példa
```
-UseUDP
```

### <a name="example-6"></a>6. példa
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>Paraméterek

### <a name="-servername"></a>– ServerName
A syslog-kiszolgáló teljes tartományneve vagy IPv4-címe.
Más paraméterek nélkül az alapértelmezett protokoll a TCP és az engedélyezett titkosítás; Tanúsítvány ellenőrzése; Tanúsítvány nevének-ellenőrzését.

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

### <a name="-serverport"></a>-ServerPort
A syslog-kiszolgáló kiszolgálói portja.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>– Nincs titkosítás
Ha szeretné letiltani a TCP-forgalom titkosítását, válassza a titkosítást.

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

### <a name="-skipcncheck"></a>-SkipCNCheck
Adja meg a SkipCNCheck, ha szeretné kihagyni a tanúsítvány nevének ellenőrzését.

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

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
Ha szeretné kihagyni a syslog-kiszolgáló tanúsítványát, válassza a SkipCertificateCheck lehetőséget.

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

### <a name="-useudp"></a>-UseUDP
UseUDP-t kell megadni, ha a syslog-kiszolgáló UDP protokollt használ.

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

### <a name="-remove"></a>-Remove
 

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