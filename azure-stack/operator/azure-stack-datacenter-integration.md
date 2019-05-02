---
title: Általános adatközpont integrációja szempontok az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, mi mindent most tervezheti meg és készítse elő az Adatközpont integrációja az Azure Stack több csomópontos.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 9aa8ec8bbbda1548088ad9dfeb389d292a0e1fbb
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985382"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Azure Stack integrált rendszerek adatközpont integrációja szempontjai
Ha érdeklik az Azure Stackkel integrált rendszereknél, tisztában kell lennie a főbb tervezési megfontolások körül a rendszerbe állítás és az hogyan illeszkedik a a rendszer az adatközpontban. Ez a cikk segítséget nyújt az Azure Stack több csomópontos rendszerének fontos infrastruktúra döntéseket ezeket a szempontokat magas szintű áttekintést nyújt. A felsorolt szempontok megismerése segít az használatakor a számítógép-Gyártói hardver gyártójától, azok az Azure Stack üzembe helyezése a helyi adatközpontban.  

> [!NOTE]
> Az Azure Stack több csomópontos rendszerekhez csak a hitelesített hardveren szállítóktól vásárolható meg. 

Azure Stack üzembe helyezéséhez meg kell adnia tervezési információkat a megoldásszolgáltató központi telepítés segítségével gyorsan és problémamentesen nyissa meg a folyamat megkezdése előtt. Az információk címtartományok szükséges hálózati, biztonsági és számos fontos döntéseket hozhat, amely számos különböző területekre és döntéshozók Tudásbázis szükség lehet a azonosító adatok között. Emiatt előfordulhat, hogy van kérje le a személyeket a szervezetben több csapat által, és gondoskodjon arról, hogy minden szükséges információját készen üzembe helyezés megkezdése előtt. Emellett segít beszélgetni hardvergyártójához ezen információk összegyűjtése közben tanácsokat a döntések elősegítése érdekében hasznos lehet, hogy rendelkeznek.

Vizsgálja, és a szükséges adatokat gyűjt, miközben szükség lehet, hogy a központi telepítés előtti konfigurációs módosításokat a hálózati környezethez. Ez magában foglalhatja foglalása az IP-címterek, az Azure Stack az útválasztók, kapcsolók és tűzfalak fel a kapcsolatot az új Azure Stack megoldás kapcsolók konfigurálása a megoldáshoz. Ellenőrizze, hogy a tárgy területen szakértő akár vonalas segítséget nyújt a tervezés rendelkezik.

## <a name="capacity-planning-considerations"></a>Kapacitástervezésének szempontjai
Az Azure Stack megoldás beszerzési kiértékelésekor hardver konfigurációs beállításokkal kell tenni amelyek közvetlen hatással vannak az Azure Stack megoldás teljes kapacitásának. Ezek közé tartoznak a Processzor, memória sűrűség, tárolási konfigurációt és a teljes megoldás méretezési (pl. kiszolgálók száma) klasszikus választási lehetőség. Ellentétben a hagyományos virtualizálási megoldás az egyszerű számtani felhasználható kapacitás meghatározásához ezeket az összetevőket nem vonatkozik. Az első oka, hogy az Azure Stack tervezésnek lesz-e üzemeltetni az infrastruktúrát vagy felügyeleti összetevők magát a megoldáson belül. A második oka, hogy a megoldás kapacitás némelyike rugalmasság; feladat foglalt a megoldás szoftverek oly módon, hogy minimalizálja a bérlői terhelések megszakadását frissítése. 

