---
title: SQL-adatbázisok használata
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan használhatja a SQL Server erőforrás-szolgáltatót az SQL-adatbázisok szolgáltatásként való használatára Azure Stack hub-ban.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: c0f599d8a63f0a1ea16e5a39303391cb1b0790a0
ms.sourcegitcommit: 8079220917523ab9ddb824e4bba3e9b091f38a9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661588"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>SQL-adatbázisok használata Azure Stack hub-on

Az SQL erőforrás-szolgáltató használatával SQL-adatbázisokat biztosíthat [Azure stack központban](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server-példánnyal, Ön és felhasználói a következőket hozhatják létre:

- SQL-adatbázisok Felhőbeli natív alkalmazásokhoz.
- Webalkalmazásokhoz használható SQL-adatbázisok.

Az SQL-erőforrás-szolgáltató telepítése előtt megfontolandó korlátozások:

- A felhasználók csak egyéni adatbázisokat hozhatnak létre és kezelhetnek. Az adatbázis-kiszolgáló példánya nem érhető el a végfelhasználók számára. Ez korlátozhatja a kompatibilitást olyan helyszíni adatbázis-alkalmazásokkal, amelyek hozzáférést igényelnek a Master, a temp DB vagy az adatbázisok dinamikus kezeléséhez.
- Az Azure Stack hub-operátor felelős az SQL Database-kiszolgálók és-gazdagépek üzembe helyezéséhez, frissítéséhez, biztonságossá tételéhez, konfigurálásához és karbantartásához. Az RP szolgáltatás nem biztosít gazdagép-és adatbázis-kiszolgálói példányok kezelési funkcióit.
- A különböző előfizetésekben lévő különböző felhasználók adatbázisai ugyanazon az adatbázis-kiszolgálói példányon találhatók. Az RP nem biztosít semmilyen mechanizmust az adatbázisok különböző gazdagépeken vagy adatbázis-kiszolgálói példányokon való elkülönítéséhez.
- Az RP nem biztosít jelentéskészítést az adatbázisok bérlői használatára vonatkozóan.
- A globális Azure-ban csak egy SQL üzemeltetési kiszolgáló helyezhető át egy másik előfizetésbe. Azure Stack hub nem támogatja az SQL üzemeltetési kiszolgálók másik előfizetésbe való áthelyezését.

A helyi környezetekben a hagyományos SQL Server számítási feladatokhoz a Azure Stack hub-beli virtuális gép SQL Server használata javasolt.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL Resource Provider-adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőkből áll:

- **Az SQL Resource Provider adapter virtuális gépe (VM)**, amely a szolgáltatói szolgáltatásokat futtató Windows Server rendszerű virtuális gép.
- **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáfér az adatbázis-erőforrásokhoz.
- **SQL Server gazdagépeket futtató kiszolgálók**, amelyek kapacitást biztosítanak az olyan adatbázisok számára, amelyekben üzemeltetési kiszolgálók is vannak.

Létre kell hoznia legalább egy SQL Server példányt, vagy hozzáférést kell biztosítania a külső SQL Server példányokhoz.

> [!NOTE]
> Azure Stack hub integrált rendszerekre telepített kiszolgálókat a bérlői előfizetésből kell létrehozni. Nem hozhatók létre az alapértelmezett szolgáltatói előfizetésből. A felhasználókat a felhasználói portálról vagy a PowerShell használatával kell létrehozni a megfelelő bejelentkezéssel. Minden üzemeltetési kiszolgáló számlázható virtuális gépek, és licencekkel kell rendelkeznie. A szolgáltatás rendszergazdája a bérlői előfizetés tulajdonosa lehet.

## <a name="next-steps"></a>Következő lépések

[Az SQL Server erőforrás-szolgáltató adapter üzembe helyezése](azure-stack-sql-resource-provider-deploy.md)
