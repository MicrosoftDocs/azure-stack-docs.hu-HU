---
title: SQL-adatbázisok használata a Azure Stackon | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisokat Azure Stack szolgáltatásként, illetve az SQL Server erőforrás-szolgáltatói adapter üzembe helyezésének gyors lépéseit.
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
ms.openlocfilehash: 210d8e074cd8c0d62567b33b70cd75984f72d149
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829283"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázisok használata Microsoft Azure Stack

Az SQL Server erőforrás-szolgáltató használatával [Azure stack](azure-stack-overview.md)szolgáltatásként kínál SQL-adatbázisokat. Miután telepítette az erőforrás-szolgáltatót, és csatlakoztatva van egy vagy több SQL Server példányhoz, Ön és a felhasználók a következőket hozhatják létre:

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
> Azure Stack integrált rendszerekre telepített kiszolgálókat a bérlői előfizetésből kell létrehozni. Nem hozhatók létre az alapértelmezett szolgáltatói előfizetésből. Ezeket létre kell hozni a bérlői portálról vagy a PowerShell használatával a megfelelő bejelentkezéssel. Minden üzemeltetési kiszolgáló számlázható virtuális gép, és licencekkel kell rendelkeznie. A szolgáltatás rendszergazdája a bérlői előfizetés tulajdonosa lehet.

## <a name="next-steps"></a>További lépések

[Az SQL Server erőforrás-szolgáltató adapter üzembe helyezése](azure-stack-sql-resource-provider-deploy.md)
