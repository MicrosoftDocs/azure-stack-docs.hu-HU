---
title: Adatközpont-integráció tervezési szempontjai Azure Stack hub integrált rendszerek esetén
description: Ismerje meg, hogyan tervezhet és készíthet elő adatközpont-integrációt Azure Stack hub integrált rendszerekkel.
author: ihenkel
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: b4809454f6bec18fbfd2ffdc3f1aa866786199c5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882471"
---
# <a name="datacenter-integration-planning-considerations-for-azure-stack-hub-integrated-systems"></a>Adatközpont-integráció tervezési szempontjai Azure Stack hub integrált rendszerek esetén

Ha egy Azure Stack hub integrált rendszer érdekli, tisztában kell lennie a központi telepítéssel kapcsolatos főbb tervezési szempontokkal, valamint a rendszernek az adatközpontba való beilleszkedésével. Ez a cikk átfogó áttekintést nyújt ezekről a szempontokról, amelyek segítenek az Azure Stack hub integrált rendszereivel kapcsolatos fontos infrastruktúra-döntések meghozatalában. Ezen megfontolások ismerete segít, ha az OEM-hardver gyártójával dolgozik, miközben Azure Stack hubot telepít az adatközpontba.  

> [!NOTE]  
> Azure Stack hub integrált rendszerek csak az engedélyezett hardvergyártók vásárolhatók meg.

Azure Stack hub telepítéséhez a telepítés megkezdése előtt meg kell adnia tervezési információkat a megoldás szolgáltatójának, hogy a folyamat gyorsan és zökkenőmentesen induljon el. A szükséges információk a hálózati, a biztonsági és az identitási információk között számos fontos döntéssel rendelkeznek, amelyek számos különböző területből és döntéshozóból származó ismereteket igényelhetnek. A szervezet több csapatával is szüksége lesz arra, hogy az üzembe helyezés előtt minden szükséges információra kész legyen. Segítséget nyújt a hardver gyártójával való kommunikációhoz az adatok gyűjtése során, mert hasznos tanácsokkal rendelkezhetnek.

A szükséges információk megkeresése és összegyűjtése során előfordulhat, hogy a hálózati környezetbe be kell állítania néhány üzembe helyezés előtti konfigurációs módosítást. Ezek a változások közé tartozhatnak az Azure Stack hub-megoldás IP-címeinek megőrzése, valamint az útválasztók, kapcsolók és tűzfalak konfigurálása az új Azure Stack hub-megoldási kapcsolókhoz való kapcsolódás előkészítéséhez. Győződjön meg arról, hogy a tárgy szakembere a tervezéshez szükséges.

## <a name="capacity-planning-considerations"></a>Kapacitás-tervezési szempontok
Amikor kiértékel egy Azure Stack hub-megoldást a beszerzéshez, olyan hardverkonfiguráció-beállításokat tesz elérhetővé, amelyek közvetlen hatással vannak az Azure Stack hub-megoldás általános kapacitására. Ezek közé tartozik a CPU, a memória sűrűsége, a tárolási konfiguráció és a megoldás összesített mérete (például a kiszolgálók száma) klasszikus lehetőségei. A hagyományos virtualizációs megoldástól eltérően az összetevők egyszerű aritmetikaa nem alkalmazható a felhasználható kapacitás meghatározására. Az első ok az, hogy Azure Stack hub a megoldásban az infrastruktúra vagy a felügyeleti összetevők üzemeltetésére szolgál. A második ok az, hogy a megoldás szoftverének egy része a rugalmasság támogatásához van fenntartva, így a megoldás szoftverének frissítése a bérlői munkaterhelések megszakadásának minimalizálásával történik.

