---
title: Készítsen biztonsági másolatot az App Service-ben az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack App Service biztonsági mentési részletes útmutatást.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: mabrigg
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: dff128ad86de23aae0bf8fe4848d256510a80d85
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618329"
---
# <a name="back-up-app-service-on-azure-stack"></a>App Service-ben készíteni az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*  

Ez a dokumentum útmutatást az Azure Stack App Service biztonsági tudnivalók.

> [!IMPORTANT]
> Az Azure Stack App Service-ben nem készül részeként [Azure Stack infrastruktúra biztonsági mentését](azure-stack-backup-infrastructure-backup.md). Kezelőként Azure Stack App Service-ben sikeresen állíthatók, ha a szükséges lépések szükségesek.

Az Azure App Service az Azure Stacken rendelkezik négy fő összetevőből katasztrófa utáni helyreállítás tervezésekor érdemes figyelembe venni:
1. Az erőforrás-szolgáltató infrastruktúra; kiszolgálói szerepkörök, feldolgozói rétegek, stb. 
2. Az App Service-ben titkos kulcsok
3. Az App Service az SQL Server üzemeltetése és a mérési adatbázis
4. Az App Service-ben felhasználómennyiség kiszolgálására alkalmasak tartalom az App Service-fájlmegosztásban tárolt   

## <a name="back-up-app-service-secrets"></a>Készítsen biztonsági másolatot az App Service titkos kulcsok
App Service-ben helyreállítása biztonsági másolatból, amikor az első üzembe helyezés által használt App Service-ben kulcsok kell adnia. Ezeket az adatokat, amint az App Service sikeresen üzembe helyezett és tárolja biztonságos helyen kell menteni. Az erőforrás-szolgáltató infrastruktúra konfigurálása az App Service recovery PowerShell-parancsmagok használatával helyreállítás közben létrejönnek, biztonsági másolatból.

A felügyeleti portál segítségével biztonsági másolatot Alkalmazáskulcs szolgáltatás az alábbi lépéseket: 

1. Jelentkezzen be az Azure Stack felügyeleti portálon a szolgáltatás-rendszergazdaként.

2. Keresse meg a **App Service-ben** -> **titkok**. 

3. Válassza ki **titkos kódok letöltése**.

   ![Titkos kódok letöltése](./media/app-service-back-up/download-secrets.png)

4. Amikor a titkos kódok letöltésre készen áll, kattintson **mentése** és az App Service-ben titkos kulcsok tárolására (**SystemSecrets.JSON**) fájlt egy biztonságos helyre. 

   ![Mentse a titkos kulcsok](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Ismételje meg ezeket a lépéseket, minden alkalommal, amikor az App Service-ben titkos kulcsok vannak-e forgatni.

## <a name="back-up-the-app-service-databases"></a>Az App Service-ben adatbázisainak biztonsági mentése
Az App Service-adatbázis visszaállításához a szüksége lesz a **Appservice_hosting** és **Appservice_metering** biztonsági másolatokat. Ezek az adatbázisok biztonsági mentése, és biztonságosan rendszeresen mentett biztosítása érdekében az SQL Server karbantartási tervek vagy az Azure Backup Server használatát javasoljuk. Azonban jönnek létre, hiszen a rendszeres SQL biztonsági mentések bármelyik módszert is használható.

Manuális biztonsági mentéséhez ezeket az adatbázisokat az SQL-kiszolgálóra való bejelentkezés, a következő PowerShell-parancsokat használhatja:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Ha az SQL AlwaysOn adatbázisok biztonsági mentése, kövesse [ezek az utasítások](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Miután minden adatbázis lett sikeresen készített biztonsági másolatot, másolja a .bak-fájlokat egy biztonságos helyre az App Service-ben titkos információkkal együtt.

## <a name="back-up-the-app-service-file-share"></a>Az App Service-fájlmegosztás biztonsági mentése
App Service-ben tárolja bérlői alkalmazás adatai a fájlmegosztásban. Ez kell készíteni az App Service-adatbázisokat és rendszeres időközönként, hogy a lehető, kevés adattal elvész, ha a visszaállítás megadása kötelező. 

A tartalom, rendszeresen másolása a fájlmegosztás használhatja az Azure Backup Server vagy egy másik módszer az App Service-fájlmegosztás biztonsági mentése a tartalom helyét mentése után az összes korábbi helyreállítási információk. 

Például használhatja ezeket a lépéseket egy Windows PowerShell (nem a PowerShell ISE) konzol-munkamenetet a robocopy használatával:

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>További lépések
[Állítsa vissza az Azure Stack App Service szolgáltatásában](app-service-recover.md)