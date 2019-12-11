---
title: MySQL-adatbázisok használata a (z) Azure Stackban lévő Pástiként | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a MySQL erőforrás-szolgáltatót, és hogyan biztosíthat szolgáltatásként MySQL-adatbázisokat Azure Stack.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: b66a72ce872d64f8fde3cb80ced5e6ad33d80b4d
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993774"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL-adatbázisok használata Microsoft Azure Stack

A MySQL-adatbázisokat általában a webhelyekhez használják, és számos webhely-platformot támogatnak. Létrehozhat például WordPress-webhelyeket a App Services erőforrás-szolgáltató (Pásti) bővítmény használatával.

Az erőforrás-szolgáltató üzembe helyezése és egy vagy több MySQL-kiszolgálói példányhoz való kapcsolódása után a következőket teheti:

* MySQL-adatbázisok létrehozása Azure Resource Manager telepítési sablonok használatával.
* Adja meg a MySQL-adatbázisokat szolgáltatásként.  

A MySQL erőforrás-szolgáltató telepítése előtt több korlátozást is figyelembe kell venni:

- A felhasználók csak egyéni adatbázisokat hozhatnak létre és kezelhetnek. Az adatbázis-kiszolgáló példánya nem érhető el a végfelhasználók számára. Ez korlátozhatja a kompatibilitást olyan helyszíni adatbázis-alkalmazásokkal, amelyek hozzáférést igényelnek a Master, a temp DB vagy az adatbázisok dinamikus kezeléséhez.
- A Azure Stack operátor felelős a MySQL adatbázis-kiszolgálók és-gazdagépek üzembe helyezéséhez, frissítéséhez, biztonságossá tételéhez, konfigurálásához és karbantartásához. Az RP szolgáltatás nem biztosít gazdagép-és adatbázis-kiszolgálói példányok kezelési funkcióit. 
- A különböző előfizetésekben lévő különböző felhasználók adatbázisai ugyanazon az adatbázis-kiszolgálói példányon találhatók. Az RP nem biztosít semmilyen mechanizmust az adatbázisok különböző gazdagépeken vagy adatbázis-kiszolgálói példányokon való elkülönítéséhez.
- Az RP nem biztosít jelentéskészítést az adatbázisok bérlői használatára vonatkozóan.

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőket biztosítja:

* **A MySQL erőforrás-szolgáltatói adapter virtuális gépe (VM)** , amely a szolgáltatói szolgáltatásokat futtató Windows Server rendszerű virtuális gép.
* **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáfér az adatbázis-erőforrásokhoz.
* A **MySQL-kiszolgálót működtető kiszolgálók**, amelyek kapacitást biztosítanak az olyan adatbázisok számára, amelyek neve üzemeltetési kiszolgáló. Saját maga is létrehozhat MySQL-példányokat, vagy hozzáférést biztosíthat a külső MySQL-példányokhoz. A [Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) rövid útmutatóban egy példa sablon látható, amely a következőhöz használható:

  * Hozzon létre egy MySQL-kiszolgálót.
  * MySQL-kiszolgáló letöltése és üzembe helyezése az Azure Marketplace-en.

> [!NOTE]
> Azure Stack integrált rendszerekre telepített kiszolgálókat a bérlői előfizetésből kell létrehozni. Nem hozhatók létre az alapértelmezett szolgáltatói előfizetésből. Ezeket a bérlői portálról vagy egy megfelelő bejelentkezéssel rendelkező PowerShell-munkamenetből kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépek, és licencekkel kell rendelkeznie. A szolgáltatás rendszergazdája a bérlői előfizetés tulajdonosa lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok

A rendszerfióknak a következő jogosultságokkal kell rendelkeznie:

* **Adatbázis:** létrehozás, eldobás
* **Bejelentkezés:** létrehozás, beállítás, eldobás, engedélyezés, visszavonás  

## <a name="next-steps"></a>Következő lépések

[A MySQL erőforrás-szolgáltató üzembe helyezése](azure-stack-mysql-resource-provider-deploy.md)
