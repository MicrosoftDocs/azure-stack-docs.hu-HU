---
title: Restart-AksHci
author: jessicaguan
description: A Restart-AksHci PowerShell-parancs újraindítja az AK-t Azure Stack HCI-ben, és eltávolítja az összes telepített Kubernetes-fürtöt.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 358ca36b085b21c6ecdbea7a024d9e72e0ca0597
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873956"
---
# <a name="restart-akshci"></a>Restart-AksHci

## <a name="synopsis"></a>Áttekintés
Indítsa újra az Azure Kubernetes szolgáltatást a Azure Stack HCI-ben, és távolítsa el az összes telepített Kubernetes-fürtöt.

## <a name="syntax"></a>Syntax

### <a name="restart-aks-hci"></a>AK újraindítása – HCI
```powershell
Restart-AksHci
```

## <a name="description"></a>Leírás
Az Azure Kubernetes szolgáltatás újraindítása Azure Stack HCI-ben a rendszer eltávolítja az összes Kubernetes-fürtöt, ha van ilyen, és az Azure Kubernetes Service Host. Emellett eltávolítja az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökön és-szolgáltatásokon a csomópontokból. Ezután visszakerül az eredeti telepítési folyamat lépéseire, amíg a gazdagép újra nem jön létre. Az Azure Kubernetes szolgáltatás a Set-AksHciConfig és a letöltött VHDX-lemezképek által konfigurált, Azure Stack HCI-konfiguráción is megmarad.

## <a name="examples"></a>Példák

### <a name="example"></a>Példa
```powershell
PS C:\> Restart-AksHci
```
