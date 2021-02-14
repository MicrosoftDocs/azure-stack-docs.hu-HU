---
title: A hardveres életciklus-gazdagép használatának szükséges ismerete
description: Ismerje meg a hardveres életciklus-gazdagép használatának szükséges ismereteit
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487782"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>A hardveres életciklus-gazdagép használatának szükséges ismerete

A cserélhető eljárások elvégzéséhez ismernie kell a következő fogalmakat és útmutatókat, és képesnek kell lennie az elérésére.

## <a name="hardware-lifecycle-host"></a>Hardver életciklus-állomása

A hardver életciklus-állomása (HLH) egy fizikai felügyeleti kiszolgáló, amely az Azure Stack hub-állvány tetején található. A gazdagép eléréséhez a következő három módszer egyikével csatlakozhat:

* Közvetlen (Crash cart)
* iDRAC (szolgáltatási port)
* iDRAC (IP-hozzáférés)

Ha az adatközponton belül van, akkor közvetlenül kapcsolódhat a HLH a VGA-és USB-portok használatával. Például egy összeomlási kosár csatlakoztatása.

Ha az adatközponton belül van, csatlakoztassa a laptopot a iDRAC 9 szolgáltatási portjához egy Micro USB-kábel használatával. További információ: a iDRAC felület elérése közvetlen USB-kapcsolaton keresztül.

Működjön együtt az ügyféllel, hogy a felügyeleti hálózatról és a felügyeleti munkaállomásról a iDRAC IP-címhez csatlakozzanak a HLH.

> [!NOTE]
> Csak azok a hálózatok kapcsolódhatnak közvetlenül a HLH-iDRAC, amelyek korábban hozzá lettek adva a Switch ACL-hez.

## <a name="credentials"></a>Hitelesítő adatok

Az ügyféllel együttműködve szerezze be a hitelesítő adatokat a következőkhöz:

* HLH
* adminisztrátor
* iDRAC-fiók (nem kötelező)

Egy teljes körű rendszergazdai jogosultságokkal rendelkező Windows-fiók.

Ha nem kapcsolódik közvetlenül a kiszolgálóhoz egy összeomlási kosár használatával, akkor a iDRAC fiók hitelesítő adataira lesz szüksége, hogy hozzáférjen a virtuális KVM-hoz.