Az [Azure stack hub Capacity Planner táblázata](https://aka.ms/azstackcapacityplanner) segít megalapozott döntéseket hozni a kapacitás megtervezésére két módon. Az első a hardveres ajánlat kiválasztásával és az erőforrások kombinációjából való illeszkedéssel próbálkozik. A második az, hogy meghatározza azt a számítási feladatot, amely Azure Stack hub futtatására szolgál, hogy megtekintse az azt támogató rendelkezésre álló hardveres SKU-t. Végezetül a táblázat a Azure Stack hub megtervezésével és konfigurálásával kapcsolatos döntések meghozatalához nyújt útmutatást.

A táblázat nem helyettesíti a saját vizsgálatát és elemzését. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot a számolótáblán belül biztosított információra vonatkozóan.

## <a name="management-considerations"></a>Felügyelettel kapcsolatos szempontok
Azure Stack hub egy lezárt rendszer, ahol az infrastruktúra az engedélyektől és a hálózat szemszögéből is zárolva van. A hálózati hozzáférés-vezérlési listák (ACL-ek) az összes jogosulatlan bejövő forgalom blokkolására és az infrastruktúra-összetevők közötti szükségtelen kommunikációra vonatkoznak. Ez a rendszer megnehezíti a jogosulatlan felhasználók számára a rendszer elérését.

A napi felügyelet és a műveletek esetében nincs korlátlan rendszergazdai hozzáférés az infrastruktúrához. Azure Stack hub-operátoroknak a felügyeleti portálon vagy a Azure Resource Manageron keresztül kell kezelnie a Rendszerkezelőt (a PowerShell vagy a REST API használatával). Más felügyeleti eszközök (például a Hyper-V kezelője vagy a Feladatátvevőfürt-kezelő) nem férnek hozzá a rendszerhez. A rendszer védelméhez a külső gyártótól származó szoftvereket (például ügynököket) nem lehet az Azure Stack hub-infrastruktúra összetevőin belül telepíteni. A külső felügyeleti és biztonsági szoftverekkel való együttműködés a PowerShell vagy a REST API használatával történik.

Vegye fel a kapcsolatot Microsoft ügyfélszolgálata, ha magasabb szintű hozzáférésre van szüksége a riasztási közvetítés lépésein keresztül nem feloldott problémák elhárításához. A támogatáson keresztül a speciális műveletekhez biztosítható az ideiglenes teljes rendszergazdai hozzáférés a rendszerhez.

## <a name="identity-considerations"></a>Identitással kapcsolatos megfontolások

### <a name="choose-identity-provider"></a>Identitás-szolgáltató kiválasztása
Érdemes figyelembe vennie, hogy melyik identitás-szolgáltatót kívánja használni Azure Stack hub üzembe helyezéséhez, vagy az Azure AD-hez vagy a AD FShoz. Az identitás-szolgáltatókat az üzembe helyezést követően nem lehet átváltani a teljes rendszer újratelepítése nélkül. Ha nem rendelkezik az Azure AD-fiókkal, és a felhőalapú megoldás szolgáltatója által biztosított fiókot használja, és ha úgy dönt, hogy átváltja a szolgáltatót, és egy másik Azure AD-fiókot használ, kapcsolatba kell lépnie a megoldás szolgáltatójával a megoldás újbóli üzembe helyezéséhez a c-ban ost.

Az identitás-szolgáltató választása nem rendelkezik a bérlői virtuális gépeken (VM-EK), az Identity rendszeren, az általuk használt fiókokon, vagy ha Active Directory tartományhoz csatlakozik, és így tovább. Ezek a dolgok különállóak.

További információt az [Azure stack hub integrált rendszerek kapcsolatok modelljei című cikkben](./azure-stack-connection-models.md)talál.

### <a name="ad-fs-and-graph-integration"></a>AD FS és gráf-integráció
Ha úgy dönt, hogy az Azure Stack hub-t a AD FS identitás-szolgáltatóként helyezi üzembe, akkor a Azure Stack hub AD FS példányát egy meglévő AD FS példánnyal kell integrálnia egy összevonási megbízhatósági kapcsolaton keresztül. Ez az integráció lehetővé teszi egy meglévő Active Directory erdőben lévő identitások hitelesítését Azure Stack hub erőforrásaival.

A Graph szolgáltatást Azure Stack központban is integrálhatja a meglévő Active Directory. Ez az integráció lehetővé teszi a szerepköralapú Access Control (RBAC) kezelését Azure Stack hub-ban. Az erőforrásokhoz való hozzáférés delegálásakor a Graph-összetevő az LDAP protokoll használatával megkeresi a felhasználói fiókot a meglévő Active Directory erdőben.

Az alábbi ábra az integrált AD FS és a gráf forgalmának folyamatát mutatja be.
a AD FS és a Graph adatforgalmát bemutató diagram ![](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licencelési modell
El kell döntenie, hogy melyik licencelési modellt kívánja használni. Az elérhető lehetőségek attól függnek, hogy Azure Stack hubot az internethez csatlakoztatva van-e:
- A csatlakoztatott üzemelő [példányok](azure-stack-connected-deployment.md)esetében választhat az igény szerinti használat vagy a kapacitás alapú licencelés lehetőség közül. Az utólagos használathoz az Azure-hoz való kapcsolódásra van szükség, amely az Azure Commerce szolgáltatáson keresztül lesz kiszámlázva. 
- Csak a Capacity-alapú licencelés támogatott, ha az internetről [bontja a kapcsolatot](azure-stack-disconnected-deployment.md) . 

További információ a licencelési modellekről: [Microsoft Azure stack hub csomagolása és díjszabása](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Elnevezési döntések

Gondolja át, hogyan szeretné megtervezni az Azure Stack hub-névteret, különösen a régió nevét és a külső tartománynevet. A Azure Stack hub nyilvános végpontokhoz való központi telepítésének külső teljes tartományneve (FQDN) a következő két név kombinációja: &lt;*region*&gt;.&lt;*fqdn*&gt;. Például: *East.Cloud.fabrikam.com*. Ebben a példában a Azure Stack hub portálok a következő URL-címeken lesznek elérhetők:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Az Azure Stack hub-telepítéshez kiválasztott régió nevének egyedinek kell lennie, és a portál címeiben fog megjelenni. 

A következő táblázat összefoglalja ezeket a tartománynév-elnevezési döntéseket.

| Name (Név) | Leírás | 
| -------- | ------------- | 
|Régió neve | Az első Azure Stack hub-régió neve. Ez a név a Azure Stack hub által felügyelt nyilvános virtuális IP-címek (VIP) teljes tartománynevének részeként használatos. A régió neve általában egy fizikai hely azonosítója, például egy adatközpont helye.<br><br>A régió neve csak betűkből és számokból állhat, 0-9 között. Nem használhatók speciális karakterek (például `-`, `#`stb.).| 
| Külső tartomány neve | A külső virtuális IP-címekkel rendelkező végpontokhoz tartozó tartománynévrendszer (DNS) zóna neve. A nyilvános VIP-címek teljes tartománynevében használatos. | 
| Magánhálózati (belső) tartomány neve | A Azure Stack hub infrastruktúra-kezeléshez létrehozott tartományának (és belső DNS-zónájának) neve.
| | |

## <a name="certificate-requirements"></a>Tanúsítványokra vonatkozó követelmények

A telepítéshez SSL (SSL) tanúsítványokat kell megadnia a nyilvános végpontokhoz. A tanúsítványok magas szinten a következő követelményekkel rendelkeznek:

- Használhat egyetlen helyettesítő karaktert, vagy használhat dedikált tanúsítványokat is, majd használhat helyettesítő karaktereket csak olyan végpontokhoz, mint a Storage és a Key Vault.
- A tanúsítványokat egy nyilvános megbízható hitelesítésszolgáltató (CA) vagy egy ügyfél által felügyelt HITELESÍTÉSSZOLGÁLTATÓ is kiállíthatja.

Ha további információra van szüksége arról, hogy milyen PKI-tanúsítványokra van szükség a Azure Stack hub telepítéséhez és a beszerzéséhez, tekintse meg [Azure stack hub nyilvánoskulcs-infrastruktúrájának tanúsítványára vonatkozó követelményeket](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> A PKI-tanúsítvány megadott információit általános útmutatásként kell használni. Az Azure Stack hub PKI-tanúsítványainak beszerzése előtt működjön együtt az OEM-hardveres partnerrel. Részletes tanúsítvány-útmutatást és követelményeket biztosítanak.


## <a name="time-synchronization"></a>Idő szinkronizálása
Ki kell választania egy adott időkiszolgálót, amely a Azure Stack hub szinkronizálására szolgál. Az időszinkronizálás kritikus fontosságú az Azure Stack hub és az infrastruktúra szerepkörei számára, mivel a Kerberos-jegyek létrehozásához használatos. A Kerberos-jegyek a belső szolgáltatások hitelesítésére szolgálnak egymással.

Meg kell adnia egy IP-címet az időszinkronizálási kiszolgálóhoz. Bár az infrastruktúra legtöbb összetevője képes feloldani egy URL-címet, néhány csak az IP-címeket támogatja. Ha a leválasztott központi telepítési beállítást használja, meg kell adnia egy időkiszolgálót a vállalati hálózaton, hogy biztosan elérje az infrastruktúra-hálózatot Azure Stack központban.

## <a name="connect-azure-stack-hub-to-azure"></a>Azure Stack hub csatlakoztatása az Azure-hoz

Hibrid felhőalapú forgatókönyvek esetén meg kell terveznie, hogyan szeretné csatlakoztatni Azure Stack hubot az Azure-hoz. Az Azure-beli virtuális hálózatok két támogatott módszerrel csatlakoztathatók Azure Stack hubhoz:

- **Helyek közötti**kapcsolat: virtuális MAGÁNHÁLÓZATI (VPN) kapcsolat IPSec (IKE v1 és IKE v2) használatával. Az ilyen típusú kapcsolathoz VPN-eszköz vagy Útválasztás és távelérés szolgáltatás (RRAS) szükséges. További információ az Azure-beli VPN-átjárókkal kapcsolatban: [about VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Az alagúton keresztüli kommunikáció titkosított és biztonságos. A sávszélességet azonban az alagút maximális átviteli sebessége korlátozza (100-200 Mbps).

- **Kimenő NAT**: alapértelmezés szerint a Azure stack hub összes virtuális gépe kimenő NAT-kapcsolaton keresztül fog csatlakozni a külső hálózatokhoz. Az Azure Stack hub-ban létrehozott minden egyes virtuális hálózathoz hozzá lesz rendelve egy nyilvános IP-cím. Azt határozza meg, hogy a virtuális gép közvetlenül van-e hozzárendelve nyilvános IP-cím, vagy egy nyilvános IP-címmel rendelkező terheléselosztó mögött van-e kimenő hozzáférése kimenő NAT-kapcsolaton keresztül, a virtuális hálózat VIP-je használatával. Ez a módszer csak a virtuális gép által kezdeményezett és külső hálózatokra (Internet vagy intranet) szánt kommunikációra használható. Nem használható a virtuális géppel kívülről való kommunikációra.

### <a name="hybrid-connectivity-options"></a>Hibrid kapcsolati beállítások

A hibrid kapcsolatok esetében fontos figyelembe venni, hogy milyen típusú központi telepítésre van szükség, és hogy hol lesz telepítve. Meg kell fontolnia, hogy el kell-e különíteni a hálózati forgalmat a bérlők számára, és hogy van-e intranetes vagy internetes üzembe helyezése.

- **Egybérlős Azure stack hub**: egy Azure stack hub üzembe helyezése, amely legalább egy hálózati szempontból úgy néz ki, mintha egy bérlő lenne. Számos bérlői előfizetés lehet, de az intranetes szolgáltatásokhoz hasonlóan minden forgalom ugyanazon hálózatokon halad át. Az egyik előfizetés hálózati forgalma ugyanazon a hálózati kapcsolaton halad át, mint egy másik előfizetéssel, és nem kell titkosított alagúton keresztül elkülöníteni.

- **Több-bérlős Azure stack hub**: egy Azure stack hub üzembe helyezése, ahol az egyes bérlői előfizetések az Azure stack hub-on kívüli hálózatokhoz kötött adatforgalmát el kell különíteni más bérlők hálózati forgalmával.
 
- **Intranetes telepítés**: egy Azure stack központi telepítés, amely egy vállalati intraneten található, jellemzően magánhálózati IP-címtartomány és egy vagy több tűzfal mögött. A nyilvános IP-címek nem igazán nyilvánosak, mert nem irányíthatók közvetlenül a nyilvános interneten keresztül.

- **Internet-telepítés**: egy Azure stack hub központi telepítése, amely a nyilvános internethez csatlakozik, és internetre irányítható nyilvános IP-címeket használ a nyilvános VIP-tartományhoz. Az üzembe helyezés továbbra is tűzfal mögött található, de a nyilvános VIP-tartomány közvetlenül elérhető a nyilvános internetről és az Azure-ból.
 
A következő táblázat összefoglalja a hibrid csatlakozási forgatókönyveket a profik, a hátrányok és a használati esetek között.

| Alkalmazási helyzet | Csatlakozási módszer | Szakemberek | Hátrányok | Jó a következőhöz: |
| -- | -- | --| -- | --|
| Önálló bérlői Azure Stack hub, intranetes telepítés | Kimenő NAT | Jobb sávszélesség a gyorsabb átvitel érdekében. Egyszerűen megvalósítható; nincs szükség átjáróra. | A forgalom nincs titkosítva; nincs elkülönítés vagy titkosítás a veremön kívül. | Vállalati üzemelő példányok, ahol az összes bérlő egyformán megbízható.<br><br>Olyan vállalatok, amelyek rendelkeznek Azure ExpressRoute-áramkörrel az Azure-ban. |
| Több-bérlős Azure Stack hub, intranetes telepítés | Helyek közötti VPN | A bérlő VNet a célhelyre irányuló forgalom biztonságos. | A sávszélességet a helyek közötti VPN-alagút korlátozza.<br><br>Szükség van egy átjáróra a virtuális hálózaton és a célként megadott hálózaton lévő VPN-eszközön. | Vállalati üzemelő példányok, ahol bizonyos bérlői forgalmat más bérlők is biztonságossá kell tenniük. |
| Egybérlős Azure Stack hub, internetes telepítés | Kimenő NAT | Jobb sávszélesség a gyorsabb átvitel érdekében. | A forgalom nincs titkosítva; nincs elkülönítés vagy titkosítás a veremön kívül. | Üzemeltetési forgatókönyvek, amelyekben a bérlő saját Azure Stack hub-telepítést és dedikált áramkört kap a Azure Stack hub-környezethez. Például: ExpressRoute és többprotokollos felirat váltás (MPLS).
| Több-bérlős Azure Stack hub, Internet-telepítés | Helyek közötti VPN | A bérlő VNet a célhelyre irányuló forgalom biztonságos. | A sávszélességet a helyek közötti VPN-alagút korlátozza.<br><br>Szükség van egy átjáróra a virtuális hálózaton és a célként megadott hálózaton lévő VPN-eszközön. | Üzemeltetési forgatókönyvek, ahol a szolgáltató több-bérlős felhőt szeretne nyújtani, ahol a bérlők nem bíznak egymással, és a forgalmat titkosítani kell.
|  |  |  |  |  |

### <a name="using-expressroute"></a>A ExpressRoute használata

Az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) -on keresztül csatlakoztathatja Azure stack hubot az Azure-hoz mind az egybérlős intranetes, mind a több-bérlős környezetben. Szüksége lesz egy kiépített ExpressRoute-áramkörre [egy kapcsolati szolgáltatón](https://docs.microsoft.com/azure/expressroute/expressroute-locations)keresztül.

Az alábbi ábrán egy egybérlős forgatókönyv ExpressRoute látható (ahol az "ügyfél kapcsolata" a ExpressRoute áramkör).

![Az egybérlős ExpressRoute forgatókönyvét bemutató ábra](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

A következő ábra a több-bérlős forgatókönyvek ExpressRoute mutatja be.

![Több-bérlős ExpressRoute forgatókönyvet bemutató diagram](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Külső figyelés
Az Azure Stack hub központi telepítéséről és eszközeiről érkező riasztások egyetlen nézetének beszerzéséhez, valamint a riasztások meglévő IT-szolgáltatások felügyeleti munkafolyamataiba való integrálásához a [Azure stack hub külső adatközpont-figyelési megoldásokkal integrálható](azure-stack-integrate-monitor.md).

A Azure Stack hub-megoldás részeként a hardver életciklus-állomása egy olyan számítógép, amely a Azure Stack hub rendszeren kívül fut, és a hardveres gyártó által biztosított felügyeleti eszközöket futtatja. Ezeket az eszközöket vagy egyéb megoldásokat használhatja, amelyek közvetlenül integrálhatók az adatközpontban meglévő figyelési megoldásokkal.

Az alábbi táblázat összefoglalja a jelenleg elérhető lehetőségek listáját.

| Terület | Külső figyelési megoldás |
| -- | -- |
| Azure Stack hub szoftver | [Operations Manager Azure Stack hub felügyeleti csomagja](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios beépülő modul](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-alapú API-hívások | 
| Fizikai kiszolgálók (bmc-n keresztül IPMI) | OEM hardver – Operations Manager gyártói felügyeleti csomag<br>OEM hardveres gyártó által biztosított megoldás<br>Hardveres szolgáltatói Nagios beépülő modulok.<br>OEM partner által támogatott figyelési megoldás (tartozék) | 
| Hálózati eszközök (SNMP) | Operations Manager hálózati eszközök felderítése<br>OEM hardveres gyártó által biztosított megoldás<br>A Nagios kapcsoló beépülő modulja |
| Bérlői előfizetés állapotának figyelése | [System Center felügyeleti csomag a Windows Azure-hoz](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vegye figyelembe a következő követelményeket:
- A használt megoldásnak ügynök nélkül kell lennie. Azure Stack hub-összetevőkön belül nem telepíthet külső gyártótól származó ügynököket. 
- Ha System Center Operations Managert szeretne használni, Operations Manager 2012 R2 vagy Operations Manager 2016 szükséges.

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

A biztonsági mentés és a vész-helyreállítás megtervezése magában foglalja a IaaS virtuális gépeket és a Pásti-szolgáltatásokat üzemeltető mögöttes Azure Stack hub-infrastruktúra tervezését, valamint a bérlői alkalmazásokat és adatokat. Ezeket a dolgokat külön tervezze meg.

### <a name="protect-infrastructure-components"></a>Infrastruktúra-összetevők biztosítása

Azure Stack hub infrastruktúra-összetevők [biztonsági mentését](azure-stack-backup-back-up-azure-stack.md) egy Ön által megadott SMB-megosztáson végezheti el:

- Külső SMB-fájlmegosztás szükséges egy meglévő Windows-alapú fájlkiszolgálón vagy harmadik féltől származó eszközön.
- Ugyanezt a megosztást használja a hálózati kapcsolók és a hardveres életciklus-gazdagép biztonsági mentéséhez. Az OEM-hardvergyártók segítséget nyújtanak az összetevők biztonsági mentéséhez és visszaállításához, mivel ezek kívül vannak Azure Stack hub-on. Ön felelős a biztonsági mentési munkafolyamatok futtatásához az OEM-gyártó javaslata alapján.

Ha katasztrofális adatvesztés következik be, az infrastruktúra biztonsági másolatával átadhatja az üzembe helyezési adatforrásokat, például a következőket: 

- Központi telepítési bemenetek és azonosítók
- Szolgáltatásfiókok
- HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány
- fFederated-erőforrások (leválasztott központi telepítések esetén)
- Csomagok, ajánlatok, előfizetések és kvóták
- RBAC szabályzat és szerepkör-hozzárendelések
- Key Vault titkok

### <a name="protect-tenant-apps-on-iaas-vms"></a>Bérlői alkalmazások védetté IaaS virtuális gépeken

Azure Stack hub nem készít biztonsági másolatot a bérlői alkalmazásokról és az adatbázisokról. Meg kell terveznie a biztonsági mentést és a vész-helyreállítási védelmet a Azure Stack hub-on kívüli célhoz. A bérlői védelem bérlő által vezérelt tevékenység. A IaaS virtuális gépeken a bérlők a mappák, az alkalmazásadatok és a rendszerállapot védelme érdekében a vendégen belüli technológiákat is használhatják. Vállalati vagy szolgáltatóként azonban előfordulhat, hogy egy biztonsági mentési és helyreállítási megoldást kíván használni ugyanabban az adatközpontban vagy külsőleg egy felhőben.

A Linux vagy a Windows IaaS virtuális gépek biztonsági mentéséhez olyan biztonsági mentési termékeket kell használnia, amelyeknek hozzáférése van a vendég operációs rendszerhez a fájl, mappa, operációs rendszer állapot és alkalmazásadatok védelme érdekében. Használhatja a Azure Backup, a System Center Datacenter Protection Managert vagy a harmadik féltől származó termékeket.

Ha egy másodlagos helyre szeretné replikálni az alkalmazásokat, és vészhelyzet esetén az alkalmazás feladatátvételét koordinálja, használhat Azure Site Recovery vagy támogatott harmadik féltől származó termékeket. Emellett a natív replikálást támogató alkalmazások, például a Microsoft SQL Server is képesek replikálni az adatfájlokat egy másik helyre, ahol az alkalmazás fut.

## <a name="learn-more"></a>További információk

- További információ a használati esetekről, a beszerzésről, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack hub](https://azure.microsoft.com/overview/azure-stack/) terméke oldal.
- Az Azure Stack hub integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a következő tanulmányt: [Azure stack hub: az Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Következő lépések
[Azure Stack hub üzembe helyezési kapcsolatainak modelljei](azure-stack-connection-models.md)
