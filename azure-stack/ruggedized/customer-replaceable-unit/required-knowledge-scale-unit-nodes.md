---
title: Szükséges ismeretek a méretezési egység csomópontjainak a Azure Stack hub-ban robusztus működéséhez
description: Ismerje meg, hogy milyen ismeretekkel kell rendelkeznie a méretezési egység csomópontjainak Azure Stack hub-ban való használatáról
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b96bf9ecd1f46495da2aa4c2d49d66cc0aff1b53
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487799"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-azure-stack-hub-ruggedized"></a>Szükséges ismeretek a méretezési egység csomópontjainak a Azure Stack hub-ban robusztus működéséhez

A cserélhető eljárások elvégzéséhez ismernie kell, és el kell tudnia érni a következő fogalmakat, útmutatókat és webhelyeket.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Emelt szintű hozzáférési munkaállomás és a privilegizált végpont

Az emelt szintű hozzáférési munkaállomás (PAW) egy dedikált munkaállomás, amely az Internet és a külső veszélyforrások vektorai által védett. A szolgáltatás a hardveres életciklus-gazdagépen (HLH) vagy kívülről is elhelyezhető az ügyfél hálózatán, és az Azure Stack hub skálázási egység csomópontjaira irányítható.

A PAW eléréséhez Távoli asztal használatával kell bejelentkeznie. Szerezze be a hitelesítő adatokat és az IP-címet az ügyféltől.

Erről a gépről a Kiemelt végpontot (PEP) is elérheti.

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack hub felügyeleti portál

Szerezze be a rendszergazdai portál hitelesítő adatait és URL-címét az ügyféltől.
További információ: Azure stack hub [felügyeleti portáljának használata](../../operator/azure-stack-manage-portals.md) 
 [](../../operator/azure-stack-manage-portals.md).


