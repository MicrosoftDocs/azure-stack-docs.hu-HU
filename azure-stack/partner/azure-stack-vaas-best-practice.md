---
title: Azure Stack az érvényesítéssel kapcsolatos ajánlott eljárásokat. | Microsoft Docs
description: Ez a cikk a szolgáltatásként történő érvényesítés ajánlott eljárásait ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 930a8ea40fde7a021a893e5289d16fa73398300f
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167267"
---
# <a name="best-practices-for-validation-as-a-service"></a>Ajánlott eljárások szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk az erőforrások szolgáltatásként történő érvényesítésének ajánlott eljárásait ismerteti. Az alaprendszer-erőforrások áttekintéséhez tekintse meg az [Érvényesítés a szolgáltatás alapvető fogalmai](azure-stack-vaas-key-concepts.md)című témakört.

## <a name="solution-management"></a>Megoldás-kezelés

### <a name="naming-convention-for-vaas-solutions"></a>Az Varga-megoldások elnevezési konvenciója

Egységes elnevezési konvenciót használhat az összes, az alapszolgáltatásban regisztrált megoldáshoz. A megoldás neve például a következő hardver tulajdonságaiból állítható össze:

|Terméknév | Egyedi hardver elem 1 | Egyedi hardver elem 2 | Megoldás neve
|---|---|---|---|
Saját megoldás XYZ |  Minden Flash | Saját kapcsoló X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Mikor hozzon létre egy új

Ugyanazt az adatbázis-megoldást használja, ha ugyanazon hardveres SKU-ra futtat munkafolyamatokat. A rendszer csak akkor hoz létre új alapszolgáltatási megoldást, ha a hardveres SKU módosul.

## <a name="workflow-management"></a>Munkafolyamat-kezelés

### <a name="naming-convention-for-vaas-workflows"></a>Az Varga munkafolyamatok elnevezési konvenciója

Egységes elnevezési konvenció használata az összes, az Hozzon létre például egy munkafolyamat nevét az alábbi Build-tulajdonságok közül a következők szerint:

|Build száma (Major) | Dátum | Megoldás mérete | Munkafolyamat neve
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)
