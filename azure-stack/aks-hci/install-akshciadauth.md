---
title: Install-AksHciAdAuth
author: jessicaguan
description: A Install-AksHciAdAuth PowerShell-parancs telepíti Active Directory hitelesítést.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d89861da54cf7fef44d8da57077aba9f372022c3
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873863"
---
# <a name="install-akshciadauth"></a>Install-AksHciAdAuth

## <a name="synopsis"></a>Áttekintés
Active Directory hitelesítés telepítése.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUser <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroup <String>    
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUserSID <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroupSID <String>    
                     [-TTL]    
                    
```

## <a name="description"></a>Leírás
Active Directory hitelesítés telepítése.

## <a name="examples"></a>PÉLDÁK

### <a name="example"></a>Példa
```powershell
PS C:\> Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A Kubernetes-fürt alfanumerikus neve.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-keytab"></a>– keytab kiterjesztésű
A keytab kiterjesztésű fájlt tartalmazó fájl elérési útja. Győződjön meg arról, hogy a fájl neve `current.keytab` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-previouskeytab"></a>-previousKeytab
A Active Directory fiók jelszavának frissítése előtt használt korábbi keytab kiterjesztésű. Ez a Active Directory jelszavának módosításakor szükséges.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-spn"></a>– SPN
Az API-kiszolgáló AD-fiókjához társított szolgáltatásnév neve.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminuser"></a>– adminUser
Annak a rendszergazdának a felhasználóneve, amely a fürt rendszergazdai engedélyeit adja meg.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroup"></a>-adminGroup
A csoport neve, amely a fürt rendszergazdai engedélyeit adja meg.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminusersid"></a>-adminUserSID
A felhasználói SID-nek meg kell adni a fürt rendszergazdai engedélyeit.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroupsid"></a>-adminGroupSID
A csoport biztonsági azonosítója, amely a fürt rendszergazdai engedélyeit adja meg.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ttl"></a>– TTL
Az adott időponthoz tartozó élettartam (órában) `-previousKeytab` . Az alapértelmezett érték 10 óra.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 10
Accept pipeline input: False
Accept wildcard characters: False
```
