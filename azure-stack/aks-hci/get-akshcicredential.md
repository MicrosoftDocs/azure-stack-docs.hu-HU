---
title: Get-AksHciCredential
author: jessicaguan
description: A Get-AksHciCredential PowerShell-parancs a kubectl használatával fér hozzá a fürthöz.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 50f99f6e5a64a1a0b687c0dfdc42b407b4106500
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873911"
---
# <a name="get-akshcicredential"></a>Get-AksHciCredential

## <a name="synopsis"></a>Áttekintés
A fürt elérése a használatával `kubectl` . Ez a megadott fürt _kubeconfig_ -fájlját fogja használni alapértelmezett _kubeconfig_ -fájlként `kubectl` .

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCredential -name <String>
                    [-outputLocation <String>]
                    [-adAuth]
```

## <a name="description"></a>Leírás
A fürt elérése a kubectl használatával.

## <a name="examples"></a>Példák

### <a name="access-your-cluster-using-kubectl"></a>A fürt elérése a kubectl használatával.
```powershell
PS C:\> Get-AksHciCredential -name myCluster
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-név
A fürt neve.

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

### <a name="-outputlocation"></a>-outputLocation
Az a hely, ahová le szeretné tölteni a kubeconfig. Az alapértelmezett szint a `%USERPROFILE%\.kube`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %USERPROFILE%\.kube
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adauth"></a>-adAuth
Használja ezt a jelzőt a kubeconfig Active Directory SSO-verziójának lekéréséhez.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
