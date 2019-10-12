---
title: Az SQL-adatbázisok széles körben elérhetők a Azure Stack
description: Megtudhatja, hogyan hozhat létre SQL Server erőforrás-szolgáltatót futtató számítógépet és a jól elérhető SQL AlwaysOn-adatbázisokat a Azure Stack.
services: azure-stack
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: e5866a80367a826dd58aa39109ebbbbd9f2edce6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283354"
---
# <a name="offer-highly-available-sql-databases"></a>Kiválóan elérhető SQL-adatbázisok

Azure Stack kezelőként beállíthatja, hogy a kiszolgálói virtuális gépek SQL Server-adatbázisokat működtessenek. Miután az SQL-üzemeltetési kiszolgáló sikeresen létrejött, és a Azure Stack felügyeli, az SQL-szolgáltatásokra előfizetett felhasználók könnyedén létrehozhatnak SQL-adatbázisokat.

Ez a cikk bemutatja, hogyan használható egy Azure Stack rövid útmutató sablon [SQL Server AlAlwaysOnon rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)létrehozásához, Azure stack SQL-üzemeltetési kiszolgálóként való hozzáadásához, majd egy magasan elérhető SQL-adatbázis létrehozásához.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * SQL Server alAlwaysOnon rendelkezésre állási csoport létrehozása sablonból
> * Azure Stack SQL-üzemeltetési kiszolgáló létrehozása
> * Magasan elérhető SQL-adatbázis létrehozása

A rendszer két virtuálisgép-SQL Server alAlwaysOnon rendelkezésre állási csoportot hoz létre és konfigurál az elérhető Azure Stack Marketplace-elemek használatával. 

A Kezdés előtt győződjön meg arról, hogy a [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md) sikeresen telepítve van, és a következő elemek érhetők el a Azure stack piactéren:

