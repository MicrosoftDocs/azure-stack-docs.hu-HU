---
title: A Azure Stack hub robusztus hálózatkezelésének bemutatása.
description: Ismerje meg az Azure Stack hub robusztus eszköz hálózatkezelését.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 3a6312a81632bffbb128049088119b30de79a732
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867802"
---
# <a name="azure-stack-hub-ruggedized-network-introduction"></a>Azure Stack hub robusztus hálózati bemutatása

## <a name="network-design-overview"></a>A hálózati tervezés áttekintése

### <a name="physical-network-design"></a>Fizikai hálózat kialakítása

Az Azure Stack hub robusztus megoldásához rugalmas és magasan elérhető fizikai infrastruktúra szükséges a működésének és szolgáltatásainak támogatásához. A ToR és a Border kapcsolók közötti Kikapcsolások SFP + vagy SFP28 adathordozóra, illetve 1 GB, 10 GB vagy 25 GB sebességre korlátozódnak. A rendelkezésre állás érdekében érdeklődjön az eredeti berendezésgyártó (OEM) hardver gyártójánál.

Az alábbi ábrán az Azure Stack hub számára ajánlott terv látható.

 ![Azure Stack hub robusztus fizikai hálózata](media/network-introduction/physical-network-design-basic.png) 

### <a name="logical-network-design"></a>Logikai hálózat kialakítása

A logikai hálózat kialakítása a fizikai hálózati infrastruktúra absztrakcióját jelöli. Ezek a gazdagépek, virtuális gépek és szolgáltatások hálózati hozzárendeléseinek rendszerezésére és leegyszerűsítésére szolgálnak. A logikai hálózat létrehozásának részeként a hálózati telephelyek jönnek létre a következők meghatározásához:
- virtuális helyi hálózatok (VLAN-ok)
- IP-alhálózatok
- IP-alhálózat/VLAN párok

Mindegyik fizikai helyen a logikai hálózathoz van társítva.

A következő táblázat azokat a logikai hálózatokat és IPv4-alhálózati tartományokat tartalmazza, amelyeket meg kell terveznie:

| **Logikai hálózat**   | **Leírás**  | **Méret** |
|-----------------------|------------------|----------|
| Nyilvános virtuális IP-cím (VIP)       | Azure Stack hub robusztus a hálózatból összesen 31 címet használ. Nyolc nyilvános IP-cím van használatban Azure Stack hub robusztus szolgáltatásainak kis készletéhez, a többi pedig a bérlői virtuális gépek által használt. Ha App Service és az SQL Resource Providers használatát tervezi, 7 további címet használ a rendszer. A fennmaradó 15 IP-cím a jövőbeli Azure-szolgáltatások számára van fenntartva. | /26 (62 gazdagép) –<br>/22 (1022 gazdagép)<br><br>Ajánlott =/24 (254 gazdagép) |
| Infrastruktúra váltása | Pont-pont típusú IP-címek útválasztási célokra, dedikált kapcsoló felügyeleti felületek és a kapcsolóhoz rendelt visszacsatolási címek. | /26 |
| Infrastruktúra        | Azure Stack hub robusztus belső összetevőinek kommunikációhoz használatos. | /24 |
| Személyes               | A Storage Network, a privát VIP-címek, az infrastruktúra-tárolók és egyéb belső függvények esetében használatos. | /20 |
| Alaplapi felügyeleti vezérlő (BMC) | A fizikai gazdagépeken található alaplapi-kezelési vezérlőkkel való kommunikációhoz használatos. | /26 |

### <a name="network-infrastructure"></a>Hálózati infrastruktúra

A Azure Stack hub hálózati infrastruktúrájának robusztus beállítása több, a kapcsolókon konfigurált logikai hálózatból áll. A következő ábra ezeket a logikai hálózatokat és azok integrálását mutatja be a Top-of-rack (TOR), a alaplapi felügyeleti vezérlő és a Border (Customer Network) kapcsolókkal.

Azure Stack hub robusztus logikai hálózati diagramja:

 ![Azure Stack hub robusztus logikai hálózata](media/network-introduction/logical-network-design.png) 

#### <a name="bmc-network"></a>BMC-hálózat

Ez a hálózat dedikált az összes alaplapi-felügyeleti vezérlő (más néven BMC vagy Service Processor) a felügyeleti hálózathoz való csatlakoztatásához. Ilyenek például a következők: iDRAC, iLO, iBMC stb. A BMC-csomópontokkal való kommunikációhoz csak egy BMC-fiók használható. Ha van ilyen, a hardver életciklus-állomása (HLH) ezen a hálózaton található, és OEM-specifikus szoftvereket biztosíthat a hardveres karbantartáshoz vagy monitorozáshoz.

A HLH az üzembe helyezési virtuális gépet (DVM) is üzemelteti. A DVM Azure Stack hub robusztus üzembe helyezése során használatos, és az üzembe helyezés befejezésekor törlődik. A DVM használatához internetkapcsolat szükséges a csatlakoztatott üzembe helyezési forgatókönyvekben több összetevő teszteléséhez, ellenőrzéséhez és eléréséhez. Ezek az összetevők a vállalati hálózaton belül és kívül is lehetnek (például: NTP, DNS és Azure). További információ a kapcsolati követelményekről: Azure Stack hub robusztus tűzfallal való integrációjának NAT szakasza.

