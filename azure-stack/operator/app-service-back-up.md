---
title: App Service biztonsági mentése Azure Stack központban
description: Megtudhatja, hogyan készíthet biztonsági másolatot a App Servicesról Azure Stack hub-on.
author: bryanla
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c559a90234b486f8501eeb7b0b8249e41dcdb8fa
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701479"
---
# <a name="back-up-app-service-on-azure-stack-hub"></a>App Service biztonsági mentése Azure Stack központban

Ez a dokumentum útmutatást nyújt a App Service Azure Stack hub-beli biztonsági mentéséről.

> [!IMPORTANT]
> A Azure Stack hub App Service nem készül biztonsági másolat a [Azure stack hub-infrastruktúra biztonsági mentésének](azure-stack-backup-infrastructure-backup.md)részeként. Azure Stack hub-kezelőként el kell végeznie a lépéseket, hogy a App Service sikeresen helyreállítható legyen, ha szükséges.

A Azure App Service on Azure Stack hub a vész-helyreállítás tervezése során négy fő összetevőből áll:
1. Az erőforrás-szolgáltatói infrastruktúra; kiszolgálói szerepkörök, feldolgozói szintek stb. 
2. A App Service titkot.
3. A App Service SQL Server az adatbázisok üzemeltetését és mérését.
4. A App Service felhasználói munkaterhelési tartalmat a App Service fájlmegosztás tárolja.

## <a name="back-up-app-service-secrets"></a>App Service titkos kódok biztonsági mentése
App Service biztonsági másolatból való helyreállításakor meg kell adnia a kezdeti telepítés által használt App Service kulcsokat. Ezeket az információkat azonnal el kell menteni, amint a App Service sikeresen üzembe lett helyezve, és biztonságos helyen tárolja őket. Az erőforrás-szolgáltatói infrastruktúra konfigurációja újra létrejön a biztonsági másolatból a helyreállítás során App Service helyreállítási PowerShell-parancsmagok használatával.

Az App Service-titkok biztonsági mentését az adminisztrációs portálon végezheti el a következő lépésekkel: 

1. Jelentkezzen be a Azure Stack hub felügyeleti portálján a szolgáltatás-rendszergazdaként.

2. Tallózással keresse meg **App Service** -> **titkokat**. 

3. Válassza a **titkos kulcsok letöltése**lehetőséget.

   ![A titkok letöltése Azure Stack hub felügyeleti portálján](./media/app-service-back-up/download-secrets.png)

4. Ha a titkok készen állnak a letöltésre, kattintson a **Save (Mentés** ) gombra, és tárolja a app Service Secrets (**SystemSecrets. JSON**) fájlt biztonságos helyen. 

   ![Titkok mentése Azure Stack hub felügyeleti portálján](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Ismételje meg ezeket a lépéseket a App Service titkainak minden elforgatásakor.

## <a name="back-up-the-app-service-databases"></a>A App Service adatbázisok biztonsági mentése
App Service visszaállításához szüksége lesz a **Appservice_hosting** és a **Appservice_metering** adatbázis biztonsági másolatára. Javasoljuk, hogy SQL Server-karbantartási terveket vagy Azure Backup Server használjon, hogy az adatbázisok biztonsági mentése és biztonságos mentése rendszeresen megtörténjen. Azonban minden olyan módszer használható, amely biztosítja a rendszeres SQL biztonsági mentések létrehozását.

Ha manuálisan szeretné biztonsági másolatot készíteni ezekről az adatbázisokról a SQL Serverba való bejelentkezés során, használja a következő PowerShell-parancsokat:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Ha SQL AlwaysOn-adatbázisokról kell biztonsági mentést készítenie, kövesse [az alábbi utasításokat](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Miután az összes adatbázis biztonsági mentése sikeres volt, másolja a. bak fájlokat egy biztonságos helyre a App Service Secrets információval együtt.

## <a name="back-up-the-app-service-file-share"></a>A App Service fájlmegosztás biztonsági mentése
A App Service a bérlői alkalmazás adatait a fájlmegosztás tárolja. Ennek a fájlmegosztásnak rendszeresen biztonsági mentést kell készítenie a App Service adatbázisokkal együtt, így a lehető legkevesebb adattal elvész, ha visszaállításra van szükség.

A App Service fájlmegosztás tartalmának biztonsági mentéséhez használja az Azure Backup Server vagy egy másik metódust a fájlmegosztás tartalmának rendszeres másolásához arra a helyre, ahol az összes korábbi helyreállítási információt mentette.

Például a következő lépésekkel használhatja a Robocopyt egy Windows PowerShell-(nem PowerShell ISE-) konzol-munkamenetből:

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Következő lépések
[App Service visszaállítása Azure Stack központban](app-service-recover.md)