---
title: A moduláris adatközpont (MDC) hálózatkezelésének bemutatása
description: Ismerje meg az Azure Modular Datacenter-eszköz hálózatkezelését.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/31/2019
ms.lastreviewed: 12/31/2019
ms.openlocfilehash: 628e1faf5a5bbfcf0bc780ae770dbdc8a8d762f2
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674574"
---
# <a name="modular-datacenter-mdc-network-introduction"></a>A moduláris adatközpont (MDC) hálózati bemutatása

## <a name="package-content"></a>Csomag tartalma

Az Azure Modular Datacenter-(MDC-) megoldás az ebben a cikkben hivatkozott további hálózati berendezésekkel rendelkezik. Ez a berendezés a tároló hálózathoz való összekapcsolására szolgál.

Erősítse meg, hogy a rendszer megfelel-e a környezetének.

| Mennyiség | Típus | Modellezés |
|-----|---------------|-------|
| 12 | 12x QSFP – 40G | SR |
| 12 | 12x SFP + 10 GB | SR |
| 12 | 12x SFP 1G | SR |
| 12 | 12x QSFP – 40G | LR |
| 12 | 12x SFP + 10 GB | LR |
| 12 | 12x SFP 1G | LR |

## <a name="network-design-overview"></a>A hálózati tervezés áttekintése

### <a name="physical-network-design"></a>Fizikai hálózat kialakítása

A MDC-megoldás rugalmas és magasan elérhető fizikai infrastruktúrát igényel a működésének és szolgáltatásainak támogatásához. A Top-of-rack (TOR) és a Border kapcsolók közötti kapcsolatokra csak SFP + vagy SFP28, illetve 1 GB, 10 GB vagy 40 GB sebességre van korlátozva.

A következő ábra a MDC ajánlott kialakítását mutatja be.

![Az ajánlott fizikai hálózati kialakítást bemutató diagram.](media/network-introduction/physical-network-design.png)

### <a name="logical-network-design"></a>Logikai hálózat kialakítása

A logikai hálózat kialakítása a fizikai hálózati infrastruktúra absztrakcióját jelöli. Ezek a gazdagépek, virtuális gépek és szolgáltatások hálózati hozzárendeléseinek rendszerezésére és leegyszerűsítésére szolgálnak. A logikai hálózat létrehozásának részeként a hálózati telephelyek jönnek létre a következők meghatározásához:

- Virtuális helyi hálózatok (VLAN-ok).
- IP-alhálózatok.
- IP-alhálózat/VLAN párok.

Ezen VLAN-ok és alhálózatok mindegyike a logikai hálózathoz van társítva minden egyes fizikai helyen.

A következő táblázat azokat a logikai hálózatokat és IPv4-alhálózati tartományokat tartalmazza, amelyeket meg kell terveznie.

| **Logikai hálózat** | **Leírás** | **Méret** |
|---------------------|-----------------|----------|
| Nyilvános virtuális IP-cím (VIP) | A MDC a hálózatból összesen 31 címet használ. A MDC-szolgáltatások kis készletéhez nyolc nyilvános IP-cím használatos, a többi pedig a bérlői virtuális gépek által használt. Ha azt tervezi, hogy Azure App Service és az SQL Resource providert is használja, hét további címet használ a rendszer. A fennmaradó 15 IP-cím a jövőbeli Azure-szolgáltatások számára van fenntartva. | /26 (62 gazdagép) –/22 (1022 gazdagép) <br><Br>Ajánlott =/24 (254 gazdagép) |
| Infrastruktúra váltása | Pont-pont típusú IP-címek útválasztási célokra, dedikált kapcsoló felügyeleti felületek és a kapcsolóhoz rendelt visszacsatolási címek. | /26 |
| Infrastruktúra | A belső MDC való kommunikációhoz használatos. | /24 |
| Személyes | A Storage Network, a privát VIP-címek, az infrastruktúra-tárolók és egyéb belső függvények esetében használatos. | /20 |
| Alaplapi felügyeleti vezérlő (BMC) | A fizikai gazdagépeken található bmc való kommunikációhoz használatos. | /26 |
| Isilon | A Isilon-tárolóval való kommunikációra szolgál. | 1x/25 TOR 1x/25 BMC (felügyelet) |

### <a name="network-infrastructure"></a>Hálózati infrastruktúra

A MDC hálózati infrastruktúrája több, a kapcsolókon konfigurált logikai hálózatból áll. Az alábbi ábrán ezeket a logikai hálózatokat láthatja, valamint azt, hogyan integrálható a Top-of-rack (TOR), a BMC és a Border (Customer Network) kapcsolók.

![A logikai hálózat kialakítását bemutató diagram.](media/network-introduction/logical-network-design.png)

#### <a name="bmc-network"></a>BMC-hálózat

Ez a hálózat dedikált az összes bmc (más néven szolgáltatás processzor) a felügyeleti hálózathoz való csatlakoztatására. Ilyenek például a iDRAC, az iLO és a iBMC. A BMC-csomópontokkal való kommunikációhoz csak egy BMC-fiók használható. Ha van ilyen, a hardver életciklus-állomása (HLH) ezen a hálózaton található, és OEM-specifikus szoftvereket biztosíthat a hardveres karbantartáshoz vagy monitorozáshoz.

