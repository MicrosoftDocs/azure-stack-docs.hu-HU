---
title: Kiválóan elérhető MySQL-adatbázisok a Azure Stack
description: Megtudhatja, hogyan hozhat létre egy MySQL-kiszolgáló erőforrás-szolgáltatót futtató számítógépet és a nagyszámú MySQL-adatbázist Azure Stack használatával.
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
ms.openlocfilehash: a03fbf9170e6cc1840bea62efeb33b960a25f99c
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283406"
---
# <a name="offer-highly-available-mysql-databases"></a>Magas rendelkezésre állású MySQL-adatbázisok ajánlat

Azure Stack kezelőként beállíthatja a kiszolgálói virtuális gépeket a MySQL-kiszolgáló adatbázisainak üzemeltetésére. Miután a MySQL-fürtöt sikeresen létrehozta, és Azure Stack felügyelte, a MySQL-szolgáltatásra előfizetett felhasználók könnyen létrehozhatnak olyan, magasan elérhető MySQL-adatbázisokat.

Ez a cikk bemutatja, hogyan hozhat létre egy [MySQL-t a replikációs fürttel](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)a Azure stack Marketplace-elemek használatával. Ez a megoldás több virtuális gépet használ az adatbázisok replikálásához a főcsomópontról egy konfigurálható számú replikára. A létrehozás után a fürt felvehető Azure Stack MySQL üzemeltetési kiszolgálóként, majd a felhasználók létrehozhatnak egy magasan elérhető MySQL-adatbázist.

> [!IMPORTANT]
> Előfordulhat, hogy a **MySQL és a replikálás** Azure stack Marketplace-eleme nem érhető el az összes Azure Cloud előfizetési környezethez. Győződjön meg arról, hogy a Marketplace-elemek elérhetők-e az előfizetésben, mielőtt a jelen oktatóanyag hátralévő részében próbálkozik.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * MySQL-kiszolgáló fürt létrehozása a piactér elemeiből
> * Azure Stack MySQL üzemeltetési kiszolgáló létrehozása
> * Magasan elérhető MySQL-adatbázis létrehozása

A rendszer az elérhető Azure Stack Marketplace-elemek használatával létrehoz és konfigurál egy három virtuális gép MySQL-kiszolgáló fürtöt. 

A Kezdés előtt győződjön meg arról, hogy a [MySQL-kiszolgáló erőforrás-szolgáltatója](azure-stack-mysql-resource-provider-deploy.md) sikeresen telepítve van, valamint hogy a Azure stack piactéren a következő elemek érhetők el:

> [!IMPORTANT]
> A MySQL-fürt létrehozásához az alábbiak mindegyike szükséges.

