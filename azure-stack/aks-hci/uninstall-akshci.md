---
title: Uninstall-AksHci
author: jessicaguan
description: A Uninstall-AksHci PowerShell-parancs eltávolítja az AK-t Azure Stack HCI-ben.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 89cd1bd48518f90dd4ce850e35e42c3106681823
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873974"
---
# <a name="uninstall-akshci"></a>Uninstall-AksHci

## <a name="synopsis"></a>Áttekintés
Eltávolítja az Azure Kubernetes szolgáltatást Azure Stack HCI-on.

## <a name="syntax"></a>Syntax

```powershell
Uninstall-AksHci [-skipConfigCleanup]
```

## <a name="description"></a>Leírás
Eltávolítja az Azure Kubernetes szolgáltatást Azure Stack HCI-on. 

Ha a PowerShell-parancsok olyan fürtön futnak, ahol a Windows felügyeleti központot korábban már telepítették, a PowerShell-modul ellenőrzi a Windows felügyeleti központ konfigurációs fájljának létezését. A Windows felügyeleti központ minden csomóponton elhelyezi a Windows felügyeleti központ konfigurációs fájlját. 

## <a name="example"></a>Példa

### <a name="example"></a>Példa
```powershell
PS C:\> Uninstall-AksHci
```

## <a name="parameters"></a>Paraméterek

### <a name="-skipconfigcleanup"></a>-skipConfigCleanup
Az eltávolítás után kihagyja a konfigurációk eltávolítását. Ha ezt a jelzőt használja, a rendszer megőrzi a régi konfigurációt.

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
