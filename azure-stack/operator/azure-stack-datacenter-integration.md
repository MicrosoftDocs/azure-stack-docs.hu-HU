---
title: Általános adatközpont-integrációs megfontolások Azure Stack integrált rendszerek esetében | Microsoft Docs
description: Ismerje meg, mit tehet a tervezéshez, és készüljön fel az adatközpont-integrációra a többcsomópontos Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 07191c95ede4f9a3ce80117bfeca9b5a02cfc0b1
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417371"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Adatközpont-integrációs megfontolások Azure Stack integrált rendszerek esetében

Ha Azure Stack integrált rendszer érdekli, tisztában kell lennie a központi telepítéssel kapcsolatos főbb tervezési szempontokkal, és hogy a rendszer hogyan illeszkedik az adatközponthoz. Ez a cikk átfogó áttekintést nyújt ezekről a szempontokról, amelyek segítenek a Azure Stack többcsomópontos rendszerre vonatkozó fontos infrastruktúra-döntések meghozatalában. Ezen megfontolások ismerete segít az OEM-hardvergyártók használatakor, amikor Azure Stack telepítik az adatközpontba.  

> [!NOTE]  
> A többcsomópontos rendszerek Azure Stack csak a jóváhagyott hardvergyártók vásárolhatók meg. 

Azure Stack telepítéséhez a telepítés megkezdése előtt meg kell adnia tervezési információkat a megoldás szolgáltatójának, hogy a folyamat gyorsan és zökkenőmentesen induljon el. A szükséges információk a hálózati, a biztonsági és az identitási információk között számos fontos döntéssel rendelkeznek, amelyek számos különböző területből és döntéshozóból származó ismereteket igényelhetnek. Ezért előfordulhat, hogy a szervezeten belül több csapatból kell lekérnie a felhasználókat, hogy az üzembe helyezés megkezdése előtt minden szükséges információra kész legyen. Ez segít a hardver gyártójával való kommunikációban az információk begyűjtése közben, mivel a döntések meghozatalához hasznos tanácsokkal rendelkezhetnek.

A szükséges információk megkeresése és összegyűjtése során előfordulhat, hogy a hálózati környezetbe be kell állítania néhány üzembe helyezés előtti konfigurációs módosítást. Ebbe beletartozhatnak a Azure Stack megoldás IP-címeinek megőrzése, az útválasztók, kapcsolók és tűzfalak konfigurálása az új Azure Stack megoldási kapcsolókhoz való kapcsolódás előkészítéséhez. Győződjön meg arról, hogy a tárgy szakembere a tervezéshez szükséges.

## <a name="capacity-planning-considerations"></a>Kapacitás-tervezési szempontok
Az Azure Stack-megoldások beszerzésére való kiértékelése során olyan hardveres konfigurációs döntéseket kell hozni, amelyek közvetlen hatással vannak a Azure Stack-megoldás általános kapacitására. Ezek közé tartozik a CPU, a memória sűrűsége, a tárolási konfiguráció és a megoldás összesített mérete (például a kiszolgálók száma) klasszikus lehetőségei. A hagyományos virtualizációs megoldástól eltérően az összetevők egyszerű aritmetikaa nem alkalmazható a felhasználható kapacitás meghatározására. Ennek első oka az, hogy Azure Stack az infrastruktúra vagy a felügyeleti összetevők a megoldáson belüli üzemeltetésére szolgál. A második ok az, hogy a megoldás kapacitása a rugalmasság támogatása mellett van fenntartva. a megoldás szoftverének frissítése oly módon, hogy a lecsökkentse a bérlői munkaterhelések megszakadását. 

