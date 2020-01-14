---
title: App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon | Microsoft Docs
description: Megtudhatja, hogyan forgathatja el a Azure App Service által használt titkokat és tanúsítványokat Azure Stack hub-on
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 079be062ee45e012abf3ec1bc869409ec4ee03a5
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914569"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon

Ezek az utasítások csak az Azure Stack hub Azure App Serviceére vonatkoznak.  A Azure App Service Azure Stack hub-titkokon való elforgatása nem része a Azure Stack hub központi titkos elforgatási eljárásának.  A kezelők a rendszeren belüli titkos kódok érvényességét, a legutóbbi frissítés dátumát és a titkok lejárta előtt hátralévő időt tudják figyelni.

> [!Important]
> Az operátorok nem kapják meg a titkos kulcs lejáratára vonatkozó riasztásokat az Azure Stack hub irányítópultján, mivel az Azure Stack hub-beli Azure App Service nincs integrálva az Azure Stack hub riasztási szolgáltatásával.  A kezelőknek rendszeresen meg kell figyelniük a titkot az Azure Stack hub felügyeleti portálján Azure Stack hub felügyeleti felületének Azure App Service használatával.

Ez a dokumentum a következő titkok elforgatásának eljárását tartalmazza:

* A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok;
* Az Azure Stack hub Azure App Service által használt adatbázis-kapcsolódási hitelesítő adatok az üzemeltetési és mérési adatbázisokkal való kommunikációhoz;
* A Azure Stack hub Azure App Service által a végpontok biztonságossá tételéhez használt tanúsítványok
* Rendszerhitelesítő adatok Azure App Service Azure Stack hub-infrastruktúra szerepköreihez.

## <a name="rotate-encryption-keys"></a>Titkosítási kulcsok elforgatása

A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok elforgatásához hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a titkosítási kulcsok szakaszban.

1. Az elforgatási eljárás elindításához kattintson **az OK** gombra.

1. A rendszer elforgatja a titkosítási kulcsokat, és frissíti az összes szerepkör-példányt. A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

## <a name="rotate-connection-strings"></a>A kapcsolatok karakterláncának elforgatása

A App Service üzemeltetési és mérési adatbázisokhoz tartozó adatbázis-kapcsolódási karakterlánc hitelesítő adatainak frissítéséhez hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a kapcsolatok karakterláncok szakaszban

1. Adja meg az **SQL SA felhasználónevét** és **jelszavát** , majd kattintson az **OK** gombra az elforgatási eljárás elindításához. 

1. A hitelesítő adatokat a rendszer a Azure App Service szerepkör példányaiban fogja elforgatni. A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

## <a name="rotate-certificates"></a>Tanúsítványok váltása

A Azure Stack hub Azure App Serviceon belül használt tanúsítványok elforgatásához hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a tanúsítványok szakaszban

1. Adja meg az elforgatni kívánt tanúsítványok **tanúsítványának fájlját** és a hozzá tartozó **jelszót** , majd kattintson **az OK**gombra.

1. A tanúsítványokat a rendszer szükség szerint elforgatja a Azure App Service Azure Stack hub-szerepkör példányain.  A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

## <a name="rotate-system-credentials"></a>Rendszerszintű hitelesítő adatok elforgatása

A Azure App Service Azure Stack hub-on belül használt hitelesítő adatok elforgatásához hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a rendszer hitelesítő adatai szakaszban.

1. Válassza ki a használt rendszer hitelesítő adatainak **hatókörét** .  A kezelők dönthetnek úgy, hogy elforgatják a rendszer hitelesítő adatait az összes szerepkörhöz vagy egyedi szerepkörhöz.

1. Adja meg a **helyi rendszergazda felhasználónevet**és az új **jelszót** , majd erősítse meg a **jelszót** , és kattintson **az OK** gombra.

1. A hitelesítő adatokat a rendszer szükség szerint elforgatja a Azure Stack hub szerepkör-példány megfelelő Azure App Service.  A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.



