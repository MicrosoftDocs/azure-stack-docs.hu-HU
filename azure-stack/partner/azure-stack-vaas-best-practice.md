---
title: Az Azure Stack érvényesítési ajánlott eljárások. | Microsoft Docs
description: Ez a cikk ismerteti a gyakorlati tanácsok szolgáltatásként ellenőrzés céljából.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 30b7a5327a709fb35c3c3360f4bb0246e9a5f75f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297677"
---
# <a name="best-practices-for-validation-as-a-service"></a>Ajánlott eljárások a szolgáltatás érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk ismerteti az ajánlott eljárások az érvényesítési (VaaS) szolgáltatás-erőforrások kezelése. VaaS erőforrások áttekintéséhez lásd: [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Megoldások kezelése

### <a name="naming-convention-for-vaas-solutions"></a>Elnevezési VaaS megoldások

Egy egységes kulcselnevezési konvenció használata VaaS regisztrált összes megoldás. Ha például a megoldás nevét is összetevődni az alábbi tulajdonságait:

|Terméknév | 1 elem egyedi hardver | Egyedi hardver elem 2 | Megoldás neve
|---|---|---|---|
A megoldás XYZ |  Az összes Flash | A kapcsoló X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Mikor hozzon létre egy új VaaS megoldás

Az azonos VaaS megoldást használni, ha ugyanazt a hardvert Termékváltozat munkafolyamatokat futtat. Csak akkor, ha a hardver SKU módosítják egy új VaaS megoldást kell létrehozni.

## <a name="workflow-management"></a>Munkafolyamatok kezelése

### <a name="naming-convention-for-vaas-workflows"></a>A munkafolyamatok VaaS elnevezési konvenciót

Egy egységes kulcselnevezési konvenció használata minden VaaS munkafolyamat-Futtatás. Például hozza létre a következő a munkafolyamat nevét az alábbi build tulajdonságait:

|Buildszám (nagy) | Dátum | Megoldás mérete | A munkafolyamat neve
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>További lépések

- Ismerje meg [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)