> [!IMPORTANT]
> A használni kívánt Azure Stack gyorsindítási sablonhoz az alábbiak mindegyike szükséges.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer) Marketplace-rendszerkép.
- SQL Server 2016 SP1 vagy SP2 (Enterprise, standard vagy Developer) a Windows Server 2016 Server rendszerképén. Ez a cikk a [SQL Server 2016 SP2 Enterprise szolgáltatást használja a Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) piactéren rendszerképeken.
- [SQL Server IaaS-bővítmény](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 1.2.30 vagy újabb verziója. Az SQL IaaS bővítmény telepíti a szükséges összetevőket, amelyekre szükség van a piactér SQL Server minden Windows-verzióhoz tartozó elemhez. Lehetővé teszi az SQL-specifikus beállítások konfigurálását az SQL-alapú virtuális gépeken. Ha a bővítmény nincs telepítve a helyi piactéren, az SQL kiépítés sikertelen lesz.
- [Egyéni parancsfájl-bővítmény a Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) 1.9.1 vagy újabb verziójához. Az egyéni szkriptek bővítménye egy eszköz, amellyel automatikusan elindítható a telepítés utáni virtuális gépek testreszabási feladatai.
- A [PowerShell kívánt állapot-konfiguráció (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 vagy újabb verziója. A DSC a Windows PowerShell egyik felügyeleti platformja, amely lehetővé teszi a szoftveres szolgáltatások konfigurációs információinak üzembe helyezését és kezelését, valamint azt a környezetet, amelyben ezek a szolgáltatások futnak.

Ha többet szeretne megtudni az elemek Azure Stack piactérhez való hozzáadásáról, tekintse meg a [Azure stack Marketplace áttekintése](azure-stack-marketplace.md)című témakört.

## <a name="create-a-sql-server-alwayson-availability-group"></a>SQL Server alAlwaysOnon rendelkezésre állási csoport létrehozása
Az ebben a szakaszban ismertetett lépések segítségével telepítse a SQL Server alAlwaysOnon rendelkezésre állási csoportot az [SQL-2016-AlwaysOn Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)rövid útmutató sablon használatával. Ez a sablon két SQL Server Enterprise, standard vagy fejlesztői példányt telepít egy always on rendelkezésre állási csoportba. A következő erőforrásokat hozza létre:

- Egy hálózati biztonsági csoport
- Egy virtuális hálózat
- Négy Storage-fiók (az egyik a Active Directory (AD), egy az SQL, egy a fájlmegosztás tanúsító, egy pedig a VM Diagnostics esetében)
- Négy nyilvános IP-cím (egy az AD-hoz, kettő az egyes SQL-alapú virtuális gépekhez, és az egyik az SQL AlwaysOn-figyelőhöz kötött nyilvános Load Balancer)
- Egy külső terheléselosztó az SQL AlwaysOn-figyelőhöz kötött, nyilvános IP-címmel rendelkező SQL virtuális gépekhez
- Egy virtuális gép (Windows Server 2016) tartományvezérlőként konfigurálva egyetlen tartománnyal rendelkező új erdőhöz
- Két virtuális gép (Windows Server 2016), amely SQL Server 2016 SP1 vagy SP2 Enterprise, standard vagy Developer Edition rendszerrel van konfigurálva, és fürtözött. Ezeknek Piactéri lemezképeknek kell lenniük.
- Egy virtuális gép (Windows Server 2016), amely tanúsító fájlmegosztásként van konfigurálva a fürthöz
- Egy rendelkezésre állási csoport, amely az SQL és a fájlmegosztás tanúsító virtuális gépeket tartalmazza  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza a **\+** **erőforrás létrehozása** > **Egyéni**lehetőséget, majd **template Deployment**.

   ![Egyéni sablon központi telepítése](media/azure-stack-tutorial-sqlrp/1.png)


3. Az **Egyéni telepítés** panelen válassza a **Sablon szerkesztése**@no__t – 2 rövid útmutató**sablont** , majd az elérhető egyéni sablonok legördülő listájának használatával válassza ki az **SQL-2016-AlwaysOn** sablont, kattintson az **OK**gombra, majd  **Mentés**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Gyorsindítás sablon kiválasztása")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Az **Egyéni telepítés** panelen válassza a **Paraméterek szerkesztése** lehetőséget, és tekintse át az alapértelmezett értékeket. Módosítsa az értékeket szükség szerint az összes kötelező paraméter információjának megadásához, majd kattintson **az OK**gombra.<br><br> Legalább:

    - Összetett jelszavak megadása a ADMINPASSWORD, a SQLSERVERSERVICEACCOUNTPASSWORD és a SQLAUTHPASSWORD paraméterekhez.
    - Adja meg a névkeresési DNS-utótagot a DNSSUFFIX paraméterhez tartozó összes kisbetűs karakternél (**azurestack. external** , ASDK-telepítés esetén).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Egyéni telepítési paraméterek szerkesztése")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Az **Egyéni telepítés** panelen válassza ki a használni kívánt előfizetést, és hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az egyéni telepítéshez.<br><br> Ezután válassza ki az erőforráscsoport helyét (ASDK-telepítések esetén**helyi** ), majd kattintson a **Létrehozás**gombra. A rendszer érvényesíti az egyéni központi telepítési beállításokat, majd elindítja az üzemelő példányt.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Egyéni központi telepítés létrehozása")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. Az adminisztrációs portálon válassza az **erőforráscsoportok** lehetőséget, majd az egyéni telepítéshez létrehozott erőforráscsoport nevét (ehhez a példához tartozó**Erőforrás-csoport** ). Tekintse meg a központi telepítés állapotát, és győződjön meg arról, hogy az összes központi telepítés sikeresen befejeződött.<br><br>Ezután tekintse át az erőforráscsoport elemeit, és válassza a **SQLPIPsql @ no__t-1resource csoport neve @ no__t-2** nyilvános IP-cím elemet. Jegyezze fel a nyilvános IP-címet és a terheléselosztó nyilvános IP-címének teljes TARTOMÁNYNEVÉt. Ezt az SQL alAlwaysOnon rendelkezésre állási csoportot kihasználó SQL-üzemeltetési kiszolgáló létrehozásához meg kell adnia egy Azure Stack operátornak.

   > [!NOTE]
   > A sablon központi telepítése több órát is igénybe vesz.

   ![Az egyéni telepítés befejeződött](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Automatikus előkészítés engedélyezése
A sablon sikeres üzembe helyezése és az SQL alAlwaysONon rendelkezésre állási csoport konfigurálása után engedélyeznie kell a rendelkezésre állási csoportban a SQL Server minden egyes példányán az [automatikus](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) betöltést. 

Amikor automatikus kivezetéssel hoz létre egy rendelkezésre állási csoportot, a SQL Server automatikusan létrehozza a másodlagos replikákat a csoportban található összes adatbázishoz anélkül, hogy a AlwaysOn-adatbázisok magas rendelkezésre állásának biztosításához egyéb manuális beavatkozásra lenne szükség.

Ezeket az SQL-parancsokat az alAlwaysOnon rendelkezésre állási csoport automatikus beültetésének konfigurálására használhatja. Cserélje le a \<InstanceName @ no__t-1 nevet az elsődleges példány SQL Server nevére, < availability_group_name > pedig szükség szerint a AlwaysOn rendelkezésre állási csoportjának nevére. 

Az elsődleges SQL-példányon:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Elsődleges SQL-példány parancsfájlja](./media/azure-stack-tutorial-sqlrp/sql1.png)

Másodlagos SQL-példányok esetén:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Másodlagos SQL-példány parancsfájlja](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Tárolt adatbázis-hitelesítés konfigurálása
Egy tárolt adatbázis rendelkezésre állási csoportba való felvétele előtt győződjön meg arról, hogy a tárolt adatbázis-hitelesítési kiszolgáló beállítás értéke 1 minden olyan kiszolgálópéldány esetében, amely rendelkezésre állási replikát futtat a rendelkezésre állási csoport számára. További információ: [tárolt adatbázis-hitelesítés](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Ezekkel a parancsokkal állíthatja be a rendelkezésre állási csoportba tartozó egyes SQL Server-példányok tárolt adatbázis-hitelesítési kiszolgáló beállítását:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Tárolt adatbázis-hitelesítés beállítása](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Azure Stack SQL-üzemeltetési kiszolgáló létrehozása
Miután létrehozta a SQL Server alAlwayOnon rendelkezésre állási csoportot, és megfelelően konfigurálta, egy Azure Stack operátornak létre kell hoznia egy Azure Stack SQL üzemeltetési kiszolgálót, hogy a további kapacitás elérhető legyen a felhasználók számára az adatbázisok létrehozásához. 

Ügyeljen arra, hogy a nyilvános IP-címet vagy a teljes FQDN-t használja az SQL-AlwaysOn rendelkezésre állási csoport erőforráscsoport létrehozásakor korábban rögzített SQL Load Balancer nyilvános IP-címéhez (**SQLPIPsql @ no__t-1resource csoportnév @ no__t-2**). Emellett ismernie kell az alAlwaysOnon rendelkezésre állási csoportban található SQL-példányokhoz való hozzáféréshez használt SQL Server hitelesítő adatokat.

> [!NOTE]
> Ezt a lépést Azure Stack operátornak kell futtatnia a Azure Stack felügyeleti portálról.

Az SQL alAlwaysOnon rendelkezésre állási csoport terheléselosztó figyelő nyilvános IP-címének és SQL-hitelesítésének bejelentkezési információinak használatával egy Azure Stack operátor mostantól [létrehozhat egy SQL-üzemeltetési kiszolgálót az SQL AlAlwaysOnon rendelkezésre állási csoport segítségével](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Győződjön meg arról is, hogy létrehozta a csomagokat és ajánlatokat, hogy az SQL AlwaysOn-adatbázis létrehozása elérhető legyen a felhasználók számára. Az operátornak hozzá kell adnia a **Microsoft. SqlAdapter** szolgáltatást egy csomaghoz, és egy új kvótát kell létrehoznia kifejezetten a nagy rendelkezésre állású adatbázisokhoz. A csomagok létrehozásával kapcsolatos további információkért lásd: [szolgáltatás, csomag, ajánlat, előfizetés áttekintése](service-plan-offer-subscription-overview.md).

> [!TIP]
> A **Microsoft. SqlAdapter** szolgáltatás nem lesz elérhető a tervekhez való hozzáadáshoz, amíg a [SQL Server erőforrás-szolgáltató nincs telepítve](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Magasan elérhető SQL-adatbázis létrehozása
Miután az SQL alAlwaysOnon rendelkezésre állási csoportot létrehozta, konfigurálta és Azure Stack SQL-üzemeltetési kiszolgálóként hozzáadta egy Azure Stack operátor, egy előfizetéssel rendelkező bérlői felhasználó, beleértve az SQL Server adatbázis-képességeket is, amelyek támogatják az SQL-adatbázisokat A AlwaysOn funkcióit a jelen szakasz lépéseinek követésével végezheti el. 

> [!NOTE]
> Futtassa ezeket a lépéseket a Azure Stack felhasználói portálon bérlői felhasználóként egy olyan előfizetéssel, amely SQL Server képességeket (Microsoft. SQLAdapter szolgáltatás) biztosít.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza az **\+** **erőforrás létrehozása** > **adat\+ tároló**, majd a **SQL Database**elemet.<br><br>Adja meg a szükséges adatbázis-tulajdonságokat, beleértve a nevet, a rendezést, a maximális méretet, valamint a telepítéshez használandó előfizetést, erőforráscsoportot és helyet. 

   ![SQL-adatbázis létrehozása](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Válassza az **SKU** lehetőséget, majd válassza ki a használni kívánt SQL üzemeltetési kiszolgáló SKU-t. Ebben a példában a Azure Stack operátor létrehozta a **vállalati-ha SKU-** t az SQL alAlwaysOnon rendelkezésre állási csoportok magas rendelkezésre állásának támogatásához.

   ![SKU kiválasztása](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Válassza a **login** > **új bejelentkezés létrehozása** lehetőséget, majd adja meg az új adatbázishoz használni kívánt SQL-hitelesítési hitelesítő adatokat. Ha elkészült, kattintson az **OK gombra** , majd **hozza létre** az adatbázis-telepítési folyamat megkezdéséhez.

   ![Bejelentkezés létrehozása](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Ha az SQL-adatbázis központi telepítése sikeresen befejeződött, tekintse át az adatbázis tulajdonságait, és keresse meg az új, magasan elérhető adatbázishoz való csatlakozáshoz használandó kapcsolati karakterláncot. 

   ![A kapcsolatok karakterláncának megtekintése](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Következő lépések

[Az SQL típusú erőforrás-szolgáltató frissítése](azure-stack-sql-resource-provider-update.md)