A HLH az üzembe helyezési virtuális gépet (DVM) is üzemelteti. A DVM a MDC telepítése során használatos, és az üzembe helyezés befejezésekor törlődik. A DVM használatához internetkapcsolat szükséges a csatlakoztatott üzembe helyezési forgatókönyvekben több összetevő teszteléséhez, ellenőrzéséhez és eléréséhez. Ezek az összetevők a vállalati hálózaton belül és kívül is lehetnek. Ilyenek például az NTP, a tartománynévrendszer (DNS) és az Azure. További információ a kapcsolati követelményekről: a MDC tűzfal-integráció hálózati címfordítás (NAT) szakasza.

#### <a name="private-network"></a>Magánhálózat

A/20 (4096-es gazda IP-címek) hálózat a MDC régióhoz van privát. Nem terjed ki a MDC régió szegély-kapcsoló eszközeire. Ez a hálózat több alhálózatra oszlik, például:

- **Storage Network**: a/25 (128 IP-cím) hálózat, amely a közvetlen tárolóhelyek és a kiszolgáló-üzenetblokk (SMB) tárolási forgalmának és a virtuális gépek élő áttelepítésének támogatásához használatos.
- **Belső virtuális IP-hálózat**: a szoftveres terheléselosztó számára csak belső VIP-címekre dedikált/25 hálózat.
- **Container Network**: a/23 (512 IP) hálózat, amely az infrastruktúra-szolgáltatásokat futtató tárolók közötti belső forgalomra van kijelölve.

A magánhálózat mérete megváltozott:/20 (4096 IP) magánhálózati IP-címmel. Ez a hálózat a MDC rendszer számára privát. Nem halad a MDC rendszer szegély-kapcsoló eszközein, és több MDC rendszeren is felhasználható. Amíg a hálózat MDC van, nem fedi át az adatközpont más hálózatait. A magánhálózati IP-címekre vonatkozó útmutatásért javasoljuk, hogy kövesse a 1918-es RFC-dokumentumot.

A/20 magánhálózati IP-terület több hálózatra oszlik, amelyek lehetővé teszik, hogy a MDC rendszerinfrastruktúra a későbbi kiadásokban is fusson a tárolókban. További információ: 1910 kibocsátási megjegyzések. Ez az új magánhálózati IP-terület lehetővé teszi, hogy a telepítés előtt csökkentse a szükséges irányítható IP-területet.

#### <a name="mdc-infrastructure-network"></a>MDC infrastruktúra-hálózat

A/24 hálózat a belső MDC-összetevőkre van kijelölve, így egymás között kommunikálhatnak és cserélhetik az adatcserét. Ez az alhálózat a MDC-megoldás külső adatközpontba irányítható. *Nem* javasoljuk nyilvános vagy internetes ÚTVÁLASZTÁSÚ IP-címek használatát ezen az alhálózaton. Ezt a hálózatot a rendszer meghirdeti a szegélynek, de a legtöbb IP-cím hozzáférés-vezérlési listával védett. A hozzáférésre jogosult IP-címek kis hatótávolságon belül vannak, a mérete pedig egy/27 hálózat. Az IP-címeket futtató szolgáltatások, például az emelt szintű végpont (PEP) és a MDC biztonsági másolat.

#### <a name="public-vip-network"></a>Nyilvános VIP-hálózat

A nyilvános VIP-hálózat a MDC hálózati vezérlőjéhez van rendelve. Nem logikai hálózat a kapcsolón. A SLB a címek készletét használja, és hozzárendeli/32 hálózatot a bérlői munkaterhelésekhez. A Switch útválasztási táblázatban ezek/32 IP-címek elérhető útvonalként lesznek meghirdetve a Border Gateway Protocol (BGP) használatával. Ez a hálózat nyilvános címeket tartalmaz, amelyek külsőleg elérhetők. A MDC-infrastruktúra megtartja a nyilvános VIP-hálózat első 31 címét, míg a fennmaradóket a bérlői virtuális gépek használják. Az alhálózat hálózati mérete legfeljebb/26 (64 gazdagép) lehet legfeljebb/22 (1 022 gazdagép) értékre. Javasoljuk, hogy tervezzen egy/24 hálózatot.

#### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása

A/26 hálózat az az alhálózat, amely a pont-pont típusú IP/30 (két gazdagép IP) alhálózatot és a visszacsatolásokat tartalmazza. Ezek az alhálózatok dedikált/32 alhálózatok a sávon belüli kapcsolók felügyeletéhez és a BGP-útválasztó AZONOSÍTÓhoz. Ezt az IP-címtartományt a MDC-megoldáson kívül kell irányítani az adatközponthoz. Lehet, hogy az IP-címek magán vagy nyilvánosak.

#### <a name="switch-management-network"></a>Váltás a felügyeleti hálózatra

A/29 (Six Host IP-címek) hálózat dedikált a kapcsolók felügyeleti portjainak csatlakoztatására. Ez a hálózat sávon kívüli hozzáférést tesz lehetővé az üzembe helyezéshez, a felügyelethez és a hibaelhárításhoz. Az előző szakaszban említett kapcsoló infrastruktúra-hálózat alapján számítjuk ki.

#### <a name="isilon-network"></a>Isilon hálózat