#### <a name="private-network"></a>Magánhálózat

A/20 (4096 gazdagép IP-címei) hálózat az Azure Stack hub robusztus régiójának része. Nem terjed ki az Azure Stack hub robusztus régiójának szegély-kapcsoló eszközeire. Ez a hálózat több alhálózatra oszlik, például:

- **Storage Network**: egy/25 (128 IP-cím) hálózat, amely a közvetlen tárolóhelyek és az SMB-tárolási forgalom, valamint a virtuális gépek élő áttelepítésének támogatásához használatos.
- **Belső virtuális IP-hálózat**: a szoftveres terheléselosztó számára csak belső VIP-címekre dedikált/25 hálózat.
- **Container Network**: a/23 (512 IP-cím) hálózat, amely az infrastruktúra-szolgáltatásokat futtató tárolók közötti belső forgalomra van kijelölve

A magánhálózati hálózat mérete/20 (4096 IP-cím) a magánhálózati IP-címek számára. Ez a hálózat a Azure Stack hub robusztus rendszerének része. Nem halad át az Azure Stack hub robusztus rendszerének szegély-kapcsoló eszközein, és több Azure Stack hub-beli robusztus rendszeren is felhasználható. Míg a hálózat a Azure Stack hub számára robusztus, nem lehet átfedésben az adatközpont többi hálózatával. A magánhálózati IP-címekre vonatkozó útmutatásért javasoljuk, hogy kövesse a 1918-es RFC-dokumentumot.

A/20 magánhálózati IP-terület több hálózatra oszlik, amelyek lehetővé teszik, hogy az Azure Stack hub robusztus rendszerinfrastruktúrája a későbbi kiadásokban a tárolókban fusson. A részletekért tekintse meg a 1910 kibocsátási megjegyzéseit. Ez az új magánhálózati IP-terület lehetővé teszi, hogy a telepítés előtt csökkentse a szükséges irányítható IP-területet.

#### <a name="azure-stack-hub-ruggedized-infrastructure-network"></a>Azure Stack hub robusztus infrastruktúra-hálózata

A/24 hálózat a belső Azure Stack hub robusztus összetevőire van kijelölve, amelyek egymás közötti kommunikációt és információcserét szolgálják. Ez az alhálózat a Azure Stack hub robusztus megoldásnak az adatközponthoz való külsőleg irányítható. **Nem** javasoljuk nyilvános vagy internetes ÚTVÁLASZTÁSÚ IP-címek használatát ezen az alhálózaton. Ezt a hálózatot a rendszer meghirdeti a szegélynek, de a legtöbb IP-címet Access Control listák (ACL-ek) védik. A hozzáférésre jogosult IP-címek kis hatótávolságon belül vannak, a mérete pedig egy/27 hálózat. Az IP-címeket futtató szolgáltatások, például az emelt szintű végpont (PEP) és a Azure Stack hub robusztus biztonsági mentés.

#### <a name="public-vip-network"></a>Nyilvános VIP-hálózat

A nyilvános VIP-hálózat a Azure Stack hub hálózati vezérlőjéhez lett kiosztva. Nem logikai hálózat a kapcsolón. A SLB a címek készletét használja, és hozzárendeli/32 hálózatot a bérlői munkaterhelésekhez. A Switch útválasztási táblázatban ezek/32 IP-címek Border Gateway Protocol (BGP) keresztül elérhető útvonalként lesznek meghirdetve. Ez a hálózat nyilvános címeket tartalmaz, amelyek külsőleg elérhetők. Az Azure Stack hub robusztus infrastruktúrája a nyilvános VIP-hálózat első 31 címét lefoglalja, míg a maradékot a bérlői virtuális gépek használják. Az alhálózat hálózati mérete legfeljebb/26 (64 gazdagép) lehet legfeljebb/22 (1022 gazdagép) értékre. Javasoljuk, hogy tervezzen egy/24 hálózatot.

#### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása

A/26 hálózat az az alhálózat, amely a pont-pont típusú IP/30 (két gazdagép IP) alhálózatot és a visszacsatolásokat tartalmazza. Ezek dedikált/32 alhálózatok a sávon belüli kapcsolók felügyeletéhez és a BGP-útválasztó AZONOSÍTÓhoz. Az IP-címtartományok ezen tartományának az Azure Stack hub robusztus megoldásán kívül kell irányítható az adatközpontra. Előfordulhat, hogy az IP-címek magán vagy nyilvánosak.

#### <a name="switch-management-network"></a>Váltás a felügyeleti hálózatra

A/29 (Six Host IP-címek) hálózat dedikált a kapcsolók felügyeleti portjainak csatlakoztatására. Ez a hálózat sávon kívüli hozzáférést tesz lehetővé az üzembe helyezéshez, a felügyelethez és a hibaelhárításhoz. A rendszer a fent említett kapcsoló-infrastruktúra hálózat alapján számítja ki.

## <a name="dns-design-overview"></a>A DNS-tervezés áttekintése

Ha Azure Stack hub robusztus végpontokat (*portál*, *adminportal*, *Management*, *adminmanagement*) szeretne elérni Azure stack Hub-ból kívülről, akkor az Azure Stack hub robusztus DNS-szolgáltatásait az Azure stack hub-ban robusztus DNS-zónákat üzemeltető DNS-kiszolgálókkal kell integrálnia.

