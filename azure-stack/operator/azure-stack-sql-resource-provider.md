---
title: SQL-adatbázisok használata az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok szolgáltatásként az Azure Stack és az első lépéseket az SQL Server erőforrás-szolgáltató adapter üzembe helyezéséhez.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 2a558bc6276384cd78c1d20bfbaf09ebd9860fa1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293123"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Az SQL Database használata a Microsoft Azure Stackhez

Az SQL Server erőforrás-szolgáltató használatával a szolgáltatásként nyújtott SQL Database-adatbázisok [Azure Stack](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltató, és csatlakoztathatja azt egy vagy több SQL Server-példányt, és a felhasználók hozhat létre:

- A natív felhőalkalmazások adatbázisok.
- SQL használó webhelyekhez.
- SQL használó munkaterhelések.

Az erőforrás-szolgáltató nem biztosít a összes adatbázis-kezelési képességek [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Ha például automatikusan lefoglalni az erőforrásokat, rugalmas készletek nem támogatottak. Azonban hasonló erőforrás-szolgáltató támogatja létrehozása, olvasása, frissítése és törlése az SQL Server-adatbázis (CRUD) műveleteket. 

## <a name="sql-resource-provider-adapter-architecture"></a>SQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőkből áll:

- **Az SQL resource provider adapter virtuális gép (VM)**, azaz a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
- **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és azt adatbázis-erőforrás.
- **SQL Servert futtató kiszolgálók**, üzemeltetési kiszolgáló adatbázis-kapacitást biztosítanak, amelyek neve.

Létre kell hoznia legalább egy SQL Server példányát, vagy külső SQL Server-példányok eléréséhez.

> [!NOTE]
> Üzemeltető kiszolgálók telepített Azure Stack integrált rendszerek kell létrehozni egy bérlő előfizetésből. Az alapértelmezett szolgáltatója előfizetésből nem hozhatók létre. Ezek a bérlői portálon vagy a megfelelő bejelentkezési a PowerShell használatával kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépeket, és rendelkeznie kell licencekkel. A szolgáltatás-rendszergazda lehet a bérlő előfizetés tulajdonosa.

## <a name="next-steps"></a>További lépések

[Az SQL Server erőforrás-szolgáltató adapter üzembe helyezése](azure-stack-sql-resource-provider-deploy.md)
