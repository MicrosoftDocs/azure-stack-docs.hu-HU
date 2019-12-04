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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780728"
---
# <a name="use-sql-databases-on-azure-stack"></a>SQL-adatbázisok használata Azure Stack

A SQL Server erőforrás-szolgáltató használatával SQL-adatbázisokat biztosíthat szolgáltatásként [Azure stackon](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server-példánnyal, Ön és felhasználói a következőket hozhatják létre:

- Adatbázisok Felhőbeli natív alkalmazásokhoz.
- SQL-t használó webhelyek.
- SQL-t használó munkaterhelések.

Az erőforrás-szolgáltató nem biztosítja a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)összes adatbázis-kezelési képességeit. Például az erőforrások automatikus kiosztására szolgáló rugalmas készletek nem támogatottak. Az erőforrás-szolgáltató azonban támogatja a hasonló létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket egy SQL Server adatbázison.

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