### <a name="azure-stack-hub-ruggedized-dns-namespace"></a>Azure Stack hub robusztus DNS-névtere

A DNS-sel kapcsolatos fontos információkat kell megadnia Azure Stack hub robusztus üzembe helyezése során.

| **Mező** | **Leírás** | **Példa** |
|-----------|-----------------|-------------|
| Régió                   | A Azure Stack hub robusztus üzembe helyezésének földrajzi helye. | kelet               |
| Külső tartomány neve     | Az Azure Stack hub robusztus üzembe helyezéséhez használni kívánt zóna neve. | cloud.fabrikam.com |
| Belső tartomány neve     | Az Azure Stack hub infrastruktúra-szolgáltatásaihoz használt belső zóna neve robusztus. A címtár-szolgáltatás integrált és magánjellegű (nem érhető el az Azure Stack hub robusztus üzembe helyezésén kívülről). | azurestack. local   |
| DNS-továbbítók           | Azok a DNS-kiszolgálók, amelyek a DNS-lekérdezések, a DNS-zónák és a Azure Stack hub-on kívül futtatott rekordok továbbítására szolgálnak a vállalati intraneten vagy a nyilvános interneten. A DNS-továbbító értékét a telepítés után a **set-AzSDnsForwarder** parancsmaggal módosíthatja. |                    |
| Elnevezési előtag (nem kötelező) | Az a névadási előtag, amelynek a Azure Stack hub robusztus infrastruktúrájának szerepkör-példányainak a neveit meg szeretné jeleníteni. Ha nincs megadva, az alapértelmezett érték a "AZS". | AZS                |

A Azure Stack hub teljes tartományneve (FQDN) robusztus központi telepítés és végpontok a régió paraméter és a külső tartománynév paraméter kombinációja. Az előző táblázatban szereplő példák értékeit használva a Azure Stack hub robusztus központi telepítésének teljes tartományneve a következő lesz: *East.Cloud.fabrikam.com*

Ilyenek például a központi telepítés egyes végpontjai a következő URL-címekhez hasonlóak:

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Ha ezt a példát DNS-névteret szeretné használni egy Azure Stack hub robusztus üzembe helyezéséhez, a következő feltételek szükségesek:

- A zóna fabrikam.com regisztrálva van egy tartományregisztráló, belső vállalati DNS-kiszolgáló vagy mindkettő. A regisztráció a névfeloldási követelményektől függ.
- A gyermektartomány cloud.fabrikam.com a zóna fabrikam.com alatt található.
- A fabrikam.com és cloud.fabrikam.com zónákat üzemeltető DNS-kiszolgálók a Azure Stack hub robusztus üzembe helyezéséről érhetők el.

Ha Azure Stack hub-alapú robusztus végpontok és példányok DNS-neveit szeretné feloldani Azure Stack hub-on kívülről, akkor integrálnia kell a DNS-kiszolgálókat. Azokat a kiszolgálókat, amelyek a Azure Stack hub külső DNS-zónáját futtatják, a használni kívánt szülő zónát futtató DNS-kiszolgálókkal.

#### <a name="dns-name-labels"></a>DNS-nevek címkéi

Azure Stack hub robusztusan támogatja a DNS-név címke nyilvános IP-címhez való hozzáadását, hogy engedélyezze a névfeloldást a nyilvános IP-címek számára. A DNS-címkék kényelmes módszert biztosítanak a felhasználók számára, hogy az Azure Stack hub-ban üzemeltetett alkalmazásokat és szolgáltatásokat a név alapján robusztus módon érje el. A DNS-név címkéje némileg eltérő névteret használ, mint az infrastruktúra-végpontok. Az előző példában szereplő névtérrel a DNS-nevek címkéjének névtere a következő lesz: *\* . East.cloudapp.Cloud.fabrikam.com*.

Ha egy bérlő egy nyilvános IP- **SajátPr** DNS-név mezőjében megad egy rekordot, akkor a **SajátPr** -rekord létrehozása az Azure stack hub robusztus külső DNS-kiszolgálójának **East.cloudapp.Cloud.fabrikam.com** . Az eredményül kapott teljes tartománynév a következő: *MyApp.East.cloudapp.Cloud.fabrikam.com*.

Ha szeretné kihasználni ezt a funkciót, és ezt a névteret használja, integrálnia kell a DNS-kiszolgálókat. Azokat a kiszolgálókat, amelyek a Azure Stack hub külső DNS-zónáját tárolják, és a használni kívánt szülő zónát üzemeltető DNS-kiszolgálókat is. Ez a névtér különbözik az Azure Stack hub robusztus szolgáltatási végpontok által használttól, ezért létre kell hoznia egy további delegálási vagy feltételes továbbítási szabályt.

A DNS-név címkével kapcsolatos további információkért tekintse meg a "DNS használata" című részt Azure Stack hub-ban robusztus.

### <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- A mérvadó DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A rekurzív DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Azure Stack hub robusztusan tartalmazza a mérvadó és a rekurzív DNS-kiszolgálókat is. A rekurzív kiszolgálók a belső privát zóna kivételével mindent feloldják a neveket, az Azure Stack hub külső nyilvános DNS-zónáját pedig a robusztus üzembe helyezéshez.

