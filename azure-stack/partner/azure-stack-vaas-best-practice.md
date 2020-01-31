---
title: Azure Stack az érvényesítéssel kapcsolatos ajánlott eljárásokat.
description: Ez a cikk a szolgáltatásként történő érvényesítés ajánlott eljárásait ismerteti.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cdd5d8a2b1933559c204e479f0215e63b6100b33
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884111"
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
