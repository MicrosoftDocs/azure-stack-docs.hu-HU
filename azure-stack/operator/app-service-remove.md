---
title: Azure App Service eltávolítása Azure Stack hub-ból
description: Megtudhatja, hogyan távolíthat el Azure App Service a Azure Stack hub-ból
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636471"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>Azure App Service eltávolítása Azure Stack hub-ból

Ez a cikk bemutatja, hogyan távolíthatja el a Azure App Service erőforrás-szolgáltatót és a kapcsolódó összetevőket Azure Stack hub-ból.

## <a name="remove-resource-provider"></a>Erőforrás-szolgáltató eltávolítása

> [!Important]
> Ez a művelet eltávolítja az összes bérlői erőforrást, eltávolítja a szolgáltatást és a kvótákat az összes csomagból, és eltávolítja a Azure App Service erőforrás-szolgáltatót a teljes egészében.  Ha telepítette a App Service a legszélesebb körben elérhető fájlkiszolgáló és a SQL Server gyors üzembe helyezési sablont, akkor ezek az erőforrások is el lesznek távolítva, mivel azok ugyanabban az erőforráscsoporthoz vannak telepítve, mint az Azure Stack hub-beli Azure App Service.

Az Azure Stack hub Azure App Serviceának eltávolításához kövesse az alábbi lépéseket:

1. Törölje azt az erőforráscsoportot, amely a Azure App Service Azure Stack hub-erőforrásokon tárolja, például: AppService. local

## <a name="remove-databases-and-file-share-content"></a>Adatbázisok és fájlmegosztás tartalmának eltávolítása

Ezt a szakaszt csak akkor kell követnie, ha a SQL Server és/vagy a fájlkiszolgáló nem Stamp vagy más erőforráscsoporthoz van telepítve, ellenkező esetben folytassa a következő szakasszal.

### <a name="remove-databases-and-logins"></a>Adatbázisok és bejelentkezések eltávolítása

1. Ha **SQL Server mindig be van kapcsolva**, távolítsa el a **AppService_Hosting** és **AppService_Metering** adatbázisokat a rendelkezésre állási csoportból:

1. A következő SQL-szkript végrehajtásával távolítsa el az adatbázisokat és a bejelentkezéseket

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>Az alkalmazás fájljának tartalmának eltávolítása a fájlkiszolgálón

1. Távolítsa el a fájlmegosztás származó tartalmat.

## <a name="next-steps"></a>További lépések

Az újratelepítéshez térjen vissza a [App Service Azure stack hub-cikkben való üzembe helyezésének előfeltételeihez](azure-stack-app-service-before-you-get-started.md) .