## <a name="resolving-external-dns-names-from-azure-stack-hub-ruggedized"></a>Külső DNS-nevek feloldása Azure Stack hub-ról robusztus

A Azure Stack hub-on kívüli végpontok DNS-neveinek feloldásához (például: www.bing.com) olyan DNS-kiszolgálókat kell megadnia a Azure Stack hub számára, amelyek a DNS-kérések továbbítására lettek kiterjesztve, mert az Azure Stack hub egyenetlen, nem mérvadó. Azok a DNS-kiszolgálók, amelyeken Azure Stack hub strapabíróan továbbítanak kérelmeket, szükségesek a telepítési munkalapon (a DNS-továbbító mezőben). A hibatűréshez legalább két kiszolgálót adjon meg ebben a mezőben. Ezen értékek nélkül Azure Stack hub robusztus üzembe helyezése meghiúsul. A DNS-továbbító értékeit a telepítés után a **set-AzSDnsForwarder** parancsmaggal módosíthatja.

## <a name="firewall-design-overview"></a>A tűzfal kialakításának áttekintése

Javasoljuk, hogy használjon egy tűzfal eszközt az Azure Stack hub robusztus védelme érdekében. A tűzfalak segítenek megvédeni az olyan műveleteket, mint az elosztott szolgáltatásmegtagadási (DDOS) támadások, a behatolások észlelése és a tartalmi ellenőrzés. Ugyanakkor az Azure Storage-szolgáltatások, például a Blobok, a táblák és a várólisták adatátviteli szűk keresztmetszete is lehetnek.

Ha a rendszer leválasztott központi telepítési módot használ, közzé kell tennie a AD FS végpontot. További információ: Datacenter Integration Identity (adatközpont-integrációs identitás) című cikk.

A Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nem feltétlenül igényelnek külső közzétételt. Például szolgáltatóként korlátozhatja a támadási felületet úgy, hogy csak a hálózaton belülről, az internetről származó Azure Stack hub felügyeletét végzi.

A vállalati szervezetek esetében a külső hálózat lehet a meglévő vállalati hálózat. Ebben a forgatókönyvben a végpontokat közzé kell tenni a vállalati hálózatról Azure Stack hub működéséhez.

### <a name="network-address-translation"></a>Hálózati címfordítás

A hálózati címfordítás (NAT) az ajánlott módszer, amely lehetővé teszi, hogy az üzembe helyezési virtuális gép (DVM) hozzáférjen a külső erőforrásokhoz az üzembe helyezés során. A vészhelyzeti helyreállítási konzol (ERCS) virtuális gépei vagy privilegizált végpontja (PEP) is a regisztráció és a hibaelhárítás során. 

A NAT a külső hálózat vagy a nyilvános VIP-címek nyilvános IP-címeinek alternatívája is lehet. Ez azonban nem ajánlott, mert korlátozza a bérlői felhasználói élményt, és növeli a bonyolultságot. Az egyik lehetőség egy olyan NAT lenne, amelyhez felhasználói IP-címenként még egy nyilvános IP-címet kell megadni a készleten. Egy másik lehetőség egy olyan NAT-szabály, amelyhez felhasználónként egy virtuális IP-cím szükséges NAT-szabályt a felhasználók által használt összes porthoz.

A NAT nyilvános VIP-hez való használatának néhány hátránya a következő:
- A tűzfalszabályok kezelésekor a felhasználók a szoftveresen definiált hálózatkezelési (SDN) veremben szabályozzák a saját végpontokat és a közzétételi szabályokat. A felhasználóknak fel kell venniük a kapcsolatot a Azure Stack hub robusztus kezelővel a virtuális IP-címek közzétételéhez és a portok listájának frissítéséhez. 
- Míg a NAT-használat korlátozza a felhasználói élményt, teljes hozzáférést biztosít az operátornak a közzétételi kérelmekhez.
- Az Azure-ban használt hibrid felhőalapú forgatókönyvek esetében vegye figyelembe, hogy az Azure nem támogatja VPN-alagút beállítását a NAT-t használó végpontok számára.

### <a name="ssl-interception"></a>SSL-lehallgatás

Jelenleg ajánlott letiltani az SSL-elfogást (például a visszafejtés kiszervezését) az összes Azure Stack hub-alapú robusztus forgalom esetében. Ha a jövőbeli frissítések támogatják, útmutatást nyújt arról, hogyan engedélyezhető az SSL-lehallgatás a Azure Stack hub számára.

###  <a name="edge-deployment-firewall-scenario"></a>Peremhálózati telepítési tűzfal forgatókönyve

Az Edge-telepítésekben a Azure Stack hub robusztus módon települ a peremhálózati útválasztó vagy a tűzfal mögött. Ezekben a forgatókönyvekben a tűzfal a szegély felett (1. forgatókönyv) nagyobb, mint az aktív-aktív és az aktív-passzív tűzfal konfigurációjának támogatása. A szegély eszközként is működhet (2. forgatókönyv), ahol csak az aktív-aktív tűzfal-konfigurációt támogatja. A 2. forgatókönyv az egyenlő árú többutas (ECMP) értékre támaszkodik, vagy a BGP vagy statikus útválasztás a feladatátvételhez.