Két/25 hálózat van. Az egyik él a TOR kapcsolón, és egy/25 használatban van a BMC-kapcsolón a felügyelethez.

## <a name="dns-design-overview"></a>A DNS-tervezés áttekintése

Ha a MDC-végpontokat (*portál*, *adminportal*, *Management* és *adminmanagement*) szeretné elérni a MDC kívülről, integrálnia kell a MDC DNS-szolgáltatásait azokkal a DNS-KISZOLGÁLÓkkal, amelyek a MDC használni kívánt DNS-zónákat futtatják.

### <a name="mdc-dns-namespace"></a>MDC DNS-névtér

A MDC-megoldás telepítésekor a DNS-sel kapcsolatos fontos információkat kell megadnia.

| **Mező** | **Leírás** | **Példa** |
|-----------|-----------------|-------------|
| Régió | A MDC központi telepítésének földrajzi helye. | *Kelet* |
| Külső tartomány neve | A MDC-telepítéshez használni kívánt zóna neve. | *cloud.fabrikam.com* |
| Belső tartomány neve | A MDC az infrastruktúra-szolgáltatásokhoz használt belső zóna neve. Ez a címtárszolgáltatások integrált és magán (nem érhető el a MDC-telepítésen kívülről). | *azurestack. local* |
| DNS-továbbítók | A MDC-en kívül futtatott DNS-lekérdezések, DNS-zónák és rekordok továbbítására szolgáló DNS-kiszolgálók akár a vállalati intraneten, akár a nyilvános interneten. A DNS-továbbító értékét a telepítés után a **set-AzSDnsForwarder** parancsmaggal módosíthatja. | |
| Elnevezési előtag (nem kötelező) | Az a névadási előtag, amelynek a MDC-infrastruktúra szerepkör-példányainak a neveit meg szeretné jeleníteni. Ha nincs megadva, az alapértelmezett érték a *AZS*. | *AZS* |

A MDC-telepítés és a végpontok teljes tartományneve (FQDN) a régió paraméter és a külső tartománynév paraméter kombinációja. Az előző táblázatban szereplő példák értékeit használva a MDC központi telepítésének teljes tartományneve *East.Cloud.fabrikam.com* lesz.

A központi telepítés néhány végpontjának példája a következő URL-címekhez hasonlít:

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Ha ezt a példát DNS-névteret szeretné használni egy MDC-telepítéshez, a következő feltételek szükségesek:

- A zóna fabrikam.com regisztrálva van egy tartományregisztráló, belső vállalati DNS-kiszolgáló vagy mindkettő. A regisztráció a névfeloldási követelményektől függ.
- A gyermektartomány cloud.fabrikam.com a zóna fabrikam.com alatt található.
- A fabrikam.com és cloud.fabrikam.com zónákat üzemeltető DNS-kiszolgálók a MDC-telepítésből is elérhetők.

A MDC-végpontok és a MDC kívüli példányok DNS-neveinek feloldásához integrálnia kell a DNS-kiszolgálókat. A MDC külső DNS-zónáját futtató kiszolgálók belefoglalása a használni kívánt fölérendelt zónát futtató DNS-kiszolgálókkal.

#### <a name="dns-name-labels"></a>DNS-nevek címkéi

A MDC támogatja a DNS-név címke nyilvános IP-címhez való hozzáadását, hogy engedélyezze a névfeloldást a nyilvános IP-címek számára. A DNS-címkék kényelmes módszert biztosítanak a felhasználók számára a MDC által üzemeltetett alkalmazások és szolgáltatások elérésére a név alapján. A DNS-név címkéje némileg eltérő névteret használ, mint az infrastruktúra-végpontok. Az előző példában szereplő névteret követően a DNS-címkék névtere a következő lenne: *\* . East.cloudapp.Cloud.fabrikam.com*.

Ha egy bérlő egy nyilvános IP-cím erőforrás DNS-név címkéjének **SajátPr** határozza meg, akkor egy rekordot hoz létre a SajátPr számára a MDC külső DNS-kiszolgáló *East.cloudapp.Cloud.fabrikam.com* . Az eredményül kapott teljes tartománynév *MyApp.East.cloudapp.Cloud.fabrikam.com* lesz.

Ha szeretné kihasználni ezt a funkciót, és ezt a névteret használja, integrálnia kell a DNS-kiszolgálókat. Vegyen fel olyan kiszolgálókat, amelyek a MDC külső DNS-zónáját és a használni kívánt szülő zónát üzemeltető DNS-kiszolgálókat futtatják. Ez a névtér eltér a MDC szolgáltatási végpontok által használttól, ezért létre kell hoznia egy további delegálási vagy feltételes továbbítási szabályt.

A DNS-név feliratának működéséről a MDC dokumentációjának "DNS használata" című részében olvashat bővebben.

### <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- A mérvadó DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A rekurzív DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

A MDC mind a mérvadó, mind a rekurzív DNS-kiszolgálókat tartalmazza. A rekurzív kiszolgálók a belső privát zóna és a MDC-telepítés külső nyilvános DNS-zónája kivételével az összes név feloldására szolgálnak.

### <a name="resolve-external-dns-names-from-mdc"></a>Külső DNS-nevek feloldása a MDC

