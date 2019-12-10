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
ms.openlocfilehash: 4aef28f0351a89f02dc4c00cd042d6bdd33ee957
ms.sourcegitcommit: 6bb20ed3dcbd64231331a8e807ba69eff8b7439b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74946801"
---
# <a name="use-sql-databases-on-azure-stack"></a>SQL-adatbázisok használata Azure Stack

A SQL Server erőforrás-szolgáltató használatával SQL-adatbázisokat biztosíthat szolgáltatásként [Azure stackon](azure-stack-overview.md). Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server-példánnyal, Ön és felhasználói a következőket hozhatják létre:

- Adatbázisok Felhőbeli natív alkalmazásokhoz.
- SQL-t használó webhelyek.
- SQL-t használó munkaterhelések.

Az erőforrás-szolgáltató nem biztosítja a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)összes adatbázis-kezelési képességeit. Például az erőforrások automatikus kiosztására szolgáló rugalmas készletek nem támogatottak. Az erőforrás-szolgáltató azonban támogatja a hasonló létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket egy SQL Server adatbázison.

Azure Stack operátor feladata az adatbázis-kiszolgálói példányok telepítése, konfigurálása és karbantartása a biztonság, a HA, a biztonsági mentés, a javítás és a frissítés számára. Az adatbázis-kiszolgáló példányát különböző felhasználói adatbázisok osztják meg, beleértve az adatbázis-kiszolgáló nevét és a nyilvános IP-címet. Az adatbázis-használat nem jelent jelentést.

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