Nyilvános, átirányítható IP-címek vannak megadva a nyilvános VIP-készlethez a külső hálózatról, a központi telepítés időpontjában. Biztonsági okokból a nyilvános útválasztású IP-címek **nem** ajánlottak egy Edge-forgatókönyvben más hálózatokon. Ez a forgatókönyv lehetővé teszi a felhasználók számára, hogy a teljes mértékben felügyelt Felhőbeli élményt a nyilvános felhőben, például az Azure-ban is megtapasztalják.

 ![Azure Stack hub robusztus peremhálózati tűzfal forgatókönyve](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve

Vállalati intranetes vagy peremhálózati üzembe helyezés esetén a Azure Stack hub robusztus módon van telepítve egy többzónás tűzfalon, illetve a peremhálózati tűzfal és a belső vállalati hálózati tűzfal között. Ezután a rendszer a biztonságos, peremhálózati (vagy DMZ) és a nem biztonságos zónák között osztja el a forgalmat, az alábbiak szerint:

- **Biztonságos zóna**: az a belső hálózat, amely belső vagy vállalati ÚTVÁLASZTÁSÚ IP-címeket használ. A biztonságos hálózat osztható. Az internetről kimenő hozzáférés a tűzfal NAT-on keresztül is elérhető. Általában a belső hálózaton keresztül érhető el az adatközponton belül. Minden Azure Stack hub robusztus hálózatnak a biztonságos zónában kell lennie, kivéve a külső hálózat nyilvános VIP-készletét.
- **Peremhálózati zóna**. A peremhálózaton a külső vagy internetes alkalmazások, például a webkiszolgálók jellemzően üzembe helyezhetők. Általában egy tűzfal figyeli, hogy elkerülje a támadásokat, például a DDoS és a behatolás (hacking) szolgáltatást, miközben továbbra is engedélyezi a megadott bejövő forgalmat az internetről. Csak a Azure Stack hub külső hálózati nyilvános VIP-készletének robusztusnak kell lennie a DMZ-zónában.
- Nem **biztonságos zóna**. A külső hálózat, az Internet. A nem biztonságos zónában lévő Azure Stack hub telepítése **nem** ajánlott.

 ![Peremhálózati tűzfal forgatókönyve](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>A VPN-tervezés áttekintése

Bár a VPN felhasználói fogalom, néhány fontos szempontot figyelembe kell vennie, hogy a megoldás tulajdonosának és az operátornak ismernie kell.

Mielőtt hálózati forgalmat küldene az Azure-beli virtuális hálózat és a helyszíni hely között, létre kell hoznia egy virtuális hálózati (VPN-) átjárót a virtuális hálózathoz.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. A VPN-átjárók használatával biztonságos forgalmat küldhet Azure Stack hub virtuális hálózata és egy Azure-beli virtuális hálózat között. A virtuális hálózat és egy másik, VPN-eszközhöz csatlakozó hálózat között is biztonságosan küldhet forgalmat.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Azure Stack hub robusztusan támogatja a virtuális hálózati átjárók egy típusát: a **VPN-** típust.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen típusú beállítás például egy többhelyes kapcsolat konfigurálása.

Az Azure Stack hub VPN-átjáróinak létrehozása és konfigurálása előtt tekintse át a Azure Stack hub robusztus hálózatkezelésével kapcsolatos szempontokat. Megtudhatja, hogyan különböznek a Azure Stack hub-konfigurációk az Azure-ból.

Az Azure-ban a kiválasztott VPN Gateway SKU sávszélességének átviteli sebességét az átjáróhoz csatlakozó összes kapcsolaton át kell osztani. Azure Stack hub-ban azonban robusztus a VPN Gateway SKU sávszélesség-értéke az átjáróhoz csatlakozó összes kapcsolati erőforrásra vonatkozik. Például: 

- Az Azure-ban az alapszintű VPN Gateway SKU körülbelül 100 Mbps összesített adatátviteli kapacitást tud kielégíteni. Ha két kapcsolatot hoz létre a VPN-átjáróhoz, és az egyik kapcsolat 50 Mbps sávszélességet használ, akkor az 50 Mbps elérhető lesz a másik kapcsolat számára. 
- Azure Stack hub robusztus, az alapszintű VPN Gateway SKU-hoz való minden kapcsolat 100 MB/s kapacitást foglal le.

### <a name="vpn-types"></a>VPN-típusok

Amikor létrehoz egy VPN Gateway-konfigurációhoz tartozó virtuális hálózati átjárót, meg kell adnia egy VPN-típust. A választott VPN-típus a létrehozni kívánt kapcsolati topológiától függ. A VPN-típus az Ön által használt hardvertől is függhet. A S2S-konfigurációkhoz VPN-eszköz szükséges. Egyes VPN-eszközök csak bizonyos VPN-típusokat támogatnak. 

>[!IMPORTANT]
> A Azure Stack hub jelenleg csak robusztus módon támogatja az Útválasztás-alapú VPN-típust. Ha az eszköz csak a házirend-alapú VPN-eket támogatja, akkor a Azure Stack hub-ból származó eszközökhöz való csatlakozások nem támogatottak. Emellett Azure Stack hub strapabíró, nem támogatja a házirend-alapú adatforgalmi választókat az útvonal-alapú átjárók esetében, mivel az egyéni IPSec/IKE-házirend-konfigurációk nem támogatottak.

- **Házirendalapú**: a házirend-alapú VPN-ek az IPSec-házirendek alapján titkosítják és irányítják a csomagokat az IPSec-alagutakon keresztül. A szabályzatok a helyszíni hálózat és a Azure Stack hub robusztus VNet közötti címtartomány-kombinációkkal vannak konfigurálva. A házirend vagy a forgalmi választó általában egy hozzáférési lista a VPN-eszköz konfigurációjában.  A **házirendalapú** az Azure-ban támogatott, de nem Azure stack hub-ban robusztus. 
- **Útvonalalapú**: az Útválasztás-alapú VPN-ek az IP-továbbítás vagy az útválasztási táblázatban konfigurált útvonalakat használják. Az útvonalak a közvetlen csomagokat a megfelelő bújtatási interfészekhez irányítják. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A **útvonalalapú** VPN-EK házirendje vagy forgalmi választója bármilyen módon van konfigurálva (vagy használjon helyettesítő kártyát). Alapértelmezés szerint nem módosíthatók. A **útvonalalapú** VPN-típus értéke **útvonalalapú**.

### <a name="configuring-a-vpn-gateway"></a>VPN-átjáró konfigurálása

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

A következő szakaszban található diagramok és leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolódási topológiát. A diagramok a fő alapkonfigurációkat mutatják be, de a diagramok útmutatóként való használatával összetettebb konfigurációk is létrehozhatók.

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

##### <a name="site-to-site"></a>Helyek közötti kapcsolat

A *helyek közötti* (S2S) VPN Gateway-kapcsolat IPSec/IKE (IKEV2) VPN-alagúton keresztüli kapcsolat. Az ilyen típusú kapcsolathoz olyan VPN-eszköz szükséges, amely a helyszínen található, és egy nyilvános IP-címet kap. Ez az eszköz nem helyezhető el NAT mögött. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

##### <a name="multi-site"></a>Többhelyes

A *többhelyes* kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Több kapcsolat használata esetén egy Route-alapú VPN-típust (más néven dinamikus átjárót) kell használnia a klasszikus virtuális hálózatok használata esetén. Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen.

### <a name="gateway-skus"></a>Átjáró-termékváltozatok

Ha Azure Stack hub virtuális hálózati átjáróját robusztusan hozza létre, akkor a használni kívánt átjáró-SKU-t kell megadnia. A következő VPN Gateway SKU-ket támogatja:

- Alapszintű
- Standard
- Nagy teljesítmény

Egy magasabb szintű átjáró kiválasztásával több processzor és hálózati sávszélesség is kiosztható az átjáróra. Ennek eredményeképpen az átjáró képes támogatni a virtuális hálózat nagyobb hálózati átviteli sebességét.

Azure Stack hub robusztusan nem támogatja az ultra Performance Gateway SKU-t, amely kizárólag az expressz útvonalon használható.

Az SKU kiválasztásakor vegye figyelembe a következőket:

- Azure Stack hub strapabíróan nem támogatja a házirend-alapú átjárókat.
- A BGP nem támogatott az alapszintű SKU-ban.
- ExpressRoute – a VPN-átjárók egyidejű konfigurációi nem támogatottak Azure Stack hub-ban.

#### <a name="gateway-availability"></a>Átjáró rendelkezésre állása

A magas rendelkezésre állási forgatókönyvek csak a **nagy teljesítményű átjáró-** kapcsolatok SKU-on konfigurálhatók. Az Azure-val ellentétben, amely az aktív/aktív és az aktív/passzív konfigurációkon keresztül biztosítja a rendelkezésre állást, Azure Stack hub robusztus módon csak az aktív/passzív konfigurációt támogatja.

#### <a name="failover"></a>Feladatátvétel

A Azure Stack hub három több-bérlős átjáró-infrastruktúra virtuális gépet is robusztus. Két virtuális gép aktív üzemmódban van, a harmadik pedig redundáns módban van. Az aktív virtuális gépek lehetővé teszik a VPN-kapcsolatok létrehozását, és a redundáns virtuális gép csak akkor fogadja a VPN-kapcsolatokat, ha feladatátvétel történik. Ha egy aktív átjárót használó virtuális gép elérhetetlenné válik, a VPN-kapcsolat a kapcsolat elvesztése után rövid idő (néhány másodperc) elteltével átadja a virtuális gép feladatátvételét.

### <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

A következő táblázat az átjárók típusát és az átjáró SKU által becsült összesített átviteli sebességet mutatja:

|                          | **VPN Gateway teljesítménye (1)** | **VPN Gateway IPsec-alagútjainak maximális száma (2)** |
|--------------------------|--------------------------------|---------------------------------------|
| **Alapszintű SKU** **(3)**    | 100 Mbps                       | 20                                    |
| **Standard termékváltozat**         | 100 Mbps                       | 20                                    |
| **Nagy teljesítményű SKU** | 200 Mbit/s                       | 10                                    |

Tábla megjegyzései

**(1)** – a VPN-átviteli sebesség nem garantált átviteli sebesség az interneten keresztül létesített létesítmények közötti kapcsolatokhoz. Ez a lehető legnagyobb átviteli sebesség mérése.  
**(2)** – az alagutak maximális száma az összes előfizetésnél Azure stack hub-alapú robusztus üzembe helyezés.  
**(3)** – a BGP-útválasztás nem támogatott az alapszintű SKU esetében.  

>[!IMPORTANT]
> Két Azure Stack hub robusztus üzembe helyezése között csak egy helyek közötti VPN-kapcsolat hozható létre. Ennek oka a platform olyan korlátozása, amely csak egyetlen VPN-kapcsolat használatát teszi lehetővé ugyanahhoz az IP-címhez. Mivel Azure Stack hub robusztusan kihasználja a több-bérlős átjárót, amely egyetlen nyilvános IP-címet használ a Azure Stack hub robusztus rendszerének összes VPN-átjárója számára, csak egy VPN-kapcsolat lehet két Azure Stack hub-alapú robusztus rendszer között. 
>
> Ez a korlátozás arra az esetre is vonatkozik, ha egynél több helyek közötti VPN-kapcsolatot csatlakoztat egy olyan VPN-átjáróhoz, amely egyetlen IP-címet használ. Azure Stack hub strapabíró, nem teszi lehetővé, hogy egynél több helyi hálózati átjáró-erőforrást hozzon létre ugyanazzal az IP-címmel. * * 


### <a name="ipsecike-parameters"></a>IPsec/IKE-paraméterek

Ha Azure Stack hub-ban robusztus VPN-kapcsolat van beállítva, akkor mindkét végén konfigurálnia kell a kapcsolódást. Ha Azure Stack hub és egy hardvereszköz között VPN-kapcsolatot konfigurál, az eszköz további beállítások megadását kéri. Például egy VPN-átjáróként működő kapcsoló vagy útválasztó.

Az Azure-nal ellentétben, amely egyszerre több ajánlatot is támogat kezdeményezőként és válaszadóként, Azure Stack hub-nal a robusztus szolgáltatás alapértelmezés szerint csak egy ajánlatot támogat. Ha más IPSec/IKE-beállításokat kell használnia a VPN-eszköz használatához, több beállítás is elérhető a kapcsolat manuális konfigurálásához.

#### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| **Tulajdonság**                    | **Érték**      |
|---------------------------------|----------------|
| IKE verziószám                     | IKEv2          |
| Diffie-Hellman Group            | ECP384         |
| Hitelesítési módszer           | Előre megosztott kulcs |
| Titkosító és kivonatoló algoritmus | AES256, SHA384 |
| SA élettartama (Idő)              | 28 800 másodperc |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| **Tulajdonság**                                     | **Érték**      |
|--------------------------------------------------|----------------|
| IKE verziószám                                      | IKEv2          |
| Titkosítási & kivonatoló algoritmusok (titkosítás)     | GCMAES256      |
| Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256      |
| SA élettartama (Idő)                               | 27 000 másodperc |
| SA élettartama (kilobájt)                          | 33 553 408     |
| Sérülés utáni titkosságvédelem (PFS)                    | ECP384         |
| Kapcsolat megszakadásának észlelése                              | Támogatott      |

### <a name="configure-custom-ipsecike-connection-policies"></a>Egyéni IPSec/IKE-kapcsolatok szabályzatának konfigurálása

Az IPsec és az IKE protokoll szabványa számos titkosítási algoritmust támogat különböző kombinációkban. Az IPsec/IKE-paraméterekkel kapcsolatban tekintheti meg, hogy mely paraméterek támogatottak Azure Stack hub-ban.

Ez a cikk útmutatást nyújt az IPsec/IKE-szabályzatok létrehozásáról és konfigurálásáról, valamint az új vagy meglévő kapcsolatok alkalmazásáról.

#### <a name="considerations"></a>Megfontolandó szempontok

A szabályzatok használatakor vegye figyelembe a következő fontos szempontokat:

- Az IPsec/IKE-házirend csak a *standard* és a *HighPerformance* (Route-based) átjáró SKU-ban működik.
- Egy adott kapcsolathoz csak **egy** házirendet adhat meg.
- Meg kell adnia az összes algoritmust és paramétert mind az IKE (Main Mode), mind az IPsec (gyors mód) esetében. A részleges házirend-specifikáció nem engedélyezett.
- A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a helyi VPN-eszközökön támogatott-e a házirend. A helyek közötti kapcsolatokat nem lehet létrehozni, ha a házirendek nem kompatibilisek.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Az IPsec/IKE-házirend létrehozásához és beállításához szükséges munkafolyamat

Ez a szakasz a két hálózat közötti pont-pont típusú VPN-kapcsolat IPsec/IKE-házirendjének létrehozásához és frissítéséhez szükséges munkafolyamatot ismerteti:

1.  Hozzon létre egy virtuális hálózatot és egy VPN-átjárót.
2.  Helyi hálózati átjáró létrehozása a létesítmények közötti kapcsolatok létrehozásához.
3.  IPsec/IKE-házirend létrehozása a kiválasztott algoritmusokkal és paraméterekkel.
4.  Hozzon létre egy IPSec-kapcsolatokat az IPsec/IKE-házirenddel.
5.  IPsec/IKE-házirend hozzáadása/frissítése/eltávolítása egy meglévő kapcsolatban.

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Támogatott titkosítási algoritmusok és fő erősségek

A következő táblázat felsorolja a támogatott titkosítási algoritmusokat és a Azure Stack hub által robusztus ügyfelek által konfigurálható fő erősségeket:

| **IPsec/IKEv2**  | **Lehetőségek** |
|------------------|-------------|
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES                                                                                                                         |
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256                                                                                                                                 |
| DH-csoport         | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, none                                                                                          |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs                                                                                  |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                                                                                                        |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs                                                                                                          |
| Gyorsmódú biztonsági társítás élettartama   | (Nem kötelező: Ha nincs megadva, az alapértelmezett értékek szerepelnek)                                                                                                      |
|                  | Másodperc (egész szám; min. 300/alapértelmezett 27 000 másodperc)                                                                                                         |
|                  | Kilobájt (egész szám; min. 1024/alapértelmezett 102 400 000 kilobájt)                                                                                                      |
| Forgalomválasztó | A házirend-alapú forgalmi választókat nem támogatja a Azure Stack hub robusztus.                                                                                           |

A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

 - IKE titkosítási algoritmus (fő mód/1. fázis).
 - IKE integritási algoritmus (fő mód/1. fázis).
 - DH-csoport (fő mód/1. fázis).
 - IPsec titkosítási algoritmus (gyors mód/2. fázis).
 - IPsec-integritási algoritmus (gyors mód/2. fázis).
 - PFS-csoport (gyors mód/2. fázis).
 - Az SA-élettartamok csak a helyi specifikációk, ezért nem kell megegyezniük.

Ha a GCMAES-t IPsec titkosítási algoritmusként használja, ki kell választania ugyanazt a GCMAES algoritmust és a kulcs hosszát az IPsec-integritáshoz. Például: a GCMAES128 használata mindkettőhöz.

Az előző táblázatban:

- A IKEv2 felel meg a Main Mode vagy az 1. fázisnak.
- Az IPsec megfelel a gyors vagy a 2. fázisnak.
- A DH-csoport a főmódban vagy az 1. fázisban használt Diffie-Hellmen csoportot határozza meg.
- A PFS-csoport a gyors módban vagy a 2. fázisban használt Diffie-Hellmen csoportot határozza meg.
- A IKEv2 fő módú SA élettartama 28 800 másodpercen belül megoldódott az Azure Stack hub strapabíró VPN-átjárók esetében.

A következő táblázat felsorolja az egyéni házirend által támogatott megfelelő Diffie-Hellman csoportokat:

| **Diffie-Hellman Group** | **DH-csoport**          | **PFS-csoport** | **A kulcs hossza** |
|--------------------------|----------------------|--------------|----------------|
| 1                        | DHGroup1             | PFS1         | 768 bites MODP   |
| 2                        | DHGroup2             | PFS2         | 1024 bites MODP  |
| 14                       | DHGroup14            | PFS2048      | 2048 bites MODP  |
|                          | DHGroup2048          |              |                |
| 19                       | ECP256               | ECP256       | 256 bites ECP    |
| 20                       | ECP384               | ECP384       | 384 bites ECP    |
| 24                       | DHGroup24            | PFS24        | 2048 bites MODP  |

### <a name="connect-azure-stack-hub-ruggedized-to-azure-using-azure-expressroute"></a>Az Azure-ExpressRoute az Azure-ban robusztus Azure Stack hub

#### <a name="overview-assumptions-and-prerequisites"></a>Áttekintés, feltételezések és előfeltételek

Az Azure ExpressRoute segítségével kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe. Egy kapcsolat szolgáltatója által biztosított magánhálózati kapcsolatot használ. A ExpressRoute nem VPN-kapcsolat a nyilvános interneten keresztül.

További információ az Azure ExpressRoute: ExpressRoute – áttekintés.

#### <a name="assumptions"></a>Feltételezések

Ez a cikk azt feltételezi, hogy:

- Ön rendelkezik az Azure-ban működő ismeretekkel.
- Azure Stack hub alapszintű ismerete robusztus.
- A hálózatkezelés alapvető ismerete.

#### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack hub robusztus és az Azure ExpressRoute-vel való összekapcsolásához a következő követelményeknek kell megfelelnie:

- Egy kiépített ExpressRoute áramkör a kapcsolati szolgáltatón keresztül.
- Azure-előfizetés ExpressRoute-áramkör és virtuális hálózatok létrehozásához az Azure-ban.
- A-t támogató útválasztó:
  - helyek közötti VPN-kapcsolatok a helyi hálózati adapter és a Azure Stack hub robusztus, több-bérlős átjárója között.
  - több VRFs létrehozása (virtuális Útválasztás és továbbítás), ha több bérlő található a Azure Stack hub robusztus üzembe helyezésében.
- Egy útválasztó, amely a következőket tartalmazta:
  - A ExpressRoute áramkörhöz csatlakozó WAN-port.
  - A Azure Stack hub robusztus több-bérlős átjáróhoz csatlakoztatott LAN-port.

#### <a name="expressroute-network-architecture"></a>ExpressRoute hálózati architektúra

Az alábbi ábra az Azure Stack hub robusztus és Azure-környezeteit mutatja be a ExpressRoute beállításának befejezése után a jelen cikkben szereplő példák alapján:

 ![ExpressRoute hálózati architektúra](media/network-introduction/expressroute-network-architecture-60.png) 

Az alábbi ábra azt mutatja be, hogy több bérlő Hogyan kapcsolódhat a Azure Stack hub robusztus infrastruktúrájának ExpressRoute-útválasztón keresztül az Azure-ba:

 ![ExpressRoute hálózati architektúra – több-bérlős](media/network-introduction/expressroute-network-architecture-multi-tenant-60.png) 