A MDC (például www.bing.com) kívüli végpontok DNS-neveinek feloldásához meg kell adnia a DNS-kiszolgálókat a MDC számára a DNS-kérések továbbításához, amelyekhez a MDC nem mérvadó. Azok a DNS-kiszolgálók, amelyek a MDC továbbítják a kérelmeket a telepítési munkalapon (a DNS-továbbító mezőben). A hibatűréshez legalább két kiszolgálót adjon meg ebben a mezőben. Ezen értékek nélkül a MDC üzembe helyezése sikertelen lesz. A DNS-továbbító értékeit a telepítés után a **set-AzSDnsForwarder** parancsmaggal módosíthatja.

## <a name="firewall-design-overview"></a>A tűzfal kialakításának áttekintése

Javasoljuk, hogy a MDC biztonsága érdekében használjon egy tűzfal eszközt. A tűzfalak segíthetnek megvédeni az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Az Azure Storage-szolgáltatások, például a Blobok, a táblák és a várólisták esetében az átviteli sebesség szűk keresztmetszete is lehetnek.

Ha a rendszer leválasztott központi telepítési módot használ, közzé kell tennie a Active Directory összevonási szolgáltatások (AD FS) végpontot. További információ: Datacenter Integration Identity (adatközpont-integrációs identitás) című cikk.

A Azure Resource Manager (rendszergazda), a felügyeleti portál és a Azure Key Vault (rendszergazda) végpontok nem feltétlenül igényelnek külső közzétételt. Például szolgáltatóként korlátozhatja a támadási felületet úgy, hogy csak a MDC felügyeli a hálózaton belülről, és nem az internetről.

A vállalati szervezetek esetében a külső hálózat lehet a meglévő vállalati hálózat. Ebben a forgatókönyvben a végpontokat közzé kell tenni a MDC a vállalati hálózatról való üzemeltetéséhez.

### <a name="network-address-translation"></a>Hálózati címfordítás

Javasoljuk, hogy a NAT-módszer lehetővé tegye a DVM számára a külső erőforrások elérését az üzembe helyezés során. A regisztráció és a hibaelhárítás során javasoljuk továbbá a ERCS-alapú virtuális gépek és a PEP NAT használatát is.

A NAT a külső hálózat vagy a nyilvános VIP-címek nyilvános IP-címeinek alternatívája is lehet. Ez a beállítás nem ajánlott, mert korlátozza a bérlői felhasználói élményt, és növeli a bonyolultságot. Az egyik lehetőség az egy-az-egyhez NAT, amelyhez felhasználói IP-címenként még egy nyilvános IP-címet kell megadni a készleten. Egy másik lehetőség egy több-az-egyhez NAT, amelyhez felhasználónként egy virtuális IP-cím szükséges NAT-szabályt használ a felhasználók által használt összes porthoz.

A NAT nyilvános VIP-hez való használatának néhány hátránya a következő:

- A tűzfalszabályok kezelése során felhasználható terhelés, mivel a felhasználók a saját végpontokat és a szoftveresen definiált hálózati veremben lévő közzétételi szabályokat vezérlik. A felhasználóknak kapcsolatba kell lépniük a MDC-kezelővel a virtuális IP-címek közzétételéhez és a portok listájának frissítéséhez.
- Míg a NAT-használat korlátozza a felhasználói élményt, teljes hozzáférést biztosít az operátornak a közzétételi kérelmekhez.
- Az Azure-ban használt hibrid felhőalapú forgatókönyvek esetében vegye figyelembe, hogy az Azure nem támogatja VPN-alagút beállítását a NAT-t használó végpontok számára.

### <a name="ssl-interception"></a>SSL-lehallgatás

Jelenleg azt javasoljuk, hogy tiltsa le az SSL-elfogást (például a visszafejtés kiszervezését) az összes MDC-forgalomon. Ha a jövőbeli frissítések támogatják, útmutatást nyújt a MDC SSL-elfogásának engedélyezéséhez.

### <a name="edge-deployment-firewall-scenario"></a>Peremhálózati telepítési tűzfal forgatókönyve

Az Edge-telepítésekben a MDC közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött helyezhető üzembe. Ezekben a forgatókönyvekben a tűzfal a szegély felett (1. forgatókönyv) nagyobb, mint az aktív-aktív és az aktív-passzív tűzfal konfigurációjának támogatása. A szegély eszközként is működhet (2. forgatókönyv), ahol csak az aktív-aktív tűzfal-konfigurációt támogatja. A 2. forgatókönyv a BGP vagy a statikus útválasztás a feladatátvételhez szolgáltatásra támaszkodik.

