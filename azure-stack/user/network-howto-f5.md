---
title: Az F5 telepítése két Azure Stack hub-példány között
description: Ismerje meg, hogyan telepítheti az F5-et két Azure Stack hub-példány között.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: cfbd828923c7653da0f0bfd86ee74703897996c7
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661446"
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

-  A Azure Resource Manager sablont a következő GitHub-tárházban találja: https://github.com/Mikej81/f5-azurestack-gslb.

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Az F5 BIG-IP VE üzembe helyezése minden példányon

Központi telepítés Azure Stack hub A és A B példánya.

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

2. Válassza **az + erőforrás létrehozása**lehetőséget.

3. Keresés a piactéren a `F5`beírásával.

4. Válassza **az F5 Big-IP ve – mind (BYOL, 2 rendszerindító helyszín)** elemet.

    ![](./media/network-howto-f5/image1.png)

5. A következő lap alján válassza a **Létrehozás**elemet.

    ![](./media/network-howto-f5/image2.png)

6. Hozzon létre egy új, **F5-GSLB**nevű erőforráscsoportot.

7. A központi telepítés befejezéséhez példaként használja az alábbi értékeket:

    ![](./media/network-howto-f5/image3.png)

8. Ellenőrizze, hogy a telepítés sikeresen befejeződött-e.

    ![](./media/network-howto-f5/image4.png)

    > [!Note]  
    > Minden BIG-IP-telepítés körülbelül 20 percet vesz igénybe.

## <a name="configure-big-ip-appliances"></a>NAGYMÉRETŰ IP-készülékek konfigurálása

Kövesse az alábbi lépéseket mind a Azure Stack, mind A B hubhoz.

1. Jelentkezzen be az Azure Stack hub felhasználói portálra az A Azure Stack hub-példányon, és tekintse át a BIG-IP sablon üzembe helyezésével létrehozott erőforrásokat.

    ![](./media/network-howto-f5/image18.png)

2. A [Big-IP-konfigurációs elemek](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html)esetében kövesse az F5 billentyűt. 

