---
title: SQL-adatbázisok használata
titleSuffix: Azure Stack
description: Megtudhatja, hogyan használhatja a SQL Server erőforrás-szolgáltatót az SQL-adatbázisok szolgáltatásként való használatára Azure Stackon.
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
ms.openlocfilehash: 9e7800c018816155205569397520271fb86c4c12
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492761"
---
# <a name="use-sql-databases-on-azure-stack"></a>SQL-adatbázisok használata Azure Stack

Az SQL erőforrás-szolgáltató használatával szolgáltatásként kínálhatja az SQL-adatbázisokat [Azure stackon](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server-példánnyal, Ön és felhasználói a következőket hozhatják létre:

- Adatbázisok Felhőbeli natív alkalmazásokhoz.
- SQL-t használó webhelyek.
- SQL-t használó munkaterhelések.

Az SQL erőforrás-szolgáltató telepítése előtt több korlátozást is figyelembe kell venni:

- A felhasználók csak egyéni adatbázisokat hozhatnak létre és kezelhetnek. Az adatbázis-kiszolgáló példánya nem érhető el a végfelhasználók számára. Ez korlátozhatja a kompatibilitást olyan helyszíni adatbázis-alkalmazásokkal, amelyek hozzáférést igényelnek a Master, a temp DB vagy az adatbázisok dinamikus kezeléséhez.
- A Azure Stack operátor felelős az SQL Database-kiszolgálók és-gazdagépek üzembe helyezéséhez, frissítéséhez, biztonságossá tételéhez, konfigurálásához és karbantartásához. Az RP szolgáltatás nem biztosít gazdagép-és adatbázis-kiszolgálói példányok kezelési funkcióit. 
- A különböző előfizetésekben lévő különböző felhasználók adatbázisai ugyanazon az adatbázis-kiszolgálói példányon találhatók. Az RP nem biztosít semmilyen mechanizmust az adatbázisok különböző gazdagépeken vagy adatbázis-kiszolgálói példányokon való elkülönítéséhez.
- Az RP nem biztosít jelentéskészítést az adatbázisok bérlői használatára vonatkozóan.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL Resource Provider-adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőkből áll:

- **Az SQL Resource Provider adapter virtuális gépe (VM)** , amely a szolgáltatói szolgáltatásokat futtató Windows Server rendszerű virtuális gép.
- **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáfér az adatbázis-erőforrásokhoz.
- **SQL Server gazdagépeket futtató kiszolgálók**, amelyek kapacitást biztosítanak az olyan adatbázisok számára, amelyekben üzemeltetési kiszolgálók is vannak.

Létre kell hoznia legalább egy SQL Server példányt, vagy hozzáférést kell biztosítania a külső SQL Server példányokhoz.

> [!NOTE]
> Azure Stack integrált rendszerekre telepített kiszolgálókat a bérlői előfizetésből kell létrehozni. Nem hozhatók létre az alapértelmezett szolgáltatói előfizetésből. Ezeket létre kell hozni a bérlői portálról vagy a PowerShell használatával a megfelelő bejelentkezéssel. Minden üzemeltetési kiszolgáló számlázható virtuális gépek, és licencekkel kell rendelkeznie. A szolgáltatás rendszergazdája a bérlői előfizetés tulajdonosa lehet.

## <a name="next-steps"></a>Következő lépések

[Az SQL Server erőforrás-szolgáltató adapter üzembe helyezése](azure-stack-sql-resource-provider-deploy.md)
