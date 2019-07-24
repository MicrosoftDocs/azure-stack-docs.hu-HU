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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6b4e74cce10522fc241c7662ed381793bd264093
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418578"
---
# <a name="best-practices-for-validation-as-a-service"></a>Ajánlott eljárások szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk az erőforrások szolgáltatásként történő érvényesítésének ajánlott eljárásait ismerteti. Az alaprendszer-erőforrások áttekintéséhez tekintse meg az [Érvényesítés a szolgáltatás alapvető fogalmai](azure-stack-vaas-key-concepts.md)című témakört.

## <a name="solution-management"></a>Megoldások kezelése

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

|Build száma (Major) | Date | Megoldás mérete | Munkafolyamat neve
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>További lépések

- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)
