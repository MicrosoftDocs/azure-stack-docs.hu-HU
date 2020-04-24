---
title: A Azure Stack érvényesítésének ajánlott eljárásai
description: Ismerje meg a szolgáltatásként történő érvényesítés ajánlott eljárásait.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfb2e4d71f821c23dd076842a0b4f3963a5599cc
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661262"
---
# <a name="azure-stack-validation-best-practices"></a>A Azure Stack érvényesítésének ajánlott eljárásai

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk az erőforrások szolgáltatásként történő érvényesítésének ajánlott eljárásait ismerteti. Az alaprendszer-erőforrások áttekintéséhez tekintse meg az [Érvényesítés a szolgáltatás alapvető fogalmai](azure-stack-vaas-key-concepts.md)című témakört.

## <a name="solution-management"></a>Megoldás-kezelés

### <a name="naming-convention-for-vaas-solutions"></a>Az Varga-megoldások elnevezési konvenciója

Egységes elnevezési konvenciót használhat az összes, az alapszolgáltatásban regisztrált megoldáshoz. A megoldás neve például a következő hardver tulajdonságaiból állítható össze:

|Terméknév | Egyedi hardver elem 1 | Egyedi hardver elem 2 | Megoldás neve
|---|---|---|---|
Saját megoldás XYZ |  Minden Flash | Saját kapcsoló X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Mikor hozzon létre egy új

Ugyanazt az adatbázis-megoldást használja, ha ugyanazon hardveres SKU-ra futtat munkafolyamatokat. Csak akkor kell létrehozni egy új adatbázis-megoldást, ha módosul a hardveres SKU.

## <a name="workflow-management"></a>Munkafolyamat-kezelés

### <a name="naming-convention-for-vaas-workflows"></a>Az Varga munkafolyamatok elnevezési konvenciója

Egységes elnevezési konvenció használata az összes, az Hozzon létre például egy munkafolyamat nevét az alábbi Build-tulajdonságok közül a következők szerint:

|Build száma (Major) | Dátum | Megoldás mérete | Munkafolyamat neve
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>További lépések

- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)
