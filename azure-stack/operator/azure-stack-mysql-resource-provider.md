---
title: MySQL-adatbázisok használata a Azure Stack hub-beli Pástiként
description: Ismerje meg, hogyan helyezheti üzembe a MySQL erőforrás-szolgáltatót, és hogyan biztosíthat MySQL-adatbázisokat Azure Stack hub szolgáltatásként.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: f7ff0e3b3ea21dc6545a6c69b1a9cdbf8dbed607
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881749"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>MySQL-adatbázisok használata Microsoft Azure Stack központban

A MySQL erőforrás-szolgáltató használatával MySQL-adatbázisokat biztosíthat [Azure stack központban](azure-stack-overview.md). Az erőforrás-szolgáltató üzembe helyezése és egy vagy több MySQL-kiszolgálói példányhoz való kapcsolódása után a következőket hozhatja létre:

* MySQL-adatbázisok Felhőbeli natív alkalmazásokhoz.
* MySQL-adatbázisok webes alkalmazásokhoz.  

A MySQL erőforrás-szolgáltató telepítése előtt több korlátozást is figyelembe kell venni:

- A felhasználók csak egyéni adatbázisokat hozhatnak létre és kezelhetnek. Az adatbázis-kiszolgáló példánya nem érhető el a végfelhasználók számára. Ez korlátozhatja a kompatibilitást olyan helyszíni adatbázis-alkalmazásokkal, amelyek hozzáférést igényelnek a Master, a temp DB vagy az adatbázisok dinamikus kezeléséhez.
- A Azure Stack hub-operátor felelős a MySQL adatbázis-kiszolgálók és-gazdagépek üzembe helyezéséhez, frissítéséhez, biztonságossá tételéhez, konfigurálásához és karbantartásához. Az RP szolgáltatás nem biztosít gazdagép-és adatbázis-kiszolgálói példányok kezelési funkcióit. 
- A különböző előfizetésekben lévő különböző felhasználók adatbázisai ugyanazon az adatbázis-kiszolgálói példányon találhatók. Az RP nem biztosít semmilyen mechanizmust az adatbázisok különböző gazdagépeken vagy adatbázis-kiszolgálói példányokon való elkülönítéséhez.
- Az RP nem biztosít jelentéskészítést az adatbázisok bérlői használatára vonatkozóan.

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőket biztosítja:

* **A MySQL erőforrás-szolgáltatói adapter virtuális gépe (VM)** , amely a szolgáltatói szolgáltatásokat futtató Windows Server rendszerű virtuális gép.
* **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáfér az adatbázis-erőforrásokhoz.
* A **MySQL-kiszolgálót működtető kiszolgálók**, amelyek kapacitást biztosítanak az olyan adatbázisok számára, amelyek neve üzemeltetési kiszolgáló. Saját maga is létrehozhat MySQL-példányokat, vagy hozzáférést biztosíthat a külső MySQL-példányokhoz. Az [Azure stack hub](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) rövid útmutatója egy példa sablonnal rendelkezik, amelyet a következőhöz használhat:

  * Hozzon létre egy MySQL-kiszolgálót.
  * MySQL-kiszolgáló letöltése és üzembe helyezése az Azure Marketplace-en.

> [!NOTE]
> Azure Stack hub integrált rendszerekre telepített kiszolgálókat a bérlői előfizetésből kell létrehozni. Nem hozhatók létre az alapértelmezett szolgáltatói előfizetésből. A felhasználókat a felhasználói portálról vagy egy megfelelő bejelentkezéssel rendelkező PowerShell-munkamenetből kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépek, és licencekkel kell rendelkeznie. A szolgáltatás rendszergazdája a bérlői előfizetés tulajdonosa lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok

A rendszerfióknak a következő jogosultságokkal kell rendelkeznie:

* **Adatbázis:** létrehozás, eldobás
* **Bejelentkezés:** létrehozás, beállítás, eldobás, engedélyezés, visszavonás  

## <a name="next-steps"></a>Következő lépések

[A MySQL erőforrás-szolgáltató üzembe helyezése](azure-stack-mysql-resource-provider-deploy.md)
