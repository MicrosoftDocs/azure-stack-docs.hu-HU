---
title: MySQL-adatbázisok használata a Azure Stack hub-beli Pástiként
description: Ismerje meg, hogyan helyezheti üzembe a MySQL erőforrás-szolgáltatót, és hogyan biztosíthat MySQL-adatbázisokat Azure Stack hub szolgáltatásként.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: f221fdd6436201df2f68b87c5b9b365d0add5fee
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491985"
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
