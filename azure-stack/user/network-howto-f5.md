---
title: Az F5 üzembe helyezése két Azure Stack hub-példány között
description: Ismerje meg, hogyan telepítheti az F5-et két Azure Stack hub-példány között.
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 3512584e4109d86a9ee6a0d71924d560b9ecba92
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90574210"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>Az F5 telepítése két Azure Stack hub-példány között

Ez a cikk végigvezeti egy külső terheléselosztó beállításán a két Azure Stack hub-környezetben. Ezt a konfigurációt használhatja a különböző számítási feladatok kezeléséhez. Ebben a cikkben az F5-et globális terheléselosztási megoldásként telepíti két független Azure Stack hub-példányon. Egy olyan elosztott terhelésű webalkalmazást is üzembe helyezhet, amely egy NGINX-kiszolgálón fut a két példányon keresztül. A magas rendelkezésre állást, az F5 virtuális készülékek feladatátvételi párét fogják futtatni.

A Azure Resource Manager sablonokat az [F5-azurestack-gslb GitHub-](https://github.com/Mikej81/f5-azurestack-gslb) tárházban találja.

## <a name="overview-of-load-balancing-with-f5"></a>A terheléselosztás áttekintése az F5 billentyűvel

Az F5 hardver, a terheléselosztó a Azure Stack hub és az Azure Stack hub-t futtató adatközponton kívül is lehet. Azure Stack hub nem rendelkezik natív képességgel a számítási feladatok terheléselosztásához két különálló Azure Stack hub-telepítés között. A F5's BIG-IP virtuális kiadása (VE) mindkét platformon fut. Ez a beállítás támogatja az Azure és a Azure Stack hub-architektúrák közötti paritást a támogató alkalmazás-szolgáltatások replikálásával. Létrehozhat egy alkalmazást egy környezetben, és áthelyezheti azt egy másikba. A teljes éles használatra kész Azure Stack hub is tükrözhető, beleértve a nagy IP-konfigurációkat, a szabályzatokat és az alkalmazás-szolgáltatásokat is. A módszer szükségtelenné teszi az alkalmazások újrabontását és tesztelését, és lehetővé teszi a beszerzését a kód írásához.

Az alkalmazások és az azokhoz kapcsolódó adatvédelmek gyakran érintik a fejlesztők számára az alkalmazások nyilvános felhőbe való áthelyezését. Erre nincs szükség. Létrehozhat egy alkalmazást Azure Stack hub-környezetében, míg a biztonsági építész konfigurálja a szükséges beállításokat a F5's webalkalmazási tűzfalon (WAF). A teljes verem replikálható Azure Stack központba azzal az ismerettel, hogy az alkalmazást azonos piacvezető WAF fogja védeni. Azonos szabályzatokkal és szabályrendszerek nem lesznek olyan biztonsági rések vagy sebezhetőségek, amelyek más WAF eltérő módon hozhatók létre.

Azure Stack hub külön piactéren az Azure-ban található. Csak bizonyos elemek lesznek hozzáadva. Ebben az esetben, ha új erőforráscsoportot szeretne létrehozni az egyes Azure Stack hubokon, és üzembe helyezi a már elérhető F5 virtuális készüléket. Itt láthatja, hogy a Azure Stack hub-példányok közötti hálózati kapcsolat engedélyezéséhez **nyilvános IP-** cím szükséges. Lényegében mind a szigetek, mind a **nyilvános IP-cím** lehetővé teszi, hogy mindkét helyen beszéljenek.

## <a name="prerequisites-for-big-ip-ve"></a>A BIG-IP VE használatának előfeltételei

-  Töltse le az **F5 Big-IP ve-all (BYOL, 2 rendszerindítási hely) címet** minden Azure stack hub piactéren. Ha nem áll rendelkezésre a portálon, lépjen kapcsolatba a felhőalapú szolgáltatójával.

-  A Azure Resource Manager sablont a következő GitHub-tárházban találja: https://github.com/Mikej81/f5-azurestack-gslb .

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Az F5 BIG-IP VE üzembe helyezése minden példányon

Központi telepítés Azure Stack hub A és A B példánya.

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

2. Válassza **az + erőforrás létrehozása**lehetőséget.

3. Keresés a piactéren a beírásával `F5` .

4. Válassza **az F5 Big-IP ve – mind (BYOL, 2 rendszerindító helyszín)** elemet.

    ![Az "irányítópult > új > Marketplace > minden > F5 BIG-IP VE – ALL (BYOL, 2 rendszerindítási hely)" párbeszédpanel az F5 billentyűt jeleníti meg a keresőmezőbe. Az egyetlen keresési eredmény: "F5 BIG-IP VE – ALL (BYOL, 2 boot locations)".](./media/network-howto-f5/image1.png)

5. A következő lap alján válassza a **Létrehozás**lehetőséget.

    ![Az "F5 BIG-IP VE – ALL (BYOL, 2 boot locations)" párbeszédpanel információt nyújt a BIG-IP VE és az üzembe helyezhető modulok számára a licenctől függően. Létezik egy létrehozás gomb.](./media/network-howto-f5/image2.png)

6. Hozzon létre egy új, **F5-GSLB**nevű erőforráscsoportot.

7. A központi telepítés befejezéséhez példaként használja az alábbi értékeket:

    ![A Microsoft. template párbeszédpanel bemenetek lapja 15 szövegmezőt, például VIRTUALMACHINENAME és ADMINUSERNAME jelenít meg, amelyek egy példa központi telepítés értékeit tartalmazzák.](./media/network-howto-f5/image3.png)

8. Ellenőrizze, hogy a telepítés sikeresen befejeződött-e.

    ![A Microsoft. template párbeszédpanel Áttekintés lapja "az üzembe helyezés befejeződött", és részletesen ismerteti az üzemelő példányt.](./media/network-howto-f5/image4.png)

    > [!NOTE]  
    > Minden BIG-IP-telepítés körülbelül 20 percet vesz igénybe.

## <a name="configure-big-ip-appliances"></a>NAGYMÉRETŰ IP-készülékek konfigurálása

Kövesse az alábbi lépéseket mind a Azure Stack, mind A B hubhoz.

1. Jelentkezzen be az Azure Stack hub felhasználói portálra az A Azure Stack hub-példányon, és tekintse át a BIG-IP sablon üzembe helyezésével létrehozott erőforrásokat.

    ![Az F5-GSLB párbeszédpanel Áttekintés lapján láthatók az üzembe helyezett erőforrások és a kapcsolódó információk.](./media/network-howto-f5/image18.png)

2. A [Big-IP-konfigurációs elemek](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html)esetében kövesse az F5 billentyűt. 

3. A BIG-IP Wide IP-lista konfigurálásával figyelheti az Azure Stack hub-ba és A B-re telepített összes készüléket. Útmutatásért lásd: [Big-IP GTM-konfiguráció](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. A BIG-IP-készülékek feladatátvételének ellenőrzése. Tesztelési rendszeren konfigurálja a DNS-kiszolgálókat a következők használatára:
    - Azure Stack hub-példány A = `f5stack1-ext` nyilvános IP-cím
    - Azure Stack hub-példány B = `f5stack1-ext` nyilvános IP-címe

5. Tallózással keresse `www.contoso.com` meg a böngészőt, és töltse be az NGINX alapértelmezett oldalát.

## <a name="create-a-dns-sync-group"></a>DNS-szinkronizálási csoport létrehozása

1. A legfelső szintű fiók megbízhatóságának kiépítésének engedélyezése. Kövesse az utasításokat a [rendszerkezelési fiók jelszavának módosításakor (11. x-15. x)](https://support.f5.com/csp/article/K13121). Miután beállította a megbízhatóságot (Certificate Exchange), tiltsa le a root fiókot.

1. Jelentkezzen be a BIG-IP-re, és hozzon létre egy DNS-szinkronizálási csoportot. Útmutatásért tekintse meg a [Big-IP DNS-szinkronizálási csoport létrehozása](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html)című témakört.

    > [!NOTE]  
    > A BIP-IP berendezés helyi IP-címét az **F5-GSLB** erőforráscsoporthoz tekintheti meg. A hálózati adapter "f5stack1-ext", és szeretne csatlakozni a nyilvános vagy magánhálózati IP-címhez (a hozzáféréstől függően).

    ![A "DNS >> GSLB: adatközpontok: adatközpont-lista" párbeszédpanel felsorolja az adatközpontokat és az állapotot. A kiválasztott adatközpontok esetében az engedélyezés, a Letiltás és a törlés gomb is használható.](./media/network-howto-f5/image5.png)
          
    ![A "DNS >> GSLB: kiszolgálók: kiszolgálók listája" párbeszédpanel felsorolja a kiszolgálókat és az állapotot. A kijelölt kiszolgálókon való alkalmazáshoz engedélyező, letiltott, törlési és újrakapcsolódási gomb van.](./media/network-howto-f5/image6.png)

1. Válassza ki az új erőforráscsoportot **F5-GSLB** , majd válassza ki a **f5stack1** virtuális gépet a **Beállítások** területen a **hálózatkezelés**elemre.

## <a name="post-install-configurations"></a>Telepítés utáni konfigurációk

A telepítése után konfigurálnia kell az Azure Stack hub-NSG, és le kell zárnia a forrás IP-címeit.

1. A megbízhatóság létrehozása után tiltsa le a 22-es portot.

2. Ha a rendszer online állapotban van, tiltsa le a forrás NSG. A felügyeleti NSG le kell zárni a felügyeleti forrásba, a külső (4353/TCP) NSG a szinkronizáláshoz a másik példányba kell zárni. a 443-et a virtuális kiszolgálókkal rendelkező alkalmazások telepítése után is zárolni kell.

3. GTM_DNS szabály úgy van beállítva, hogy engedélyezze a port 53 (DNS) forgalmát a rendszerben, és a BIG-IP-feloldó megkezdi a munkát. A figyelők létre lettek hozva.

    ![A hálózati adapter párbeszédpanel fStack1-ext lapján láthatók a fStack1-ext felülettel kapcsolatos információk, valamint a NSG, a fStack1-ext-NSG. A bejövő portszabályok vagy a kimenő portszabályok megtekintésére a lapok közül választhat.](./media/network-howto-f5/image7.png)

4. Helyezzen üzembe egy alapszintű webalkalmazási munkaterhelést az Azure Stack hub-környezetben a BIG-IP-címek közötti terheléselosztáshoz. A NGNIX-kiszolgáló használatára például a [Nginx és a Nginx, valamint a Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/)használatával lehet telepíteni.

    > [!NOTE]  
    > Helyezzen üzembe egy NGNIX-példányt az A Azure Stack hub A és A B Azure Stack hub szolgáltatásban.

5. Miután az NGINX üzembe lett helyezve egy Docker-tárolóban egy Ubuntu virtuális gépen az Azure Stack hub-példányokon belül, ellenőrizze, hogy elérhető-e az alapértelmezett weblapon a kiszolgálókon.

    !["Üdvözöljük az Nginx-ben!" az oldal azt jelzi, hogy az Nginx-webkiszolgáló telepítése sikeres volt, és további konfigurálásra van szükség. A támogatási információkra két hivatkozás vezet.](./media/network-howto-f5/image8.png)

6. Jelentkezzen be a BIG-IP berendezés felügyeleti felületére. Ebben a példában az **F5-stack1-ext** nyilvános IP-címet használja.

    ![A BIG-IP konfigurációs segédprogram bejelentkezési képernyőjének felhasználónevet és jelszót kell megadnia.](./media/network-howto-f5/image9.png)

7. A BIG-IP használatával közzéteheti a NGINX-hozzáférést.
    
    -  Ebben a feladatban a BIG-IP-címet egy virtuális kiszolgálóval és egy készlettel konfigurálja, hogy engedélyezze a beérkező internet-hozzáférést a WordPress-alkalmazáshoz. Először azonosítania kell az NGINX-példány magánhálózati IP-címét.

8. Jelentkezzen be az Azure Stack hub felhasználói portálra. 

9. Válassza ki az NGINX hálózati adapterét.

    ![Az "irányítópult > erőforráscsoportok > NGINX > ubuntu2673" párbeszédpanel áttekintő lapja az ubuntu2673 hálózati adapter információit jeleníti meg.](./media/network-howto-f5/image10.png)

10. A BIG-IP konzolon lépjen a **helyi forgalom > készletek > készlet listára** , és válassza a lehetőséget **+** . Konfigurálja a készletet a tábla értékeinek használatával. Hagyja az összes többi mezőt az alapértelmezett értékre.

    ![A bal oldali ablaktábla lehetővé teszi, hogy új készletet hozzon létre. A jobb oldali ablaktáblán a "helyi forgalom >> készletek: készlet >> új készlet" szerepel, és képességeket biztosít az új készletre vonatkozó információk megadásához. Befejeződött a gomb.](./media/network-howto-f5/image11.png)
    
    | Kulcs | Érték |
    | --- | --- |
    | Név | NGINX_Pool |
    | Állapotfigyelő | HTTPS |
    | Csomópont neve | NGINX |
    | Cím | \<your NGINX private IP address> |
    | Szolgáltatási port | 443 |

11. Válassza a **kész**lehetőséget. Ha helyesen van konfigurálva, a készlet állapota zöld.

    ![A jobb oldali ablaktábla a "helyi forgalom >> készletek: készletek listája" címmel rendelkezik, és az újonnan létrehozott készlet az egyetlen bejegyzés a listában.](./media/network-howto-f5/image12.png)

    Most konfigurálnia kell a virtuális kiszolgálót. Ehhez először meg kell keresnie az F5 BIG-IP-cím magánhálózati IP-címét.

12. A BIG-IP konzolon lépjen a **hálózati > saját IP** -címei elemre, és jegyezze fel az IP-címet.

    ![A bal oldali ablaktábla lehetővé teszi a saját IP-címek megjelenítését. A jobb oldali ablaktábla "hálózati >> saját IP-címek" címmel rendelkezik. Két önip-cím van felsorolva, és az első self_2nic ki van emelve.](./media/network-howto-f5/image13.png)

13. Hozzon létre egy virtuális kiszolgálót a **helyi forgalom**  >  **virtuális kiszolgálók**  >  **listájában** , és válassza a lehetőséget **+** . Konfigurálja a készletet a tábla értékeinek használatával. Hagyja az összes többi mezőt az alapértelmezett értékre.

    | Kulcs | Érték |
    | --- | --- |
    |Név | NGINX |
    |Cél címe | \<Self IP address of the BIG-IP> |
    |Szolgáltatási port | 443 |
    |SSL-profil (ügyfél) | clientssl |
    |Forráscím fordítása | Automatikus leképezés |
        
    ![A bal oldali ablaktábla a jobb oldali ablaktábla "helyi forgalom >> virtuális kiszolgálók: Virtual Server List >> NGINX", ahol a szükséges információk megadására szolgál.](./media/network-howto-f5/image14.png)

    ![Ezen a lapon további információk adhatók meg. Frissítés és törlés gomb van.](./media/network-howto-f5/image15.png)

14. Ezzel végrehajtotta az NGINX-alkalmazás BIG-IP-konfigurációját. A megfelelő funkciók ellenőrzéséhez keresse fel a webhelyet, és ellenőrizze az F5 statisztikát.

15. Nyisson meg egy böngészőt, `https://<F5-public-VIP-IP>` és győződjön meg róla, hogy megjelenik a NGINX alapértelmezett lapja.

    !["Üdvözöljük az Nginx-ben!" az oldal azt jelzi, hogy az Nginx-webkiszolgáló telepítése sikeres volt, és további konfigurálásra van szükség. Két hivatkozás található a támogatási információk eléréséhez.](./media/network-howto-f5/image16.png)

16. Most ellenőrizze a virtuális kiszolgáló statisztikáit a forgalom ellenőrzéséhez, ehhez navigáljon a **statisztikák > modul statisztikái > helyi forgalom elemre**.

17. A **statisztika típusa**területen válassza a **virtuális kiszolgálók**elemet.

    ![A bal oldali ablaktábla a jobb oldali ablaktáblán a "statisztika >> modul statisztikái: helyi forgalom >> virtuális kiszolgálók" lehetőségre mutat, és a lista a NGINX virtuális kiszolgálót és másokat is megjeleníti. Az NGINX ki van emelve.](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>További tudnivalók

Az F5 használatával kapcsolatos további tudnivalók:

- [Adatközpont rendelkezésre állási szolgáltatásai BIG-IP DNS használatával](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [A BIG-IP rendszer üzembe helyezése HTTP-alkalmazásokkal](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Széles körű IP-cím létrehozása a GSLB számára](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md) 
