---
title: MySQL-adatbázisok használata PaaS Azure Stack-ként |} A Microsoft Docs
description: Ismerje meg, hogyan lehet a MySQL erőforrás-szolgáltató üzembe helyezése, és adja meg a MySQL-adatbázisok szolgáltatásként az Azure Stacken.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 092d35b70373f95c74c510d8452fd43c0e7cd5c2
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131166"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL-adatbázisok használata a Microsoft Azure Stackhez

MySQL-adatbázisok gyakran használják a webhelyek szolgáltatásban, és támogatási webhelyén számos platformon. Létrehozhat például az App Services erőforrás-szolgáltató (PaaS) bővítménnyel WordPress-webhelyeit.

Az erőforrás-szolgáltató üzembe helyezése, után teszi lehetővé:

* Hozzon létre MySQL-kiszolgálók és adatbázisokat az Azure Resource Manager központi telepítési sablonok használatával.
* Adja meg a MySQL-adatbázisok szolgáltatásként.  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőket tartalmazza:

* **A MySQL erőforrás szolgáltató adapter a virtuális gép (VM)** , azaz a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
* **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és azt adatbázis-erőforrás.
* **MySQL-kiszolgálót üzemeltető kiszolgálók**, amely az adatbázisok, az úgynevezett üzemeltető kiszolgálók-kapacitást biztosítanak. MySQL-példányok létrehozásához, saját maga, vagy külső MySQL-példányok eléréséhez. A [Azure Stack gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) rendelkezik egy példa a sablon, amely segítségével:

  * MySQL-kiszolgáló létrehozása az Ön számára.
  * Töltse le és üzembe helyezése az Azure Marketplace-ről egy MySQL-kiszolgálóhoz.

> [!NOTE]
> Üzemeltető kiszolgálók telepített Azure Stack integrált rendszerek kell létrehozni egy bérlő előfizetésből. Az alapértelmezett szolgáltatója előfizetésből nem hozhatók létre. A bérlői portál vagy egy PowerShell-munkamenetet egy megfelelő bejelentkezési, létre kell hozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépeket, és rendelkeznie kell licenccel. A szolgáltatás-rendszergazda lehet a bérlő előfizetés tulajdonosa.

### <a name="required-privileges"></a>Szükséges jogosultságok

A system fióknak a következő jogosultságokkal kell rendelkeznie:

* **Adatbázis:** létrehozni, eldobni
* **Bejelentkezés:** létrehozása, beállítása, dobja el, adja meg, visszavonása  

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató üzembe helyezése](azure-stack-mysql-resource-provider-deploy.md)