Nyilvános IP-címek a nyilvános VIP-készlethez a külső hálózatról üzembe helyezéskor vannak megadva. Biztonsági okokból a nyilvános útválasztású IP-címek *nem* ajánlottak egy Edge-forgatókönyvben más hálózatokon. Ez a forgatókönyv lehetővé teszi a felhasználók számára, hogy a teljes mértékben felügyelt Felhőbeli élményt a nyilvános felhőben, például az Azure-ban is megtapasztalják.

 ![Diagram, amely megjeleníti az MDC Edge-tűzfalak forgatókönyveit.](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve

A vállalati intranet vagy peremhálózati környezetekben a MDC többzónás tűzfalon, illetve a peremhálózati tűzfal és a belső vállalati hálózati tűzfal között helyezhető üzembe. Ezután a rendszer a biztonságos, peremhálózati (vagy DMZ) és nem biztonságos zónák között osztja el a forgalmat, az alábbiak szerint:

- **Biztonságos zóna**: az a belső hálózat, amely belső vagy vállalati ÚTVÁLASZTÁSÚ IP-címeket használ. A biztonságos hálózat osztható. Az internetről kimenő hozzáférés a tűzfal NAT-on keresztül is elérhető. Általában a belső hálózaton keresztül érhető el az adatközponton belül. Minden MDC-hálózatnak a biztonságos zónában kell lennie, kivéve a külső hálózat nyilvános VIP-készletét.
- **Peremhálózati zóna**: a peremhálózaton a külső vagy internetes alkalmazások, például a webkiszolgálók jellemzően üzembe helyezhetők. Általában egy tűzfal figyeli, hogy elkerülje a támadásokat, például a DDoS és a behatolás (hacking) szolgáltatást, miközben továbbra is engedélyezi a megadott bejövő forgalmat az internetről. Csak a külső hálózat nyilvános VIP-készlete MDC kell lennie a DMZ-zónában.
- Nem **biztonságos zóna**: a külső hálózat, az Internet. A MDC telepítése nem ajánlott a *nem* biztonságos zónában.

![A peremhálózaton lévő hálózati tűzfal forgatókönyvét bemutató diagram.](media/network-introduction/perimeter-network-firewall-scenario-50.png)

## <a name="vpn-design-overview"></a>A VPN-tervezés áttekintése

Bár a VPN felhasználói fogalom, néhány fontos szempontot figyelembe kell vennie, hogy a megoldás tulajdonosának és az operátornak ismernie kell.

Mielőtt hálózati forgalmat küldene az Azure-beli virtuális hálózat és a helyszíni hely között, létre kell hoznia egy virtuális hálózati (VPN-) átjárót a virtuális hálózathoz.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. A VPN-átjárók használatával biztonságos forgalmat küldhet a MDC virtuális hálózata és az Azure-beli virtuális hálózat között. A virtuális hálózat és egy másik, VPN-eszközhöz csatlakozó hálózat között is biztonságosan küldhet forgalmat.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. A MDC a virtuális hálózati átjárók egy típusát támogatja: a VPN-típust.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen típusú beállítás például egy többhelyes kapcsolat konfigurálása.

A MDC VPN-átjárók létrehozása és konfigurálása előtt tekintse át a MDC hálózatkezelésével kapcsolatos szempontokat. Megtudhatja, hogyan különböznek a MDC-konfigurációk az Azure-tól.

Az Azure-ban a kiválasztott VPN Gateway SKU sávszélességének átviteli sebességét az átjáróhoz csatlakozó összes kapcsolaton át kell osztani. A MDC azonban a VPN Gateway SKU sávszélesség-értékét a rendszer az átjáróhoz csatlakozó összes kapcsolati erőforrásra alkalmazza. Például:

- Az Azure-ban az alapszintű VPN Gateway SKU körülbelül 100 Mbps összesített adatátviteli kapacitást tud kielégíteni. Ha két kapcsolatot hoz létre a VPN-átjáróhoz, és az egyik kapcsolat 50 Mbps sávszélességet használ, akkor az 50 Mbps elérhető lesz a másik kapcsolat számára.
- A MDC-ben az alapszintű VPN Gateway-alapú SKU-hoz való minden kapcsolat 100 Mbps átviteli sebességgel van lefoglalva.

### <a name="vpn-types"></a>VPN-típusok

Amikor létrehoz egy VPN Gateway-konfigurációhoz tartozó virtuális hálózati átjárót, meg kell adnia egy VPN-típust. A választott VPN-típus a létrehozni kívánt kapcsolati topológiától függ. A VPN-típus az Ön által használt hardvertől is függhet. A helyek közötti konfigurációkhoz VPN-eszköz szükséges. Egyes VPN-eszközök csak bizonyos VPN-típusokat támogatnak.

>[!IMPORTANT]
> A MDC jelenleg csak az Útválasztás-alapú VPN-típust támogatja. Ha az eszköz csak a házirend-alapú VPN-eket támogatja, a MDC nem támogatottak azok az eszközökhöz való kapcsolatok. A MDC emellett jelenleg nem támogatja a házirend-alapú forgalmi választókat az útvonal-alapú átjárók esetében, mivel az Egyéni IPsec/IKE-házirend konfigurációja nem támogatott.

- **Házirendalapú**: a házirend-alapú VPN-ek az IPSec-házirendek alapján titkosítják és irányítják a csomagokat az IPSec-alagutakon keresztül. A házirendek a helyszíni hálózat és a MDC virtuális hálózat között a címek előtagjainak kombinációjával vannak konfigurálva. A házirend vagy a forgalmi választó általában egy hozzáférési lista a VPN-eszköz konfigurációjában. A **házirendalapú** az Azure-ban, de nem a MDC-ben támogatott.
- **Útvonalalapú**: az Útválasztás-alapú VPN-ek az IP-továbbítás vagy az útválasztási táblázatban konfigurált útvonalakat használják. Az útvonalak a közvetlen csomagokat a megfelelő bújtatási interfészekhez irányítják. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A **útvonalalapú** VPN-EK házirendje vagy forgalmi választója bármilyen módon (vagy helyettesítő karakterekkel) van konfigurálva. Alapértelmezés szerint nem módosíthatók. A **útvonalalapú** VPN-típus értéke **útvonalalapú**.

### <a name="configure-a-vpn-gateway"></a>VPN-átjáró konfigurálása

A VPN Gateway-kapcsolatok számos, adott beállításokkal konfigurált erőforrásra támaszkodnak. Ezen erőforrások többsége külön konfigurálható, de bizonyos esetekben ezeket egy adott sorrendben kell konfigurálni.

#### <a name="settings"></a>Beállítások

Az egyes erőforrásokhoz kiválasztott beállítások kritikus fontosságúak a sikeres kapcsolatok létrehozásához.

Ez a cikk segítséget nyújt a következők megértésében:

- Az átjáró típusainak, a VPN-típusoknak és a kapcsolati típusoknak.
- Az átjáró-alhálózatokat, a helyi hálózati átjárókat és az egyéb erőforrás-beállításokat, amelyeket érdemes figyelembe venni.

#### <a name="connection-topology-diagrams"></a>Kapcsolati topológia-diagramok

A VPN Gateway-kapcsolatokhoz különböző konfigurációk érhetők el. Döntse el, melyik konfiguráció felel meg legjobban az igényeinek. A következő szakaszban megtekintheti az alábbi VPN Gateway-kapcsolatokkal kapcsolatos információkat és topológiai diagramokat:

- Elérhető üzemi modell
- Elérhető konfigurációs eszközök
- Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

A következő szakaszban található diagramok és leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolódási topológiát. A diagramok a fő alapkonfiguráció-topológiákat mutatják, de a diagramok útmutatóként való használatával összetettebb konfigurációk is létrehozhatók.

#### <a name="site-to-site-and-multisite-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

##### <a name="site-to-site"></a>Helyek közötti kapcsolat

A helyek közötti VPN Gateway-kapcsolat egy IPsec/IKE (IKEv2) VPN-alagúton keresztüli kapcsolat. Az ilyen típusú kapcsolathoz olyan VPN-eszköz szükséges, amely a helyszínen található, és egy nyilvános IP-címet kap. Ez az eszköz nem helyezhető el NAT mögött. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

##### <a name="multisite"></a>Többhelyes

A többhelyes kapcsolat a helyek közötti kapcsolat egy változata. Több VPN-kapcsolatot hoz létre a virtuális hálózati átjáróról, és általában több helyszíni helyhez is csatlakozhat. Ha több kapcsolattal dolgozik, egy Route-alapú VPN-típust (más néven dinamikus átjárót) kell használnia a klasszikus virtuális hálózatok használata esetén. Mivel minden egyes virtuális hálózatnak csak egy VPN-átjárója lehet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen.

### <a name="gateway-skus"></a>Átjáró-termékváltozatok

A MDC virtuális hálózati átjárójának létrehozásakor meg kell adnia a használni kívánt átjáró SKU-t. A következő VPN Gateway SKU-ket támogatja:

- Alapszintű
- Standard
- Nagy teljesítmény

Egy magasabb szintű átjáró kiválasztásával több processzor és hálózati sávszélesség is kiosztható az átjáróra. Ennek eredményeképpen az átjáró képes támogatni a virtuális hálózat nagyobb hálózati átviteli sebességét.

A MDC nem támogatja az ultra Performance Gateway SKU-t, amelyet kizárólag az Azure ExpressRoute használ. Az SKU kiválasztásakor vegye figyelembe a következő szempontokat:

- A MDC nem támogatja a házirend-alapú átjárókat.
- A BGP nem támogatott az alapszintű SKU-ban.
- ExpressRoute – a VPN-átjárók egyidejű konfigurációi nem támogatottak a MDC.

#### <a name="gateway-availability"></a>Átjáró rendelkezésre állása

A magas rendelkezésre állási forgatókönyvek csak az High-Performance Gateway-alapú kapcsolatok SKU-ban konfigurálhatók. Az Azure-val ellentétben, amely az aktív/aktív és az aktív/passzív konfigurációkon keresztül biztosítja a rendelkezésre állást, a MDC csak az aktív/passzív konfigurációt támogatja.

#### <a name="failover"></a>Feladatátvétel

Három több-bérlős átjáró infrastruktúra-alapú virtuális gép található a MDC-ben. Két virtuális gép aktív módban van. A harmadik virtuális gép redundáns üzemmódban van. Az aktív virtuális gépek lehetővé teszik a VPN-kapcsolatok létrehozását. A redundáns virtuális gép csak akkor fogad VPN-kapcsolatokat, ha feladatátvétel történik. Ha egy aktív átjárót használó virtuális gép elérhetetlenné válik, a VPN-kapcsolat a kapcsolat elvesztése után rövid idő (néhány másodperc) elteltével átadja a virtuális gép feladatátvételét.

### <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

Az alábbi táblázatban az átjárótípusok és azok becsült összesített átviteli sebessége tekinthető meg az átjárók termékváltozata alapján.

| Átjáró típusa | VPN Gateway átviteli sebesség (1) | VPN-átjáró maximális IPsec-alagutak (2) |
|--------------|----------------------------|---------------------------------------|
| Alapszintű termékváltozat (3) | 100 Mbit/s | 20 |
| Standard termékváltozat | 100 Mbit/s | 20 |
| High-Performance SKU | 200 Mbit/s | 10 |

Tábla megjegyzései:

(1) a VPN-átviteli sebesség nem garantált átviteli sebesség az interneten keresztül létesített létesítmények közötti kapcsolatokhoz. Ez a lehető legnagyobb átviteli sebesség mérése.

(2) a maximális alagutak a MDC összes előfizetésre vonatkozó teljes telepítése.

(3) az alapszintű SKU nem támogatja a BGP-útválasztást.

>[!IMPORTANT]
> Két MDC-telepítés között csak egy helyek közötti VPN-kapcsolat hozható létre. Ez a korlátozás azért van, mert a platform korlátozásai csak egyetlen VPN-kapcsolat használatát teszik lehetővé ugyanahhoz az IP-címhez. Mivel a MDC a több-bérlős átjárót használja, amely egyetlen nyilvános IP-címet használ a MDC rendszer összes VPN-átjárója számára, csak egy VPN-kapcsolat lehet két MDC-rendszer között.
>
> Ez a korlátozás arra az esetre is vonatkozik, ha egynél több helyek közötti VPN-kapcsolatot csatlakoztat egy olyan VPN-átjáróhoz, amely egyetlen IP-címet használ. A MDC nem engedélyezi egynél több helyi hálózati átjáró-erőforrás létrehozását ugyanazon IP-cím használatával.

### <a name="ipsecike-parameters"></a>IPsec/IKE-paraméterek

A MDC-ben VPN-kapcsolat beállításakor mindkét végponton konfigurálnia kell a kapcsolódást. Ha VPN-kapcsolatot konfigurál a MDC és egy hardvereszköz között, akkor az eszköz további beállítások megadását kéri. Előfordulhat például, hogy az eszköz egy VPN-átjáróként működő kapcsolót vagy útválasztót kér.

Az Azure-nal ellentétben, amely egyszerre több ajánlatot is támogat kezdeményezőként és válaszadóként, a MDC alapértelmezés szerint csak egy ajánlatot támogat. Ha más IPsec/IKE-beállításokat kell használnia a VPN-eszköz használatához, több beállítás is elérhető a kapcsolat manuális konfigurálásához.

#### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| **Tulajdonság** | **Érték** |
|--------------|-----------|
| IKE-verzió | IKEv2 |
| Diffie-Hellman Group | ECP384 |
| Hitelesítési módszer | Előre megosztott kulcs |
| Titkosítási & kivonatolási algoritmusok | AES256, SHA384 |
| SA élettartama (idő) | 28 800 másodperc |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| **Tulajdonság** | **Érték** |
|--------------|-----------|
| IKE-verzió | IKEv2 |
| Titkosítási & kivonatoló algoritmusok (titkosítás) | GCMAES256 |
| Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256 |
| SA élettartama (idő) | 27 000 másodperc |
| SA élettartama (kilobájt) | 33 553 408 |
| Tökéletes továbbítási titoktartás (PFS) | ECP384 |
| Elhalt társ-észlelés | Támogatott |

### <a name="configure-custom-ipsecike-connection-policies"></a>Egyéni IPsec/IKE-kapcsolatok szabályzatának konfigurálása

Az IPsec és az IKE protokoll szabványa számos titkosítási algoritmust támogat különböző kombinációkban. Az IPsec/IKE-paraméterek című témakörben megtekintheti, hogy mely paraméterek támogatottak a MDC-ben a megfelelőségi és biztonsági követelmények kielégítése érdekében.

Ez a cikk útmutatást nyújt az IPsec/IKE-szabályzatok létrehozásáról és konfigurálásáról, valamint az új vagy meglévő kapcsolatok alkalmazásáról.

#### <a name="considerations"></a>Megfontolandó szempontok

A következő fontos szempontokat kell figyelembe vennie a szabályzatok használatakor:

- Az IPsec/IKE-házirend csak a standard és a nagy teljesítményű (Route-alapú) átjáró SKU-ban működik.
- Egy adott kapcsolathoz csak egy házirendet adhat meg.
- Meg kell adnia az összes algoritmust és paramétert mind az IKE (Main Mode), mind az IPsec (gyors mód) esetében. A részleges házirend-specifikáció nem engedélyezett.
- A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a helyi VPN-eszközökön támogatott-e a házirend. A helyek közötti kapcsolatokat nem lehet létrehozni, ha a házirendek nem kompatibilisek.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Az IPsec/IKE-házirend létrehozásához és beállításához szükséges munkafolyamat

Ez a szakasz az IPsec/IKE-házirend létrehozásához és frissítéséhez szükséges munkafolyamatot ismerteti a helyek közötti VPN-kapcsolaton.

1. Hozzon létre egy virtuális hálózatot és egy VPN-átjárót.
1. Helyi hálózati átjáró létrehozása a létesítmények közötti kapcsolatok létrehozásához.
1. IPsec/IKE-házirend létrehozása a kiválasztott algoritmusokkal és paraméterekkel.
1. Hozzon létre egy IPsec-kapcsolatokat az IPsec/IKE-házirenddel.
1. IPsec/IKE-házirend hozzáadása, frissítése vagy eltávolítása egy meglévő kapcsolatban.

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Támogatott titkosítási algoritmusok és fő erősségek

A következő táblázat felsorolja a támogatott titkosítási algoritmusokat és a MDC ügyfelek által konfigurálható fő erősségeket.

| **IPsec/IKEv2** | **Beállítások** |
|-----------------|-------------|
| IKEv2 titkosítása | AES256, AES192, AES128, DES3, DES |
| IKEv2 integritása | SHA384, MD5, SHA1, SHA256 |
| DH-csoport | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, none |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs |
| IPsec-integritás | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs |
| A GYORSMÓDÚ biztonsági társítás élettartama | (Nem kötelező: a rendszer az alapértelmezett értékeket használja, ha nincs megadva.) |
| | Másodperc (egész szám, min. 300/alapértelmezett 27 000 másodperc) |
| | Kilobájt (egész szám, min. 1024/alapértelmezett 102 400 000 kilobájt) |
| Forgalmi választó | A MDC nem támogatja a házirend-alapú forgalmi választókat. |

A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

- IKE titkosítási algoritmus (fő mód/1. fázis).
- IKE integritási algoritmus (fő mód/1. fázis).
- DH-csoport (fő mód/1. fázis).
- IPsec titkosítási algoritmus (gyors mód/2. fázis).
- IPsec-integritási algoritmus (gyors mód/2. fázis).
- PFS-csoport (gyors mód/2. fázis).
- Az SA-élettartamok csak a helyi specifikációk. Nem kell megegyezniük.

Ha az IPsec titkosítási algoritmushoz GCMAES használ, akkor az IPsec-integritáshoz ugyanazt a GCMAES algoritmust és a kulcs hosszát kell kiválasztania. Használja például a GCMAES128-t mindkettőhöz.

Az előző táblázatban:

- A IKEv2 felel meg a Main Mode vagy az 1. fázisnak.
- Az IPsec megfelel a gyors vagy a 2. fázisnak.
- A DH-csoport a főmódban vagy az 1. fázisban használt Diffie-Hellman csoportot határozza meg.
- A PFS-csoport a gyors módban vagy a 2. fázisban használt Diffie-Hellman csoportot határozza meg.
- A IKEv2 fő módú SA élettartama 28 800 másodpercen belül megoldódott a MDC VPN-átjárók esetében.

A következő táblázat felsorolja az egyéni házirend által támogatott megfelelő Diffie-Hellman csoportokat.

| **Diffie-Hellman Group** | **DH-csoport** | **PFS-csoport** | **A kulcs hossza** |
|--------------------------|-------------|--------------|----------------|
| 1 | DHGroup1 | PFS1 | 768 bites MODP |
| 2 | DHGroup2 | PFS2 | 1024 bites MODP |
| 14 | DHGroup14 | PFS2048 | 2048 bites MODP |
| | DHGroup2048 | | |
| 19 | ECP256 | ECP256 | 256 bites ECP |
| 20 | ECP384 | ECP384 | 384 bites ECP |
| 24 | DHGroup24 | PFS24 | 2048 bites MODP |

### <a name="connect-mdc-to-azure-by-using-azure-expressroute"></a>MDC-kapcsolat az Azure-ba az Azure ExpressRoute használatával

#### <a name="overview-assumptions-and-prerequisites"></a>Áttekintés, feltételezések és előfeltételek

Az Azure ExpressRoute segítségével kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe. Egy kapcsolat szolgáltatója által biztosított magánhálózati kapcsolatot használ. A ExpressRoute nem VPN-kapcsolat a nyilvános interneten keresztül.

További információ az Azure ExpressRoute: ExpressRoute – áttekintés.

#### <a name="assumptions"></a>Feltételezések

Ez a cikk azt feltételezi, hogy rendelkezik a következővel:

- Az Azure működésének ismerete.
- A MDC alapszintű ismerete.
- A hálózatkezelés alapszintű ismerete.

#### <a name="prerequisites"></a>Előfeltételek

A MDC és az Azure ExpressRoute használatával történő összekapcsolásához a következő követelményeknek kell megfelelnie:

- A kapcsolat szolgáltatóján keresztül kiépített ExpressRoute áramkört kell létrehoznia.
- Azure-előfizetéssel hozzon létre egy ExpressRoute-áramkört és egy virtuális hálózatot az Azure-ban.
- Rendelkeznie kell egy útválasztóval, amelynek a következőket kell tennie:
  - A helyek közötti VPN-kapcsolatok támogatása a helyi hálózati adapter és Azure Stack több-bérlős átjáró között.
  - Támogatás a virtuális Útválasztás és a továbbítás több példányának létrehozásához, ha több bérlő van a MDC üzemelő példányában.
- Rendelkeznie kell egy olyan útválasztóval, amely a következőket tartalmazta:
  - A ExpressRoute áramkörhöz csatlakozó WAN-port.
  - A MDC több-bérlős átjáróhoz csatlakoztatott LAN-port.

#### <a name="expressroute-network-architecture"></a>ExpressRoute hálózati architektúra

A következő ábra a MDC és az Azure-környezeteket mutatja be, miután befejezte a ExpressRoute beállítását a jelen cikkben szereplő példák alapján.

 ![A ExpressRoute hálózati architektúráját bemutató ábra.](media/network-introduction/expressroute-network-architecture-60.png)