- [MySQL replikációval](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Ez a Bitnami-megoldás sablonja, amelyet a rendszer a MySQL-fürt üzembe helyezéséhez fog használni.
- [Debian 8 "Megbéklyóz"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). A Debian 8 "Jessie" a credativ által biztosított Microsoft Azure backports kernelrel. A Debian GNU/Linux az egyik legnépszerűbb Linux-disztribúció.
- [Egyéni parancsfájl a linux 2,0-hez](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Az egyéni szkriptek bővítménye egy eszköz, amely a virtuális gép testreszabási feladatait a virtuális gép létrehozása után hajtja végre Ha ezt a bővítményt hozzáadja egy virtuális géphez, az letölthet parancsfájlokat az Azure Storage-ból, és futtathatja őket a virtuális gépen. Az egyéni szkriptek bővítményének feladatai a Azure PowerShell-parancsmagok és az Azure platformfüggetlen parancssori felület (xPlat CLI) használatával is automatizálható.
- VM-hozzáférés Linux-bővítmény 1.4.7. A virtuálisgép-hozzáférési bővítmény lehetővé teszi a jelszó, SSH-kulcs vagy SSH-konfiguráció alaphelyzetbe állítását, így visszanyerheti a hozzáférést a virtuális géphez. Új felhasználót is hozzáadhat jelszóval vagy SSH-kulccsal, vagy törölhet egy felhasználót a bővítmény használatával. Ez a bővítmény a linuxos virtuális gépeket célozza meg.

Ha többet szeretne megtudni az elemek Azure Stack piactérhez való hozzáadásáról, tekintse meg a [Azure stack Marketplace áttekintése](azure-stack-marketplace.md)című témakört.

Szüksége lesz egy SSH-ügyfélre is, például a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) -ra, hogy bejelentkezzen a Linux rendszerű virtuális gépekre a telepítés után.

## <a name="create-a-mysql-server-cluster"></a>MySQL-kiszolgáló fürt létrehozása 
Az ebben a szakaszban ismertetett lépések segítségével telepítse a MySQL-kiszolgáló fürtöt a [MySQL és a replikációs](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) piactér elemmel. Ez a sablon három MySQL-kiszolgálói példányt telepít, amelyek egy magasan elérhető MySQL-fürtben vannak konfigurálva. Alapértelmezés szerint a következő erőforrásokat hozza létre:

- Egy virtuális hálózat
- Egy hálózati biztonsági csoport
- egy Storage-fiók
- Rendelkezésre állási csoport
- Három hálózati adapter (egyet az egyes alapértelmezett virtuális gépek esetében)
- Nyilvános IP-cím (az elsődleges MySQL-fürt virtuális géphez)
- Három linuxos virtuális gép a MySQL-fürt üzemeltetéséhez

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **számítás**, majd **a MySQL replikációval**lehetőséget.

   ![Egyéni sablon központi telepítése](media/azure-stack-tutorial-mysqlrp/1.png)

3. Adja meg az alapvető telepítési információkat az **alapok** lapon. Tekintse át az alapértelmezett értékeket, és szükség szerint módosítsa, majd kattintson **az OK gombra**.<br><br>Legalább a következőket kell megadnia:
   - Központi telepítés neve (alapértelmezés szerint mymysql)
   - Alkalmazás gyökerének jelszava. 12 karakterből álló alfanumerikus jelszó megadása **Speciális karakterek nélkül**
   - Alkalmazás-adatbázis neve (alapértelmezés szerint bitnami)
   - A létrehozandó MySQL adatbázis-replika virtuális gépek száma (alapértelmezés szerint 2)
   - Válassza ki a használni kívánt előfizetést
   - Válassza ki a használni kívánt erőforráscsoportot, vagy hozzon létre egy újat
   - Válassza ki a helyet (az alapértelmezett érték a ASDK helyi)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Központi telepítés alapjai")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. A **környezeti konfiguráció** lapon adja meg a következő információkat, majd kattintson az **OK**gombra: 
   - A Secure Shell-(SSH-) hitelesítéshez használandó jelszó vagy nyilvános SSH-kulcs. Ha jelszót használ, tartalmaznia kell betűket, számokat **, és tartalmazhat** speciális karaktereket.
   - Virtuális gép mérete (alapértelmezés szerint a standard D1 v2 virtuális gépek)
   - Adatlemez mérete GB-ban, kattintson **az OK** gombra

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Környezeti konfiguráció")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Tekintse át a központi telepítés **összegzését**. Igény szerint letöltheti a testreszabott sablont és paramétereket, majd **az OK**gombra kattintva.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Összegzése")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. A telepítés elindításához kattintson a **Létrehozás** gombra a **vásárlás** lapon.

   ![Vásárlás](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > Az üzembe helyezés körülbelül egy órát vesz igénybe. Győződjön meg arról, hogy az üzembe helyezés befejeződött, és a MySQL-fürt a folytatás előtt teljesen konfigurálva van. 

7. Miután az összes központi telepítés sikeresen befejeződött, tekintse át az erőforráscsoport elemeit, és válassza ki a **mysqlip** nyilvános IP-cím elemet. Jegyezze fel a fürt nyilvános IP-címét és teljes TARTOMÁNYNEVÉt.<br><br>Ezt egy Azure Stack operátornak kell megadnia, hogy létre tudja hozni a MySQL-fürtöt hasznosító MySQL-üzemeltetési kiszolgálót.


### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása
Alapértelmezés szerint nincs nyilvános hozzáférés konfigurálva a MySQL-hez a gazdagép virtuális gépe számára. Ahhoz, hogy a Azure Stack MySQL erőforrás-szolgáltató csatlakozhasson és kezelhesse a MySQL-fürtöt, létre kell hozni egy bejövő hálózati biztonsági csoport (NSG) szabályt.

1. A felügyeleti portálon navigáljon a MySQL-fürt üzembe helyezése során létrehozott erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot (**alapértelmezett-alhálózat-SG**):

   ![nyitás](media/azure-stack-tutorial-mysqlrp/6.png)

2. Válassza a **bejövő biztonsági szabályok** lehetőséget, majd kattintson a **Hozzáadás**gombra.<br><br>Adja meg a **3306** értéket a **célport tartományban** , és szükség esetén adjon meg egy leírást a **név** és a **Leírás** mezőkben. Kattintson a Hozzáadás gombra a bejövő biztonsági szabály párbeszédpanel bezárásához.

   ![nyitás](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Külső hozzáférés konfigurálása a MySQL-fürthöz
Ahhoz, hogy a MySQL-fürtöt Azure Stack MySQL-kiszolgáló gazdagépként lehessen hozzáadni, engedélyezni kell a külső hozzáférést.

1. Egy SSH-ügyfél használata esetén ez a példa [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)-t használ, jelentkezzen be az elsődleges MySQL-gépre egy olyan számítógépről, amely hozzáférhet a nyilvános IP-címhez. Az elsődleges MySQL virtuális gép neve általában **0** és egy nyilvános IP-címmel van társítva.<br><br>Használja a nyilvános IP-címet, és jelentkezzen be a virtuális gépre a **bitnami** felhasználónevével és a korábban a speciális karakterek nélkül létrehozott alkalmazás jelszavával.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. Az SSH-ügyfél ablakban a következő parancs használatával győződjön meg arról, hogy a bitnami szolgáltatás aktív és fut. Ha a rendszer kéri, adja meg újra a bitnami jelszavát:

   `sudo service bitnami status`

   ![Szolgáltatás keresése](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Hozzon létre egy távelérési felhasználói fiókot, amelyet a Azure Stack MySQL üzemeltetési kiszolgáló használ a MySQL-hez való kapcsolódáshoz, majd zárja be az SSH-ügyfelet.<br><br>A következő parancsok futtatásával jelentkezzen be a MySQL-be root-ként, használja a korábban létrehozott legfelső szintű jelszót, és hozzon létre egy új rendszergazda felhasználót, cserélje le *\<username\>* és *\<Password\>* szükség szerint a környezetéhez. Ebben a példában a létrehozandó felhasználó neve **sqlsa** , és a rendszer erős jelszót használ:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Rendszergazda felhasználó létrehozása](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Jegyezze fel az új MySQL-felhasználói adatokat.<br><br>Meg kell adnia ezt a felhasználónevet és jelszót, valamint a fürt nyilvános IP-címét vagy teljes FQDN-jét egy Azure Stack operátorhoz, hogy egy MySQL-alapú üzemeltetési kiszolgálót hozzon létre a MySQL-fürt használatával.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Azure Stack MySQL üzemeltetési kiszolgáló létrehozása
Miután létrehozta a MySQL-kiszolgáló fürtöt, és megfelelően konfigurálta, egy Azure Stack operátornak létre kell hoznia egy Azure Stack MySQL üzemeltetési kiszolgálót, hogy a további kapacitás elérhető legyen a felhasználók számára adatbázisok létrehozásához. 

Ne felejtse el használni a nyilvános IP-címet vagy a teljes FQDN-t a MySQL-fürthöz tartozó elsődleges virtuális gép nyilvános IP-címéhez, amelyet korábban a MySQL-fürt**mysqlip**létrehozásakor rögzítettek. Emellett az operátornak ismernie kell a MySQL-kiszolgáló által létrehozott hitelesítő adatokat, amelyeket távolról is elérhet a MySQL-fürt adatbázisához.

> [!NOTE]
> Ezt a lépést Azure Stack operátornak kell futtatnia a Azure Stack felügyeleti portálról.

A MySQL-fürt nyilvános IP-címének és MySQL-hitelesítésének bejelentkezési információinak használatával egy Azure Stack operátor mostantól [létrehozhat egy MySQL üzemeltetési kiszolgálót az új MySQL-fürt használatával](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Győződjön meg arról is, hogy létrehozta a csomagokat és ajánlatokat, hogy a MySQL-adatbázis létrehozása elérhető legyen a felhasználók számára. Az operátornak hozzá kell adnia a **Microsoft. MySqlAdapter** szolgáltatást egy csomaghoz, és egy új kvótát kell létrehoznia kifejezetten a nagy rendelkezésre állású adatbázisokhoz. A csomagok létrehozásával kapcsolatos további információkért lásd: [szolgáltatás, csomag, ajánlat, előfizetés áttekintése](service-plan-offer-subscription-overview.md).

> [!TIP]
> A **Microsoft. MySqlAdapter** szolgáltatás nem lesz elérhető a tervekhez való hozzáadáshoz, amíg a [MySQL-kiszolgáló erőforrás-szolgáltatója nincs telepítve](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Magasan elérhető MySQL-adatbázis létrehozása
Miután a MySQL-fürtöt létrehozta, konfigurálta és hozzáadta Azure Stack MySQL-üzemeltetési kiszolgálóként egy Azure Stack operátor, egy előfizetéssel, beleértve a MySQL-kiszolgáló adatbázis-funkcióit, a következő módon hozhat létre nagyszámú MySQL-adatbázist: a szakasz lépéseit követve. 

> [!NOTE]
> Futtassa ezeket a lépéseket a Azure Stack felhasználói portálon bérlői felhasználóként a MySQL-kiszolgáló funkcióit (Microsoft. MySQLAdapter szolgáltatás) biztosító előfizetéssel.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza **a\+erőforrás létrehozása** > **az adat\+ Storage**, majd a **MySQL-adatbázis**elemet.<br><br>Adja meg a szükséges adatbázis-tulajdonságok adatait, beleértve a nevet, a rendezést, a használandó előfizetést és a telepítéshez használandó helyet. 

   ![MySQL-adatbázis létrehozása](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Válassza az **SKU** lehetőséget, majd válassza ki a használni kívánt MySQL üzemeltetési kiszolgáló SKU-t. Ebben a példában a Azure Stack operátor létrehozta a **MySQL-ha SKU-** t a MySQL-fürt adatbázisainak magas rendelkezésre állásának támogatásához.

   ![SKU kiválasztása](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Válassza a **bejelentkezés** > **új bejelentkezés létrehozása** lehetőséget, majd adja meg az új adatbázishoz használandó MySQL-hitelesítési hitelesítő adatokat. Ha elkészült, kattintson az **OK gombra** , majd **hozza létre** az adatbázis-telepítési folyamat megkezdéséhez.

   ![Bejelentkezés hozzáadása](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Ha a MySQL-adatbázis üzembe helyezése sikeresen befejeződött, tekintse át az adatbázis tulajdonságait, és keresse meg az új, magasan elérhető adatbázishoz való csatlakozáshoz használandó kapcsolati karakterláncot. 

   ![A kapcsolatok karakterláncának megtekintése](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>További lépések

[A MySQL típusú erőforrás-szolgáltató frissítése](azure-stack-mysql-resource-provider-update.md)