A [Azure stack Capacity Planner táblázat](https://aka.ms/azstackcapacityplanner) segít a megalapozott döntések meghozatalában a kapacitás megtervezése terén: vagy a hardveres ajánlat kiválasztásával, illetve az erőforrások kombinációjára való igazítással vagy a számítási feladatok meghatározásával. A Azure Stack a futtatására szolgáló elérhető hardveres SKU-ket szeretné megtekinteni. Végezetül a táblázat a Azure Stack tervezésével és konfigurálásával kapcsolatos döntések meghozatalához nyújt útmutatást. 

A táblázat nem helyettesíti a saját vizsgálatát és elemzését.  A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot a számolótáblán belül biztosított információra vonatkozóan.



## <a name="management-considerations"></a>Felügyeleti szempontok
Azure Stack egy lezárt rendszer, ahol az infrastruktúra az engedélyektől és a hálózati perspektívától egyaránt le van zárva. A hálózati hozzáférés-vezérlési listák (ACL-ek) az összes jogosulatlan bejövő forgalom blokkolására és az infrastruktúra-összetevők közötti szükségtelen kommunikációra vonatkoznak. Ez megnehezíti a jogosulatlan felhasználók számára a rendszer elérését.

A napi felügyelet és műveletek esetében nincs korlátlan rendszergazdai hozzáférés az infrastruktúrához. Azure Stack operátoroknak a rendszergazdát a felügyeleti portálon vagy a Azure Resource Manageron keresztül kell kezelnie (a PowerShell vagy a REST API használatával). Más felügyeleti eszközök, például a Hyper-V kezelője vagy a Feladatátvevőfürt-kezelő nem fér hozzá a rendszerhez. A rendszer védelméhez a külső gyártótól származó szoftvereket (például ügynököket) nem lehet a Azure Stack infrastruktúra összetevőin belül telepíteni. A külső felügyeleti és biztonsági szoftverekkel való együttműködés a PowerShell vagy a REST API használatával történik.

Ha magasabb szintű hozzáférésre van szükség a riasztási közvetítés lépésein keresztül nem feloldott problémák elhárításához, Microsoft ügyfélszolgálata kell dolgoznia. A támogatáson keresztül egy olyan módszer áll rendelkezésre, amely lehetővé teszi, hogy az ideiglenes teljes rendszergazdai hozzáférést biztosítson a rendszerhez a fejlettebb műveletek elvégzéséhez. 

## <a name="identity-considerations"></a>Identitással kapcsolatos megfontolások

### <a name="choose-identity-provider"></a>Identitás-szolgáltató kiválasztása
Meg kell fontolnia, hogy az Azure AD-t vagy az AD FS-t Azure Stack üzembe helyezéshez milyen identitás-szolgáltatót szeretne használni. Az identitás-szolgáltatókat az üzembe helyezést követően nem lehet átváltani a teljes rendszer újratelepítése nélkül. Ha nem rendelkezik az Azure AD-fiókkal, és a felhőalapú szolgáltató által megadott fiókot használja, és ha úgy dönt, hogy átvált a szolgáltatóra, és egy másik Azure AD-fiókot használ, akkor az f megoldás újbóli üzembe helyezéséhez fel kell vennie a kapcsolatot a megoldás szolgáltatójával. vagy az Ön költségeit.

Az identitás-szolgáltató választása nem érinti a bérlői virtuális gépeket, az azonosító rendszert és az általuk használt fiókokat, függetlenül attól, hogy csatlakozhatnak-e Active Directory tartományhoz stb. Ez külön.

Az Identitáskezelő kiválasztásáról a [Azure stack integrált rendszerek kapcsolatok modelljei című cikkben](./azure-stack-connection-models.md)olvashat bővebben.

### <a name="ad-fs-and-graph-integration"></a>AD FS és gráf-integráció
Ha úgy dönt, hogy a Azure Stackt a AD FS használatával végzi el az identitás-szolgáltatóként, a AD FS-példányt integrálnia kell Azure Stack egy meglévő AD FS-példánnyal egy összevonási megbízhatósági kapcsolaton keresztül. Ez lehetővé teszi, hogy az identitások egy meglévő Active Directory erdőben hitelesítve legyenek a Azure Stack erőforrásaival.

A Graph szolgáltatást Azure Stack a meglévő Active Directory is integrálhatja. Ez lehetővé teszi a szerepköralapú Access Control (RBAC) kezelését Azure Stackokban. Az erőforrásokhoz való hozzáférés delegálásakor a Graph-összetevő az LDAP protokoll használatával megkeresi a felhasználói fiókot a meglévő Active Directory erdőben.

Az alábbi ábra az integrált AD FS és a gráf forgalmának folyamatát mutatja be.
![A AD FS és a gráf forgalmát bemutató diagram](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licencelési modell
El kell döntenie, hogy melyik licencelési modellt kívánja használni. Az elérhető lehetőségek attól függnek, hogy az internethez Azure Stack csatlakozik-e:
- A [csatlakoztatott](azure-stack-connected-deployment.md)üzemelő példányok esetében választhat az igény szerinti használat vagy a kapacitás alapú licencelés lehetőség közül. Az utólagos használathoz az Azure-hoz való kapcsolódásra van szükség, amely az Azure Commerce szolgáltatáson keresztül lesz kiszámlázva. 
- Csak a Capacity-alapú licencelés támogatott, ha az internetről [bontja a kapcsolatot](azure-stack-disconnected-deployment.md) . 

További információ a licencelési modellekről: [Microsoft Azure stack csomagolás és díjszabás](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Elnevezési döntések

Gondolja át, hogyan szeretné tervezni a Azure Stack névteret, különösen a régió nevét és a külső tartománynevet. A Azure stack nyilvános végpontok számára történő központi telepítésének külső teljes tartományneve (FQDN) &lt;a következő két név kombinációja: *régió*&gt;.&lt; *teljes tartománynév* &gt;. Például: *East.Cloud.fabrikam.com*. Ebben a példában a Azure Stack portálok a következő URL-címeken lesznek elérhetők:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> A Azure Stack központi telepítéshez kiválasztott régió nevének egyedinek kell lennie, és a portál címeiben fog megjelenni. 

A következő táblázat összefoglalja ezeket a tartománynév-elnevezési döntéseket.

| Name (Név) | Leírás | 
| -------- | ------------- | 
|Régió neve | Az első Azure Stack régió neve. Ez a név a Azure Stack által felügyelt nyilvános virtuális IP-címek (VIP) teljes tartománynevének részeként használatos. A régió neve általában egy fizikai hely azonosítója, például egy adatközpont helye.<br><br>A régió neve csak betűkből és számokból állhat, 0-9 között. Nincsenek speciális karakterek, például a "-" vagy a "#", stb. megengedettek.| 
| Külső tartomány neve | A külső virtuális IP-címekkel rendelkező végpontokhoz tartozó tartománynévrendszer (DNS) zóna neve. A nyilvános VIP-címek teljes tartománynevében használatos. | 
| Magánhálózati (belső) tartomány neve | Az infrastruktúra-kezelés Azure Stack számára létrehozott tartomány (és belső DNS-zóna) neve. 
| | |

## <a name="certificate-requirements"></a>Tanúsítványokra vonatkozó követelmények

A telepítéshez SSL (SSL) tanúsítványokat kell megadnia a nyilvános végpontokhoz. A tanúsítványok magas szinten a következő követelményekkel rendelkeznek:

- Használhat egyetlen helyettesítő karaktert, vagy használhat dedikált tanúsítványokat, és csak olyan végpontokhoz használhat helyettesítő karaktereket, mint a Storage és a Key Vault.
- A tanúsítványokat egy nyilvános megbízható hitelesítésszolgáltató (CA) vagy egy ügyfél által felügyelt HITELESÍTÉSSZOLGÁLTATÓ is kiállíthatja.

Ha további információra van szüksége arról, hogy milyen PKI-tanúsítványokra van szükség a Azure Stack telepítéséhez és a beszerzéséhez, tekintse meg a [nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelményeket Azure stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> A PKI-tanúsítvány megadott információit általános útmutatásként kell használni. A Azure Stack PKI-tanúsítványainak beszerzése előtt működjön együtt az OEM-hardveres partnerrel. Részletes tanúsítvány-útmutatást és követelményeket is biztosítanak.


## <a name="time-synchronization"></a>Idő szinkronizálása
Ki kell választania egy adott időkiszolgálót, amely a Azure Stack szinkronizálására szolgál.  Az időszinkronizálás kritikus fontosságú a Azure Stack és a hozzá tartozó infrastruktúra-szerepkörök létrehozásához, mivel ez a szolgáltatás a belső szolgáltatások egymással történő hitelesítéséhez használt Kerberos-jegyek előállítására szolgál.

Meg kell adnia az időszinkronizálási kiszolgáló IP-címét, bár az infrastruktúra legtöbb összetevője képes feloldani egy URL-címet, néhány csak az IP-címeket támogatja. Ha a leválasztott központi telepítési beállítást használja, meg kell adnia egy időkiszolgálót a vállalati hálózaton, hogy biztosan elérhető legyen a Azure Stack infrastruktúra-hálózatáról.

## <a name="connect-azure-stack-to-azure"></a>Azure Stack kapcsolódása az Azure-hoz

Hibrid Felhőbeli forgatókönyvek esetén meg kell terveznie, hogyan szeretne csatlakozni Azure Stack az Azure-hoz. Az Azure-beli virtuális hálózatok két támogatott módszerrel csatlakoztathatók Azure Stack virtuális hálózatokhoz: 
- **Helyek közötti**kapcsolat. Virtuális magánhálózati (VPN) kapcsolat IPsec (IKE v1 és IKE v2) használatával. Az ilyen típusú kapcsolathoz VPN-eszköz vagy Útválasztás és távelérés szolgáltatás (RRAS) szükséges. További információ az Azure-beli VPN-átjárókkal kapcsolatban: [about VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Az alagúton keresztüli kommunikáció titkosított és biztonságos. A sávszélességet azonban az alagút maximális átviteli sebessége korlátozza (100-200 Mbps).
- **Kimenő NAT**. Alapértelmezés szerint a Azure Stackban lévő összes virtuális gép kimenő NAT-kapcsolaton keresztül fog csatlakozni a külső hálózatokhoz. A Azure Stackban létrehozott minden egyes virtuális hálózathoz hozzá lesz rendelve egy nyilvános IP-cím. Azt határozza meg, hogy a virtuális gép közvetlenül van-e hozzárendelve nyilvános IP-cím, vagy egy nyilvános IP-címmel rendelkező terheléselosztó mögött van-e a kimenő NAT-kapcsolat a virtuális hálózat VIP-címének használatával. Ez csak a virtuális gép által kezdeményezett és külső hálózatokra (Internet vagy intranet) irányuló kommunikáció esetén működik. Nem használható a virtuális géppel kívülről való kommunikációra.

### <a name="hybrid-connectivity-options"></a>Hibrid kapcsolati beállítások

A hibrid kapcsolatok esetében fontos figyelembe venni, hogy milyen típusú központi telepítésre van szükség, és hogy hol lesz telepítve. Meg kell fontolnia, hogy el kell-e különíteni a hálózati forgalmat a bérlők számára, és hogy van-e intranetes vagy internetes üzembe helyezése.

- **Egybérlős Azure stack**. Egy Azure Stack központi telepítés, amely legalább egy hálózati szempontból úgy néz ki, mintha egy bérlő legyen. Számos bérlői előfizetés lehet, de az intranetes szolgáltatásokhoz hasonlóan minden forgalom ugyanazon hálózatokon halad át. Az egyik előfizetés hálózati forgalma ugyanazon a hálózati kapcsolaton halad át, mint egy másik előfizetéssel, és nem kell titkosított alagúton keresztül elkülöníteni.

- **Több-bérlős Azure stack**. Egy Azure Stack üzemelő példány, amelyben az egyes bérlői előfizetések olyan hálózatokra vannak kötve, amelyeknek a Azure Stack kívülről kell elkülöníteni a többi bérlő hálózati forgalmát.
 
- **Intranetes üzembe helyezés**. Egy Azure Stack központi telepítés, amely egy vállalati intraneten található, jellemzően magánhálózati IP-címtartomány és egy vagy több tűzfal mögött. A nyilvános IP-címek nem igazán nyilvánosak, mert nem irányíthatók közvetlenül a nyilvános interneten keresztül.

- Az **Internet központi telepítése**. Egy Azure Stack központi telepítés, amely a nyilvános internethez csatlakozik, és internetre irányítható nyilvános IP-címeket használ a nyilvános VIP-tartományhoz. Az üzembe helyezés továbbra is tűzfal mögött található, de a nyilvános VIP-tartomány közvetlenül elérhető a nyilvános internetről és az Azure-ból.
 
A következő táblázat összefoglalja a hibrid kapcsolati forgatókönyveket, az előnyeit, hátrányait és használati eseteit.

| Forgatókönyv | Csatlakozási módszer | Szakemberek számára | Hátrányok | Jó a következőhöz: |
| -- | -- | --| -- | --|
| Egybérlős Azure Stack, intranetes telepítés | Kimenő NAT | Jobb sávszélesség a gyorsabb átvitel érdekében. Egyszerűen megvalósítható; nincs szükség átjáróra. | A forgalom nincs titkosítva; nincs elkülönítés vagy titkosítás a veremön kívül. | Vállalati üzemelő példányok, ahol az összes bérlő egyformán megbízható.<br><br>Olyan vállalatok, amelyek rendelkeznek Azure ExpressRoute-áramkörrel az Azure-ban. |
| Több-bérlős Azure Stack, intranetes telepítés | Helyek közötti VPN | A bérlő VNet a célhelyre irányuló forgalom biztonságos. | A sávszélességet a helyek közötti VPN-alagút korlátozza.<br><br>Szükség van egy átjáróra a virtuális hálózaton és a célként megadott hálózaton lévő VPN-eszközön. | Vállalati üzemelő példányok, ahol bizonyos bérlői forgalmat más bérlők is biztonságossá kell tenniük. |
| Egybérlős Azure Stack, internetes telepítés | Kimenő NAT | Jobb sávszélesség a gyorsabb átvitel érdekében. | A forgalom nincs titkosítva; nincs elkülönítés vagy titkosítás a veremön kívül. | Üzemeltetési forgatókönyvek, ahol a bérlő saját Azure Stack üzembe helyezést és dedikált áramkört kap a Azure Stack-környezethez. Például: ExpressRoute és többprotokollos felirat váltás (MPLS).
| Több-bérlős Azure Stack, internetes telepítés | Helyek közötti VPN | A bérlő VNet a célhelyre irányuló forgalom biztonságos. | A sávszélességet a helyek közötti VPN-alagút korlátozza.<br><br>Szükség van egy átjáróra a virtuális hálózaton és a célként megadott hálózaton lévő VPN-eszközön. | Üzemeltetési forgatókönyvek, ahol a szolgáltató több-bérlős felhőt szeretne nyújtani, ahol a bérlők nem bíznak egymással, és a forgalmat titkosítani kell.
|  |  |  |  |  |

### <a name="using-expressroute"></a>A ExpressRoute használata

Az egybérlős intranetes és több-bérlős forgatókönyvek esetében a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) -on keresztül is csatlakozhat Azure stack az Azure-hoz. Szüksége lesz egy kiépített ExpressRoute-áramkörre [egy kapcsolati szolgáltatón](https://docs.microsoft.com/azure/expressroute/expressroute-locations)keresztül.

Az alábbi ábrán egy egybérlős forgatókönyv ExpressRoute látható (ahol az "ügyfél kapcsolata" a ExpressRoute áramkör).

![Az egybérlős ExpressRoute forgatókönyvét bemutató ábra](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

A következő ábra a több-bérlős forgatókönyvek ExpressRoute mutatja be.

![Több-bérlős ExpressRoute forgatókönyvet bemutató diagram](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Külső figyelése
Ha egyetlen nézetet szeretne kapni az Azure Stack-telepítésből és-eszközökből származó összes riasztásról, valamint a riasztások meglévő IT-szolgáltatásmenedzsment munkafolyamataiba való integrálásáról, akkor a [külső adatközpont-figyelési megoldásokkal integrálhatja Azure stack](azure-stack-integrate-monitor.md).

A Azure Stack megoldás részeként a hardver életciklus-állomása olyan, Azure Stack kívüli számítógép, amely az OEM gyártó által biztosított hardveres felügyeleti eszközöket futtatja. Ezeket az eszközöket vagy egyéb megoldásokat használhatja, amelyek közvetlenül integrálhatók az adatközpontban meglévő figyelési megoldásokkal.

Az alábbi táblázat összefoglalja a jelenleg elérhető lehetőségek listáját.

| Terület | Külső figyelési megoldás |
| -- | -- |
| Szoftver Azure Stack | [Operations Manager Azure Stack felügyeleti csomagja](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios beépülő modul](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-alapú API-hívások | 
| Fizikai kiszolgálók (bmc-n keresztül IPMI) | OEM hardver – Operations Manager gyártói felügyeleti csomag<br>OEM hardveres gyártó által biztosított megoldás<br>Hardveres szolgáltatói Nagios beépülő modulok.<br>OEM partner által támogatott figyelési megoldás (tartozék) | 
| Hálózati eszközök (SNMP) | Operations Manager hálózati eszközök felderítése<br>OEM hardveres gyártó által biztosított megoldás<br>A Nagios kapcsoló beépülő modulja |
| Bérlői előfizetés állapotának figyelése | [System Center felügyeleti csomag a Windows Azure-hoz](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vegye figyelembe a következő követelményeket:
- A használt megoldásnak ügynök nélkül kell lennie. Azure Stack-összetevőkön belül nem telepíthet harmadik féltől származó ügynököket. 
- Ha System Center Operations Managert szeretne használni, Operations Manager 2012 R2 vagy Operations Manager 2016 szükséges.

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

A biztonsági mentés és a vész-helyreállítás tervezése a IaaS virtuális gépeket és a Pásti-szolgáltatásokat üzemeltető mögöttes Azure Stack infrastruktúrát, valamint a bérlői alkalmazások és az adatmennyiségek tervezését is magában foglalja. Ezeket külön kell megterveznie.

### <a name="protect-infrastructure-components"></a>Infrastruktúra-összetevők biztosítása

Azure Stack infrastruktúra-összetevők [biztonsági mentését](azure-stack-backup-back-up-azure-stack.md) egy Ön által megadott SMB-megosztáson végezheti el:

- Külső SMB-fájlmegosztás szükséges egy meglévő Windows-alapú fájlkiszolgálón vagy harmadik féltől származó eszközön.
- Ugyanezt a megosztást kell használnia a hálózati kapcsolók és a hardveres életciklus-gazdagép biztonsági mentéséhez. Az OEM-hardvergyártók segítséget nyújtanak az összetevők biztonsági mentéséhez és visszaállításához, mivel ezek kívülről Azure Stack. Ön felelős a biztonsági mentési munkafolyamatok futtatásához az OEM-gyártó javaslata alapján.

Ha katasztrofális adatvesztés történik, az infrastruktúra biztonsági mentésével újratervezheti az üzembe helyezési adatokat, például a központi telepítési bemeneteket és az azonosítókat, a szolgáltatásfiókok, a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány, az összevont erőforrások (leválasztott központi telepítések), a csomagok, ajánlatok, előfizetések, kvóták, RBAC szabályzatok és szerepkör-hozzárendelések, valamint Key Vault titkos kódok.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Bérlői alkalmazások védetté IaaS virtuális gépeken

Azure Stack nem készít biztonsági másolatot a bérlői alkalmazásokról és az azokról. Meg kell terveznie a biztonsági mentést és a vész-helyreállítási védelmet a Azure Stackon kívüli célhoz. A bérlői védelem bérlő által vezérelt tevékenység. A IaaS Virtual Machines esetében a bérlők a vendégen belüli technológiákat használhatják a mappák, az alkalmazásadatok és a rendszerállapotok védelme érdekében. Vállalati vagy szolgáltatóként azonban előfordulhat, hogy egy biztonsági mentési és helyreállítási megoldást kíván használni ugyanabban az adatközpontban vagy külsőleg egy felhőben.

A Linux vagy a Windows IaaS rendszerű virtuális gépek biztonsági mentéséhez olyan biztonsági mentési termékeket kell használnia, amelyeknek hozzáférése van a vendég operációs rendszerhez a fájl, mappa, operációs rendszer állapot és alkalmazásadatok védelme érdekében. Használhatja a Azure Backup, a System Center adatközpont-védelmi kezelőjét vagy a támogatott harmadik féltől származó termékeket.

Ha egy másodlagos helyre szeretné replikálni az alkalmazásokat, és vészhelyzet esetén az alkalmazás feladatátvételét koordinálja, használhat Azure Site Recovery vagy támogatott harmadik féltől származó termékeket. Emellett a natív replikálást támogató alkalmazások, például a Microsoft SQL Server is képesek replikálni az adatforrást egy másik helyre, ahol az alkalmazás fut.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

- További információ a használati esetekről, a vásárlásról, a partnerekről és az OEM-hardvergyártók használatáról: [Azure stack](https://azure.microsoft.com/overview/azure-stack/) termék oldal.
- Az Azure Stack integrált rendszerek ütemtervével és földrajzi elérhetőségével kapcsolatos információkért tekintse meg a tanulmányt: [Azure Stack: Az Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)kiterjesztése. 

## <a name="next-steps"></a>További lépések
[Azure Stack üzemelő példányainak kapcsolati modelljei](azure-stack-connection-models.md)
