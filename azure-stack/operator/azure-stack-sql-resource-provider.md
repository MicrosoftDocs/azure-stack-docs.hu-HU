---
title: SQL-adatbázisok használata az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok szolgáltatásként az Azure Stack és az első lépéseket az SQL Server erőforrás-szolgáltató adapter üzembe helyezéséhez.
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
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 4913ff049b9c3bbc0869aa6cbc14d677e1946e54
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308413"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Az SQL Database használata a Microsoft Azure Stackhez

Az SQL Server erőforrás-szolgáltató használatával a szolgáltatásként nyújtott SQL Database-adatbázisok [Azure Stack](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltató, és csatlakoztathatja azt egy vagy több SQL Server-példányt, és a felhasználók hozhat létre:

- A natív felhőalkalmazások adatbázisok.
- SQL használó webhelyekhez.
- SQL használó munkaterhelések.

Az erőforrás-szolgáltató nem biztosít a összes adatbázis-kezelési képességek [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Ha például automatikusan lefoglalni az erőforrásokat, rugalmas készletek nem támogatottak. Azonban hasonló erőforrás-szolgáltató támogatja létrehozása, olvasása, frissítése és törlése az SQL Server-adatbázis (CRUD) műveleteket. 

## <a name="sql-resource-provider-adapter-architecture"></a>SQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőkből áll:

- **Az SQL resource provider adapter virtuális gép (VM)** , azaz a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
- **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és azt adatbázis-erőforrás.
- **SQL Servert futtató kiszolgálók**, üzemeltetési kiszolgáló adatbázis-kapacitást biztosítanak, amelyek neve.

Létre kell hoznia legalább egy SQL Server példányát, vagy külső SQL Server-példányok eléréséhez.

> [!NOTE]
> Üzemeltető kiszolgálók telepített Azure Stack integrált rendszerek kell létrehozni egy bérlő előfizetésből. Az alapértelmezett szolgáltatója előfizetésből nem hozhatók létre. Ezek a bérlői portálon vagy a megfelelő bejelentkezési a PowerShell használatával kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépeket, és rendelkeznie kell licencekkel. A szolgáltatás-rendszergazda lehet a bérlő előfizetés tulajdonosa.

## <a name="next-steps"></a>További lépések

[Az SQL Server erőforrás-szolgáltató adapter üzembe helyezése](azure-stack-sql-resource-provider-deploy.md)
