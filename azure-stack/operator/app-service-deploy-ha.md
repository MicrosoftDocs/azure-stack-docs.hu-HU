---
title: Az Azure Stack App Service üzembe helyezése a magas rendelkezésre állású konfigurációban |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az App Service-ben az Azure Stack használatával a magas rendelkezésre állású konfigurációval.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: dc4cb3e7931b456de6e807d9f7b691f9bfb71a33
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269512"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Magas rendelkezésre állású konfigurációban App Service üzembe helyezése

Ez a cikk bemutatja, hogyan használhatja az Azure Stack piactéren elemek üzembe helyezése az Azure Stack App Service egy magas rendelkezésre állású konfigurációban. Mellett rendelkezésre Piactéri elemek, ez a megoldás is használ a [az App Service-fileshare – SQL Server – magas rendelkezésre állás](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack gyorsindítási sablon. Ez a sablon egy magas rendelkezésre állású infrastruktúrát, a üzemeltetési az App Service erőforrás-szolgáltató létrehozása automatizálja. App Service-ben a virtuális gép magas rendelkezésre állású infrastruktúrát telepíti. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>A magas rendelkezésre állású virtuális gépek App Service-ben infrastruktúra üzembe helyezése
A [az App Service-fileshare – SQL Server – magas rendelkezésre állás](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack-gyorssablon egyszerűbbé teszi az App Service üzemelő példánya egy magas rendelkezésre állású konfigurációban. Az alapértelmezett szolgáltatója előfizetésben kell telepíteni. 

Ha egy egyéni erőforrás létrehozása az Azure Stackben, a sablont hoz létre:
- Egy virtuális hálózat és alhálózatok megadása kötelező.
- A hálózati biztonsági csoportokat a fájlkiszolgáló, az SQL Server és Active Directory Domain Services (AD DS) alhálózatokhoz.
- Storage-fiókok a Virtuálisgép-lemezek és a fürt felhőbeli tanúsítót.
- Az SQL virtuális gépek magánhálózati IP-Címmel rendelkező egy belső terheléselosztó az SQL AlwaysOn-figyelő kötve.
- Három rendelkezésre állási csoportok az Active Directory tartományi szolgáltatások, a fájlkiszolgáló fürt és az SQL-fürt.
- Két csomópontos SQL-fürt.
- Két csomópontos fájlkiszolgáló fürt.
- Két tartományvezérlőt.

### <a name="required-azure-stack-marketplace-items"></a>Szükséges Azure Stack piactéren elemek
Ezzel a sablonnal előtt győződjön meg arról, hogy a következő [Azure Stack piactéren elemek](azure-stack-marketplace-azure-items.md) érhetők el az Azure Stack-példány:

- Windows Server 2016 Datacenter Core platform (az AD DS és a file server virtuális gépek)
- Az SQL Server 2016 SP2 a Windows Server 2016 (vállalati)
- Legújabb SQL IaaS-bővítményt. 
- Legújabb PowerShell Desired State Configuration bővítmény 

> [!TIP]
> Felülvizsgálat [a sablon információs fájl](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) további részleteket a sablon követelményeinek és az alapértelmezett értékek a githubon. 

### <a name="deploy-the-app-service-infrastructure"></a>Az App Service-infrastruktúra üzembe helyezése
Ez a szakasz a lépéseket követve hozzon létre egy egyéni üzembe helyezés a **az App Service-fileshare – SQL Server – magas rendelkezésre állás** Azure Stack gyorsindítási sablon.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **egyéni**, majd **sablonalapú telepítés**.

   ![Egyéni sablon telepítése](media/app-service-deploy-ha/1.png)


3. Az a **egyéni üzembe helyezés** panelen válassza ki **szerkesztési sablon** > **gyorsindítási sablon** , majd a legördülő listából válassza ki a rendelkezésre álló egyéni sablonok, Válassza ki a **az App Service-fileshare – SQL Server – magas rendelkezésre állás** sablont, kattintson a **OK**, majd **mentése**.

   ![Válassza ki az App Service-fileshare – SQL Server – magas rendelkezésre állás gyorsindítási sablon](media/app-service-deploy-ha/2.png)

4. Az a **egyéni üzembe helyezés** panelen válassza ki **paraméterek szerkesztése** , és görgessen le a tekintse át a sablon alapértelmezett értékeit. Módosítsa ezeket az értékeket adja meg az összes kötelező paraméter információkat, majd szükség szerint **OK**.<br><br> Legalább a ADMINPASSWORD, FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD és SQLLOGINPASSWORD paraméterek megadására, összetett jelszavakat.
    
   ![Egyéni telepítési paraméterek szerkesztése](media/app-service-deploy-ha/3.png)

5. Az a **egyéni üzembe helyezés** panelen ellenőrizze, **szolgáltatói előfizetés alapértelmezett** használja majd hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot, az egyéni előfizetés van kiválasztva a központi telepítés.<br><br> Ezután válassza ki az erőforráscsoport helye (**helyi** ASDK telepítések) majd **létrehozás**. Az egyéni központi telepítési beállítások érvényesíti a sablon telepítésének megkezdése előtt.

    ![Egyéni központi telepítés létrehozása](media/app-service-deploy-ha/4.png)

6. A felügyeleti portálon, válassza ki a **erőforráscsoportok** pedig az erőforráscsoport nevét, az egyéni üzembe helyezés (**app-service – magas rendelkezésre állás** ebben a példában). Győződjön meg arról, központi telepítések sikeresen befejeződött az üzembe helyezés állapotának megtekintéséhez.

   > [!NOTE]
   > A sablon telepítésének befejezéséhez körülbelül egy óra alatt vesz igénybe.

   [![](media/app-service-deploy-ha/5-sm.png "Tekintse át a sablon központi telepítési állapota")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Rekord sablon kimenete
Után a sablon üzembe helyezés sikeresen befejeződik, a sablon üzembe helyezéséhez adja vissza rekord. Ezek az információk az App Service-telepítő futtatásakor kell. 

Győződjön meg arról, jegyezze fel ezeket az értékeket a kimeneti mindegyike:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Fedezze fel a sablon kimeneti értékeket az alábbi lépéseket követve:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. A felügyeleti portálon, válassza ki a **erőforráscsoportok** pedig az erőforráscsoport nevét, az egyéni üzembe helyezés (**app-service – magas rendelkezésre állás** ebben a példában). 

3. Kattintson a **központi telepítések** válassza **Microsoft.Template**.

    ![Microsoft.Template deployment](media/app-service-deploy-ha/6.png)

4. Kiválasztása után a **Microsoft.Template** központi telepítését, jelölje be **kimenetek** , és jegyezze fel a sablon paraméter kimenete. Ez az információ lesz szükség, ha az App Service üzembe helyezése.

    ![A paraméter kimenete](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Magas rendelkezésre állású konfigurációban App Service üzembe helyezése
Kövesse a lépéseket ebben a szakaszban történő üzembe helyezést az Azure Stack App Service egy magas rendelkezésre állású konfiguráció alapján a [az App Service-fileshare – SQL Server – magas rendelkezésre állás](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack gyorsindítási sablon. 

Miután telepítette az App Service erőforrás-szolgáltató, megadhatja az ajánlatok és csomagok. Felhasználók majd előfizethetnek az szolgáltatásba és az alkalmazások létrehozásának első lépései.

> [!IMPORTANT]
> A resource provider telepítőjét futtatja, előtt ellenőrizze, hogy elolvasta a kibocsátási megjegyzéseket, kísérő minden App Service-ben kiadás új funkciókat, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés megismeréséhez.

### <a name="prerequisites"></a>Előfeltételek
Az App Service-telepítő futtatása előtt több lépésre szükség, leírtak szerint a [mielőtt elkezdené a cikk az Azure Stack App Service-szel](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Nem minden lépést ismertetett a használatának megkezdése előtt a cikk akkor szükség, mert a sablon üzembe helyezéséhez konfigurálja az infrastruktúra virtuális gépein az Ön számára. 

- [Töltse le a App Service-ben telepítő és a segítő szkripteket](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Töltse le a legújabb egyéni szkriptek futtatására szolgáló bővítmény az Azure Stack piactéren](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Szükséges tanúsítványok előállításában](azure-stack-app-service-before-you-get-started.md#get-certificates).
- A kiválasztott Azure stack-azonosítása szolgáltató alapján azonosító-alkalmazás létrehozása. Alkalmazás azonosítója lehet tenni vagy [Azure ad-ben](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) vagy [Active Directory összevonási szolgáltatások](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) , és jegyezze fel az alkalmazásazonosítót.
- Győződjön meg arról, hogy hozzáadta a Windows Server 2016 Datacenter rendszerképet az Azure Stack piactéren. Ez az App Service-ben a telepítéshez szükséges.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Magas rendelkezésre állású konfigurációban az App Service üzembe helyezése
Az App Service erőforrás-szolgáltató telepítése szükséges legalább egy órát. Szükséges idő hossza attól függ, hogy hány szerepkör példányai, üzembe helyezése. A telepítő a telepítés során fut, a következő feladatokat:

- Hozzon létre egy blobtárolót a megadott Azure Stack-tárfiókban.
- DNS-zóna és -bejegyzések létrehozása App Service-hez.
- Az App Service erőforrás-szolgáltató regisztrálásához.
- Az App Service-katalóguselemek regisztrálása.

Az App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa az App Service-ben korábban letöltött telepítőt (**appservice.exe**) rendszergazdaként egy olyan számítógépről, amely hozzáférhet az Azure Stack rendszergazdai Azure erőforrás-kezelési végpontot.

2. Válassza ki **App Service üzembe helyezése és frissítése a legújabb verzióra**.

    ![Telepítése vagy frissítése](media/app-service-deploy-ha/01.png)

3. Fogadja el a Microsoft szoftverlicencelési feltételeit, és kattintson a **tovább**.

    ![Microsoft licencelési feltételeit](media/app-service-deploy-ha/02.png)

4. Fogadja el a nem Microsoft-licencelési feltételeit, és kattintson a **tovább**.

    ![Nem a Microsofttól származó licencelési feltételek](media/app-service-deploy-ha/03.png)

5. Adja meg az App Service-ben az Azure Stack környezettel felhőbeli végpont-konfigurációja.

    ![App Service-ben felhőbeli végpont-konfiguráció](media/app-service-deploy-ha/04.png)

6. **Csatlakozás** az Azure Stack-előfizetésre, és a telepítéshez használható, és válassza ki a helyet. 

    ![Csatlakozás az Azure Stack-előfizetéshez](media/app-service-deploy-ha/05.png)

7. Válassza ki **használja a meglévő virtuális hálózat és alhálózatok** és a **erőforráscsoport-név** a magas rendelkezésre állású sablon üzembe helyezéséhez használt erőforráscsoport.<br><br>Ezután válassza ki a sablon központi telepítésének részeként létrehozta a virtuális hálózatot, és válassza ki a megfelelő szerepkör alhálózatok a legördülő listából válassza ki lehetőségek közül. 

    ![Virtuális hálózat kiválasztása](media/app-service-deploy-ha/06.png)

8. Adja meg a fájlmegosztás elérési útja és a fájlmegosztás tulajdonosa fájlparaméterek adatokat jelenít meg a korábban rögzített sablont. Ha befejezte, kattintson a **tovább**.

    ![Fájlmegosztási kimeneti adatok](media/app-service-deploy-ha/07.png)

9. Telepítse az App Service-ben használt gép nem található, a fájlkiszolgáló, az App Service-fájlmegosztás üzemeltetéséhez használt megegyező virtuális hálózatba, mert nem kell tudnia oldani a nevet. **Ez az elvárt működés**.<br><br>Győződjön meg arról, hogy a fájlmegosztás UNC elérési út és a fiókok adatait megadott információk helyességét, és nyomja le az **Igen** App Service-ben a telepítés folytatásához a figyelmeztető párbeszédpanelen.

    ![Várt hiba-párbeszédpanelen.](media/app-service-deploy-ha/08.png)

    Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
    - Adatforrás: Bármely
    - Forrás porttartomány: *
    - Cél: IP-címek
    - Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
    - Cél porttartomány: 445
    - Protokoll: TCP
    - Művelet: Engedélyezés
    - Prioritás: 700
    - Név: Outbound_Allow_SMB445

10. Adja meg az identitás Alkalmazásazonosítója és elérési útja és a jelszavakat az identitás-tanúsítványokat, és kattintson a **tovább**:
    - Identitás alkalmazástanúsítványának (formátumban **sso.appservice.local.azurestack.external.pfx**)
    - Az Azure Resource Manager-főtanúsítványt (**AzureStackCertificationAuthority.cer**)

    ![ID alkalmazás tanúsítványának és a legfelső szintű tanúsítvány](media/app-service-deploy-ha/008.png)

10. Ezután adja meg a következő tanúsítványok a fennmaradó szükséges adatokat, és kattintson a **tovább**:
    - Alapértelmezett Azure Stack SSL-tanúsítványt (az formátumban **_.appservice.local.azurestack.external.pfx**)
    - API SSL-tanúsítvány (formátumban **api.appservice.local.azurestack.external.pfx**)
    - Közzétevő tanúsítványa (formájában **ftp.appservice.local.azurestack.external.pfx**) 

    ![További konfigurációs tanúsítványok](media/app-service-deploy-ha/09.png)

11. Adja meg az SQL Server-kapcsolódási információt, a magas rendelkezésre állású sablon üzembe helyezési kimenetek az SQL Server kapcsolati információk használatával:

    ![Az SQL Server-kapcsolódási információt](media/app-service-deploy-ha/10.png)

12. Az App Service-adatbázisok üzemeltetéséhez használt SQL server megegyező virtuális hálózatba nem található, telepítse az App Service-ben használt gép, mert nem kell tudnia oldani a nevet.  **Ez az elvárt működés**.<br><br>Győződjön meg arról, hogy az SQL Server nevét és a fiókok adatait megadott információk helyességét, és nyomja le az **Igen** App Service-ben a telepítés folytatásához. Kattintson a **tovább**.

    ![Az SQL Server-kapcsolódási információt](media/app-service-deploy-ha/11.png)

13. Fogadja el az alapértelmezett szerepkör konfigurációs értékeket, vagy módosítsa az ajánlott értékek, és kattintson **tovább**.<br><br>Javasoljuk, hogy az alapértelmezett értékeket, az App Service-infrastruktúra szerepkörpéldányok módon lehet módosítani a magas rendelkezésre állású konfigurációk:

    |Szerepkör|Alapértelmezett|Magas rendelkezésre állású javaslat|
    |-----|-----|-----|
    |Vezérlői szerepkör|2|2|
    |Felügyeleti szerepkör|1.|3|
    |Közzétevői szerepkör|1.|3|
    |Előtér-kiszolgálói szerepkör|1.|3|
    |Megosztott feldolgozói szerepkör|1.|10|
    |     |     |     |

    ![Infrastruktúra-szerepkör példány értékek](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Alapértelmezett értékét azoknak, ajánlott a tutoral nő a hardverkövetelményekkel telepíthető az App Service-ben. A javasolt 21 virtuális gépek helyett az alapértelmezett 18 maggal és 32,256 MB RAM 15 virtuális gépek támogatásához 26 magok összesen és 46,592 MB RAM szükséges.

14. Az App Service infrastruktúráját virtuális gépek telepítéséhez használandó a platformlemezkép kiválasztása, és kattintson a **tovább**:

    ![Platform lemezkép kiválasztása](media/app-service-deploy-ha/13.png)

15. Adja meg az App Service-ben infrastruktúra szerepkör hitelesítő adatokat kell használni, és kattintson az **tovább**:

    ![Infrastruktúra-szerepkör hitelesítő adatok](media/app-service-deploy-ha/14.png)

16. Tekintse át az adatokat az App Service üzembe helyezése, és kattintson a **tovább** való központi telepítésének megkezdése. 

    ![Tekintse át a telepítés összegzése](media/app-service-deploy-ha/15.png)

17. Tekintse át az App Service-ben üzembe helyezés folyamatban. Ez is igénybe vehet, az adott központi telepítési konfigurációt, és a hardver függően egy óránál. A telepítő sikeres befejezése után jelölje ki a **kilépési**.

    ![A telepítés befejeződött.](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>További lépések

[Horizontális felskálázás az App Service](azure-stack-app-service-add-worker-roles.md). Szükség lehet további App Service-ben infrastruktúra szerepkör feldolgozók várt alkalmazás igény szerint a környezetében történő hozzáadásához. Alapértelmezés szerint az Azure Stack App Service támogatja az ingyenes és a megosztott feldolgozói rétegek. Adja hozzá a többi feldolgozói rétegek, további feldolgozói szerepkörök hozzáadása kell.

[Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md). További konfigurációs szükség, hogy igény szerinti üzembe helyezést, az több mint például a GitHub, BitBucket, onedrive vállalati verzió vagy DropBox verziókövetési szolgáltatók.

[Készítsen biztonsági másolatot az App Service-ben](app-service-back-up.md). Miután sikeresen üzembe helyezését és konfigurálását az App Service-ben győződjön meg az összes összetevő szükséges vész-helyreállítási készül biztonsági másolat megakadályozni az adatvesztést, és elkerülheti a felesleges szolgáltatás helyreállítási műveletek során.