3. A BIG-IP Wide IP-lista konfigurálásával figyelheti az Azure Stack hub-ba és A B-re telepített összes készüléket. Útmutatásért lásd: [Big-IP GTM-konfiguráció](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. A BIG-IP-készülékek feladatátvételének ellenőrzése. Tesztelési rendszeren konfigurálja a DNS-kiszolgálókat a következők használatára:
    - Azure Stack hub-példány A `f5stack1-ext` = nyilvános IP-cím
    - Azure Stack hub-példány B `f5stack1-ext` = nyilvános IP-címe

5. Tallózással `www.contoso.com` keresse meg a böngészőt, és töltse be az NGINX alapértelmezett oldalát.

## <a name="create-a-dns-sync-group"></a>DNS-szinkronizálási csoport létrehozása

1. A legfelső szintű fiók megbízhatóságának kiépítésének engedélyezése. Kövesse az utasításokat a [rendszerkezelési fiók jelszavának módosításakor (11. x-15. x)](https://support.f5.com/csp/article/K13121). Miután beállította a megbízhatóságot (Certificate Exchange), tiltsa le a root fiókot.

1. Jelentkezzen be a BIG-IP-re, és hozzon létre egy DNS-szinkronizálási csoportot. Útmutatásért tekintse meg a [Big-IP DNS-szinkronizálási csoport létrehozása](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html)című témakört.

    > [!Note]  
    > A BIP-IP berendezés helyi IP-címét az **F5-GSLB** erőforráscsoporthoz tekintheti meg. A hálózati adapter "f5stack1-ext", és szeretne csatlakozni a nyilvános vagy magánhálózati IP-címhez (a hozzáféréstől függően).

    ![](./media/network-howto-f5/image5.png)
          
    ![](./media/network-howto-f5/image6.png)

1. Válassza ki az új erőforráscsoportot **F5-GSLB** , majd válassza ki a **f5stack1** virtuális gépet a **Beállítások** területen a **hálózatkezelés**elemre.

## <a name="post-install-configurations"></a>Telepítés utáni konfigurációk

A telepítése után konfigurálnia kell az Azure Stack hub-NSG, és le kell zárnia a forrás IP-címeit.

1. A megbízhatóság létrehozása után tiltsa le a 22-es portot.

2. Ha a rendszer online állapotban van, tiltsa le a forrás NSG. A felügyeleti NSG le kell zárni a felügyeleti forrásba, a külső (4353/TCP) NSG a szinkronizáláshoz a másik példányba kell zárni. a 443-et a virtuális kiszolgálókkal rendelkező alkalmazások telepítése után is zárolni kell.

3. GTM_DNS szabály úgy van beállítva, hogy engedélyezze a port 53 (DNS) forgalmát a rendszerben, és a BIG-IP-feloldó megkezdi a munkát. A figyelők létre lettek hozva.

    ![](./media/network-howto-f5/image7.png)

4. Helyezzen üzembe egy alapszintű webalkalmazási munkaterhelést az Azure Stack hub-környezetben a BIG-IP-címek közötti terheléselosztáshoz. A NGNIX-kiszolgáló használatára például a [Nginx és a Nginx, valamint a Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/)használatával lehet telepíteni.

    > [!Note]  
    > Helyezzen üzembe egy NGNIX-példányt az A Azure Stack hub A és A B Azure Stack hub szolgáltatásban.

5. Miután az NGINX üzembe lett helyezve egy Docker-tárolóban egy Ubuntu virtuális gépen az Azure Stack hub-példányokon belül, ellenőrizze, hogy elérhető-e az alapértelmezett weblapon a kiszolgálókon.

    ![](./media/network-howto-f5/image8.png)

6. Jelentkezzen be a BIG-IP berendezés felügyeleti felületére. Ebben a példában az **F5-stack1-ext** nyilvános IP-címet használja.

    ![](./media/network-howto-f5/image9.png)

7. A BIG-IP használatával közzéteheti a NGINX-hozzáférést.
    
    -  Ebben a feladatban a BIG-IP-címet egy virtuális kiszolgálóval és egy készlettel konfigurálja, hogy engedélyezze a beérkező internet-hozzáférést a WordPress-alkalmazáshoz. Először azonosítania kell az NGINX-példány magánhálózati IP-címét.

8. Jelentkezzen be az Azure Stack hub felhasználói portálra. 

9. Válassza ki az NGINX hálózati adapterét.

    ![](./media/network-howto-f5/image10.png)

10. A BIG-IP konzolon lépjen a **helyi forgalom > készletek > készlet listára** , és **+** válassza a lehetőséget. Konfigurálja a készletet a tábla értékeinek használatával. Hagyja az összes többi mezőt az alapértelmezett értékre.

    ![](./media/network-howto-f5/image11.png)
    
    | Kulcs | Érték |
    | --- | --- |
    | Name (Név) | NGINX_Pool |
    | Állapotfigyelő | HTTPS |
    | Csomópont neve | NGINX |
    | Cím | \<saját NGINX saját IP-címe> |
    | Szolgáltatási port | 443 |

11. Válassza a **kész**lehetőséget. Ha helyesen van konfigurálva, a készlet állapota zöld.

    ![](./media/network-howto-f5/image12.png)

    Most konfigurálnia kell a virtuális kiszolgálót. Ehhez először meg kell keresnie az F5 BIG-IP-cím magánhálózati IP-címét.

12. A BIG-IP konzolon lépjen a **hálózati > saját IP** -címei elemre, és jegyezze fel az IP-címet.

    ![](./media/network-howto-f5/image13.png)

13. Hozzon létre egy virtuális kiszolgálót a **helyi forgalom** > **virtuális kiszolgálók** > **listájában** , és válassza **+** a lehetőséget. Konfigurálja a készletet a tábla értékeinek használatával. Hagyja az összes többi mezőt az alapértelmezett értékre.

    | Kulcs | Érték |
    | --- | --- |
    |Name (Név) | NGINX |
    |Cél címe | \<A BIG-IP> önkiszolgáló IP-címe |
    |Szolgáltatási port | 443 |
    |SSL-profil (ügyfél) | clientssl |
    |Forráscím fordítása | Automatikus leképezés |
        
    ![](./media/network-howto-f5/image14.png)

    ![](./media/network-howto-f5/image15.png)

14. Ezzel végrehajtotta az NGINX-alkalmazás BIG-IP-konfigurációját. A megfelelő funkciók ellenőrzéséhez keresse fel a webhelyet, és ellenőrizze az F5 statisztikát.

15. Nyisson meg egy böngészőt, `https://<F5-public-VIP-IP>` és győződjön meg róla, hogy megjelenik a NGINX alapértelmezett lapja.

    ![](./media/network-howto-f5/image16.png)

16. Most ellenőrizze a virtuális kiszolgáló statisztikáit a forgalom ellenőrzéséhez, ehhez navigáljon a **statisztikák > modul statisztikái > helyi forgalom elemre**.

17. A **statisztika típusa**területen válassza a **virtuális kiszolgálók**elemet.

    ![](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>További tudnivalók

Az F5 használatával kapcsolatos további tudnivalók:

- [Adatközpont rendelkezésre állási szolgáltatásai BIG-IP DNS használatával](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [A BIG-IP rendszer üzembe helyezése HTTP-alkalmazásokkal](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Széles körű IP-cím létrehozása a GSLB számára](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>További lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md) 