A [Azure Stack kapacitását planner számolótábla](https://aka.ms/azstackcapacityplanner) segít tájékoztatni vonatkozó kétféleképpen kapacitásának tervezési döntések: vagy az egy hardver ajánlat kiválasztásával és az erőforrások vagy együttes igazítás kísérlet definiálásával Azure Stack számítási feladatokra szolgál a hardver által támogatott SKU-k megtekintéséhez futtassa. Végül a számolótábla célja az Azure Stack-tervezési és konfigurációs kapcsolatos döntések elősegítése érdekében az útmutató. 

A táblázat nem célja, hogy a saját vizsgálati és elemzési helyettesíti.  Microsoft nem vállal értük felelősséget vagy rájuk kifejezett vagy törvényi garanciát a számolótábla megadott információkat.



## <a name="management-considerations"></a>Eszközkezeléssel kapcsolatos szempontok
Az Azure Stack egy rendszer lezárt, ahol az infrastruktúra rögzítve van egy engedélyek közül mindkettőt és a hálózati szempontjából. Hálózati hozzáférés-vezérlési listák (ACL) minden nem engedélyezett a bejövő forgalmat és felesleges infrastruktúra-összetevők közötti kommunikációhoz blokkolása érvényesek. Így a jogosulatlan felhasználók hozzáférhetnek a rendszerhez.

A napi felügyeleten és műveletek esetében nem korlátozott rendszergazdai hozzáféréssel az infrastruktúra nem. Az Azure Stack-operátorok a rendszer a felügyeleti portálon keresztül vagy az Azure Resource Manager (PowerShell vagy a REST API) keresztül kell kezelni. Nincs nem érhető el a rendszer más felügyeleti eszközökkel, például a Hyper-V kezelőjével vagy a Feladatátvevőfürt-kezelőben. A rendszer védelme érdekében, harmadik féltől származó szoftverek (például ügynököket) belül az Azure Stack-infrastruktúra összetevői nem telepíthetők. Együttműködés a külső felügyeleti és biztonsági szoftver PowerShell vagy a REST API-n keresztül történik.

Ha magasabb szintű hozzáférési problémák megoldásához, amelyek nem megoldott riasztás közvetítés lépéseket van szükség, Support kell működnie. Támogatásának köszönhetően a Speciális műveletek végrehajtásához a rendszer ideiglenes teljes rendszergazdai hozzáférést biztosítania módszer. 

## <a name="identity-considerations"></a>Identitás kapcsolatos szempontok

### <a name="choose-identity-provider"></a>Identitásszolgáltató kiválasztása
Fontolja meg az Azure AD vagy az AD FS, az Azure Stack központi telepítéshez használni kívánt melyik identitásszolgáltatóhoz kell. Identitás-szolgáltatóktól nem lehet átállítani a teljes rendszer újbóli üzembe helyezés nélküli üzembe helyezés után. Ha nem Ön a tulajdonosa az Azure AD-fiókot, és a Felhőszolgáltató által biztosított rendelkező fiókot használ, és ha úgy dönt, hogy a szolgáltató váltson, és használjon egy másik Azure AD-fiók, ezen a ponton kell forduljon annak érdekében, hogy a megoldás f újbóli üzembe helyezéséhez vagy, a költségek.

Identitás-szolgáltató tetszőleges nem befolyásolta a bérlői virtuális gépeket, a identitásrendszer és fiókokat használnak, hogy azok csatlakozhassanak az Active Directory-tartományhoz, és így tovább. Ez a különálló.

Az identitásszolgáltatót kiválasztásáról többet is megtudhat a [Azure Stack integrált rendszerek kapcsolati modellek cikk](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Az AD FS- és Graph-integráció
Ha az AD FS használata Identitásszolgáltatóként az Azure Stack üzembe helyezése, integrálnia kell az AD FS-példányt az Azure Stacken keresztül összevonási megbízhatósági kapcsolat egy meglévő AD FS példánnyal. Ez lehetővé teszi az identitásokat a az Azure Stackben erőforrásokkal történő hitelesítéshez meglévő Active Directory-erdőben.

A Graph szolgáltatás az Azure Stackben is integrálható a meglévő Active Directory. Ez lehetővé teszi, hogy kezelése szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Stackben. Erőforráshoz való hozzáférés delegálása, a Graph-összetevő jelenik meg a felhasználói fiókot a meglévő Active Directory-erdőben, az LDAP protokoll használatával.

Az alábbi ábrán látható integrált az AD FS- és Graph adatforgalmat.
![Az AD FS- és Graph adatforgalmat bemutató ábra.](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licencelési modell
Döntse el, melyik licencelési modell létrehozásához használni szeretne. Az elérhető lehetőségek attól függnek,-e az internethez csatlakoztatott Azure Stack üzembe helyezése:
- Az egy [kapcsolódó telepítési](azure-stack-connected-deployment.md), választhatja a használatalapú mint-akkor-használható vagy a kapacitás-alapú licencelés. Használatalapú-,-Ön-használatához szükséges jelentés használati, amely az Azure kereskedelmi keresztül történik majd csatlakozni az Azure-bA. 
- Csak kapacitásalapú licencelési akkor támogatott, ha Ön [üzembe helyezése leválasztott](azure-stack-disconnected-deployment.md) az internetről. 

További információ a licencmodellek szükségesek: [a Microsoft Azure Stack csomagolására és díjszabás](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Elnevezési döntéseket hozhat

Hogyan szeretné megtervezése az Azure Stack-névteret, különösen a régió neve és a külső tartománynév gondolja kell. Az Azure Stack üzemelő példányához tartozó nyilvános végpontok külső teljesen minősített tartománynevét (FQDN) a következő két neve együttes használata: &lt; *régió*&gt;.&lt; *fqdn*&gt;. Ha például *east.cloud.fabrikam.com*. Ebben a példában az Azure Stack portálok lenne a következő URL-címekkel érhető el:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Az Azure Stack üzembe helyezéshez választott régióban név egyedinek kell lennie, és a portál címeket fog megjelenni. 

A következő táblázat összefoglalja a ezek tartományi névhasználati döntéseket hozhat.

| Name (Név) | Leírás | 
| -------- | ------------- | 
|Régió neve | Az első Azure Stack-régió neve. Ezt a nevet a teljes tartománynév részeként szolgál a nyilvános virtuális IP-címek (VIP), amely az Azure Stack kezeli. A régió neve általában egy fizikai helyazonosító, például egy adatközpont elhelyezkedése lesz.<br><br>A régió neve csak betűket és számokat, 0 – 9 között kell állnia. Nincsenek speciális karaktereket, például "-" vagy "#", stb. engedélyezett.| 
| Külső tartomány neve | A tartománynévrendszer (DNS) zóna neve kívülre irányuló virtuális IP-címeket használó végpontokon. A teljes Tartománynevét használják a nyilvános virtuális IP-címeket. | 
| Saját tartománynevet (belső) | A tartomány (és belső DNS-zóna) az infrastruktúra-felügyelettel az Azure Stacken létrehozott neve. 
| | |

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

Üzembe helyezés kell adja meg a Secure Sockets Layer (SSL) tanúsítványok nyilvános végpontok. Magas szinten a tanúsítványokat az alábbi követelményekkel rendelkezik:

- Egyetlen helyettesítő tanúsítvány is használhatja, vagy egy dedikált tanúsítványokat használ, és a helyettesítő karakterek használata kizárólag végpontok például a tárolási és Key Vault.
- Egy nyilvános megbízható hitelesítésszolgáltató (CA) által adhatók ki tanúsítványok vagy egy ügyfél által felügyelt hitelesítésszolgáltató.

Milyen nyilvános kulcsokra épülő infrastruktúra további információt tanúsítványokat kell telepíteni az Azure Stack, és azok beszerzésével, tekintse meg, hogyan [Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványkövetelmények](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> A megadott PKI-tanúsítvány adatait kell használni, általános útmutatást. Mielőtt bármilyen PKI-tanúsítványokat az Azure Stack szerez be, a Számítógépgyártó hardver partnerrel együttműködve. Nyújtanak részletesebb tanúsítvány-irányelvek és követelmények lesz.


## <a name="time-synchronization"></a>Időszinkronizálás
Ki kell választania egy megadott idő a kiszolgáló szinkronizálása az Azure Stack segítségével.  Időszinkronizálás az Azure Stacket és az infrastruktúra-szerepkörök, mivel ez belső szolgáltatások egymással hitelesítéséhez Kerberos-jegyekhez létrehozására szolgál.

Meg kell adnia a szinkronizálási kiszolgálót, az IP-címet az infrastruktúra-összetevők a legtöbb fel tudja oldani az egy URL-CÍMÉT, bár egyes csak támogatja IP-címek. Ha a kapcsolat nélküli telepítési beállítást használja, meg kell adnia egy kiszolgálót, amelynek Ön a vállalati hálózatban arra is érhető el az Azure stack infrastruktúra-hálózat.

## <a name="connect-azure-stack-to-azure"></a>Csatlakozás az Azure Stack az Azure-bA

A hibrid felhős rendszerekben meg kell terveznie a csatlakoztatása az Azure Stack az Azure-módját. Az Azure Stackben virtuális hálózatok összekapcsolása virtuális hálózatok az Azure-ban támogatott két módszer áll rendelkezésre: 
- **Site-to-site**. Egy virtuális magánhálózati (VPN) kapcsolat IPsec (IKE v1 és IKE v2). Ez a kapcsolattípus egy VPN-eszköz vagy az Útválasztás és távelérés szolgáltatás (RRAS) szükséges. Az Azure-beli VPN-átjárókkal kapcsolatos további információkért lásd: [információk a VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A kommunikáció ezen az alagúton keresztül van titkosítva, és biztonságos. Azonban a sávszélesség korlátozza a maximális átviteli sebesség az alagút (100 – 200 MB/s).
- **Kimenő NAT**. Alapértelmezés szerint az összes virtuális gépek az Azure Stackben elérhetőséget külső hálózatokhoz kimenő NAT-n keresztül Minden virtuális hálózat jön létre az Azure Stackben hozzárendelt nyilvános IP-cím beolvasása. -E a virtuális gép közvetlenül hozzá rendelt nyilvános IP-címet, vagy egy nyilvános IP-címmel rendelkező terheléselosztó mögött található, azt a használatával a virtuális IP-CÍMEK a virtuális hálózat kimenő NAT-n keresztül kimenő hozzáférést kap. Ez csak a virtuális gép által kezdeményezett, és a külső hálózatokat (internetes vagy intranetes) felé irányuló kommunikációt működik. A virtuális gép kívülről folytatott kommunikációhoz használható.

### <a name="hybrid-connectivity-options"></a>Hibrid kapcsolati lehetőségek

A hibrid kapcsolatokat fontos fontolja meg, milyen üzembe helyezési szeretne ajánlani, és hol fogja telepíteni. Érdemes figyelembe venni, hogy el kell különítenie bérlőnként hálózati forgalmat, és e rendelkezni fog egy intranetes vagy internetes központi szüksége.

- **Az Azure Stack egybérlős**. Az Azure Stack üzemelő példányához, amely úgy tűnik, legalább egy hálózati szempontból a, ha azt egy bérlő. Nem lehet több bérlői előfizetések, de bármely intranetes szolgáltatás, például az összes forgalom ugyanazon a hálózaton keresztül fogadja. Egy előfizetésből származó hálózati forgalom egy másik előfizetés az azonos hálózati kapcsolaton keresztül fogadja, és nem kell elkülöníteni egy titkosított csatornán keresztül.

- **Az Azure Stack több-bérlős**. Az Azure Stack üzemelő példányához, ahol minden egyes bérlő előfizetés forgalmat, amely az Azure Stackhez külső hálózatokhoz kötött különítve más bérlők hálózati forgalmat kell lennie.
 
- **Intranetes telepítési**. Az Azure Stack üzemelő példányához, amely általában a privát IP-címteret és a egy vagy több tűzfal mögött helyezkedik el a vállalati intraneten egyaránt elérhetik. A nyilvános IP-címek nem nyilvánosak valóban, mivel nem irányítható közvetlenül a nyilvános interneten keresztül.

- **Internetes üzembe helyezési**. Az Azure Stack üzemelő példányához, amely csatlakozik a nyilvános internet és a használt internetről elérhető nyilvános IP-címek esetében a nyilvános VIP-címtartománnyal. Az üzemelő példány továbbra is Dőljön tűzfal mögött található, de a nyilvános virtuális IP-cím-tartományt a nyilvános internetről és az Azure közvetlenül elérhető.
 
A következő táblázat összefoglalja a hibrid kapcsolat forgatókönyveket, a szakembereknek, a hátrányai, és a használati esetek.

| Forgatókönyv | Kapcsolati módszer | Szakemberek számára | Hátrányok | Megfelelő választás a |
| -- | -- | --| -- | --|
| Egyetlen bérlő az Azure Stack, intranet üzembe helyezés | Kimenő NAT | A gyorsabb adatátvitel jobb sávszélességet. Egyszerű megvalósítani; nem szükséges átjáró. | A forgalom nem titkosított; Nincs elkülönítés vagy a halmazon kívül a titkosítás. | Nagyvállalati környezetben, ahol egyetlen bérlő számára egyaránt megbízhatónak minősülnek.<br><br>Olyan vállalatok, amelyek egy Azure ExpressRoute-kapcsolatcsoporttal az Azure-bA. |
| Több-bérlős Azure Stack, intranet üzembe helyezés | Site-to-site VPN | A bérlői virtuális hálózatok közötti forgalmat cél védve legyen. | Site-to-site VPN-alagút sávszélessége korlátozza.<br><br>A virtuális hálózat és VPN-eszközt a cél hálózati átjáróra van szükség. | Nagyvállalati környezetben, ahol az egyes bérlői forgalom más bérlőktől származó védettnek kell lennie. |
| Egyetlen bérlő az Azure Stack, internetes központi telepítés | Kimenő NAT | A gyorsabb adatátvitel jobb sávszélességet. | A forgalom nem titkosított; Nincs elkülönítés vagy a halmazon kívül a titkosítás. | Szolgáltatási forgatókönyvek esetében, ahol a bérlői lekérdezi a saját Azure Stack üzembe helyezés és a egy dedikált kapcsolatcsoport az Azure Stack-környezet. Például az ExpressRoute és Multiprotocol címke váltás (mpls virtuális Magánhálózat).
| Több-bérlős Azure Stack, internetes központi telepítés | Site-to-site VPN | A bérlői virtuális hálózatok közötti forgalmat cél védve legyen. | Site-to-site VPN-alagút sávszélessége korlátozza.<br><br>A virtuális hálózat és VPN-eszközt a cél hálózati átjáróra van szükség. | Szolgáltatási forgatókönyvek esetében, ahol a szolgáltató szeretne egy több-bérlős felhőalapú ajánlat, ahol a bérlők számára nem megbízható egymással és a forgalom titkosítani kell.
|  |  |  |  |  |

### <a name="using-expressroute"></a>ExpressRoute-tal

Azure Stack csatlakozhat az Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) intranetes egybérlős és több-bérlős helyzetekben. Szüksége lesz egy keresztül kiosztott ExpressRoute-kapcsolatcsoport [kapcsolati](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Az alábbi ábra bemutatja az ExpressRoute egy egybérlős forgatókönyv esetében (ahol "Az ügyfél-kapcsolat" az ExpressRoute-kapcsolatcsoport van).

![Diagram megjelenítése egybérlős ExpressRoute forgatókönyv](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Az alábbi ábrán látható ExpressRoute olyan több-bérlős forgatókönyvben.

![Diagram megjelenítése több-bérlős ExpressRoute forgatókönyv](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Külső figyelése
Az összes riasztást egyetlen nézetben kérhet az Azure Stack üzembe helyezés és az eszközök, és a riasztások integrálhatja meglévő informatikai szolgáltatás felügyeleti munkafolyamatokat jegykiadás kapcsolatos is [Azure Stack integrálható külső adatközpont-felügyeleti megoldások](azure-stack-integrate-monitor.md).

Az Azure Stack megoldás részét képező, a hardver életciklus állomás kívüli Azure Stack hardver OEM gyártó által biztosított felügyeleti eszközöket futtató számítógépen. Ezekkel az eszközökkel vagy más megoldásokkal, amelyek közvetlenül integrálható a meglévő figyelési megoldásokkal, a helyi adatközpontban is használhatja.

A következő táblázat összefoglalja a jelenleg rendelkezésre álló beállítások listája.

| Terület | Külső figyelő megoldás |
| -- | -- |
| Az Azure Stack szoftver | [Az Azure Stack felügyeleti csomag az Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios-beépülő modul](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-alapú API-hívások | 
| Fizikai kiszolgálók (bmc-k IPMI-n keresztül) | OEM hardver – Operations Manager szállító felügyeleti csomag<br>OEM hardvert gyártó által biztosított megoldás<br>Hardver szállítójával Nagios beépülő modulokat.<br>OEM-partner által támogatott figyelési megoldáshoz (tartalmazza) | 
| Hálózati eszközök (SNMP) | Az Operations Manager hálózati eszközök felderítése<br>OEM hardvert gyártó által biztosított megoldás<br>Nagios kapcsoló beépülő modul |
| Bérlő előfizetési állapotfigyelés | [A System Center Management Pack for Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vegye figyelembe az alábbi követelményeknek:
- A megoldás használata ügynök nélküli kivételfigyelés kell lennie. Külső ügynökök belül az Azure Stack-összetevők nem telepíthető. 
- Ha szeretné használni a System Center Operations Manager, az Operations Manager 2012 R2 vagy az Operations Manager 2016-ra szükség.

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

Biztonsági mentés és vészhelyreállítás megtervezése magában foglalja a tervezési mindkét a mögöttes Azure Stack infrastruktúrájának IaaS virtuális gépek és PaaS-szolgáltatásokat üzemeltető és bérlői alkalmazásokhoz és adatokhoz. Meg kell terveznie ezeket külön-külön.

### <a name="protect-infrastructure-components"></a>Infrastruktúra-összetevőket védelme

Is [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md) infrastruktúra-összetevőket egy SMB-megosztás, adjon meg:

- Szüksége lesz egy külső SMB-fájlmegosztás a meglévő Windows-alapú fájlkiszolgáló vagy egy külső eszköz.
- A biztonsági mentés hálózati kapcsolók és a hardver életciklus gazdagép a megosztáson kell használnia. OEM hardvergyártójához segítségével szolgál útmutatással a biztonsági mentés és visszaállítás ezeket az összetevőket, mivel ezek a külső, az Azure Stackhez. Ön felelős az OEM beszállítói ajánlása alapján a biztonsági mentési munkafolyamatok futtatása.

Végzetes adatvesztés esetén használhatja az infrastruktúra biztonsági mentését a reseed telepítési adatok, például az üzembe helyezés bemeneteit és azonosítók, szolgáltatásfiókok, legfelső szintű hitelesítésszolgáltató tanúsítványát, összevont erőforrások (a kapcsolat nélküli üzembe helyezés), csomagok, ajánlatok, az előfizetések, kvóták, RBAC házirend- és szerepkör-hozzárendelések és a Key Vault titkos kulcsok.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Bérlői alkalmazások IaaS virtuális gépek védelme

Az Azure Stack nem készített biztonsági bérlői alkalmazások és adatok mentése. Meg kell terveznie a biztonsági mentési és vészhelyreállítási védelem biztosításához egy cél Azure stack külső. Bérlő védelem egy bérlő által készített tevékenységet. IaaS virtuális gépek esetében bérlők használhatnak a Vendég technológiák a fájlmappák, az alkalmazás adatok és a rendszerállapot védelme. Azonban egy enterprise vagy a service provider, érdemes ugyanabban az adatközpontban vagy a felhőben külső biztonsági mentési és helyreállítási megoldást.

Linux vagy Windows IaaS virtuális gépek biztonsági mentése, kell használnia biztonságimásolat-készítő a vendég operációs rendszer-hozzáféréssel rendelkező fájl, mappa, operációs rendszer állapotának és alkalmazások adatainak védelme érdekében. Azure Backup szolgáltatással, a System Center Data Center Protection Manager használatára, de támogatja a harmadik féltől származó termékekre.

Egy másodlagos helyre replikálja az adatokat, és az alkalmazás feladatátadását, ha katasztrófa történik, használhatja az Azure Site Recovery vagy támogatott külső termékekhez. Alkalmazások, amelyek támogatják a natív replikáció esetén (például a Microsoft SQL Server) is, adatokat egy másik helyre, ahol az alkalmazás fut. replikálhatja.

## <a name="learn-more"></a>Részletek

- További információ a használati esetek, vásárlás, partnerek és OEM hardverszállítók: a [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) termékoldalán.
- Az ütemterv és a rendelkezésre állás földrajzi információ az Azure Stack integrált rendszerek, tekintse meg a: [Azure Stack: Azure-bővítmény](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>További lépések
[Azure Stack üzemelő példányainak kapcsolati modelljei](azure-stack-connection-models.md)
