---
title: MDC hálózati integráció
description: Ismerkedjen meg Azure Stack hálózati integrációval a MDC-eszközhöz.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: bfc1851d57db31a929b92902792851a3cef58b39
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182971"
---
# <a name="network-integration"></a>Hálózati integráció 

Ez a témakör Azure Stack hálózati integrációt ismerteti.

## <a name="border-connectivity-uplink"></a>Szegélyes kapcsolat (kimenő)


A hálózati integráció megtervezése fontos előfeltétel a sikeres Azure Stack integrált rendszerek üzembe helyezéséhez, működtetéséhez és kezeléséhez. A határok közötti kapcsolat megtervezése megkezdődik, ha a dinamikus útválasztást a Border Gateway Protocol (BGP) használatával szeretné használni. Ehhez hozzá kell rendelni egy 16 bites BGP autonóm rendszerszámot (nyilvános vagy privát) vagy statikus útválasztást, ahol a rendszer statikus alapértelmezett útvonalat rendel hozzá a szegélyhez tartozó eszközökhöz.

Az Edge-kapcsolókhoz a fizikai interfészeken konfigurált, pont – pont típusú IP-címekkel (/30 hálózatokkal) rendelkező 3. rétegbeli kapcsolatok szükségesek. A 2. rétegbeli, Azure Stack műveleteket támogató Edge-kapcsolók nem támogatottak. 

### <a name="bgp-routing"></a>BGP-Útválasztás

Egy dinamikus útválasztási protokoll, például a BGP garantálja, hogy a rendszer mindig tisztában van a hálózati változásokkal, és megkönnyíti a felügyeletet. A fokozott biztonság érdekében előfordulhat, hogy a rendszer jelszót állít be a BGP-társhoz a szegély és a szegély között.

Az alábbi ábrán látható módon a TOR-kapcsolón lévő magánhálózati IP-terület hirdetése le van tiltva egy előtag-lista használatával. Az előtag lista megtagadja a magánhálózat hirdetményét, és a TOR és a peremhálózat közötti kapcsolaton útvonal-térképként alkalmazza.

A szoftveres Load Balancer (SLB), amely a Azure Stack megoldási partnereken belül fut a TOR-eszközökön, így dinamikusan képes reklámozni a VIP-címeket.

Annak biztosítása érdekében, hogy a felhasználói forgalom azonnal és transzparens módon helyreállítható legyen a meghibásodástól, a TOR-eszközök között konfigurált VPC vagy MLAG lehetővé teszi a többplatformos kapcsolatok összesítésének használatát a gazdagépeken és a HSRP, illetve a VRRP, amelyek hálózati redundanciát biztosítanak az IP-hálózatokhoz.


### <a name="static-routing"></a>Statikus útválasztás

A statikus útválasztáshoz további konfiguráció szükséges a szegély eszközeihez. További manuális beavatkozásra és felügyeletre, valamint a változás előtt alapos elemzésre van szükség. A konfigurációs hiba által okozott problémák több időt vehetnek igénybe a módosításoktól függően. Ez az útválasztási módszer nem ajánlott, de támogatott.

Ha statikus útválasztást használó hálózati környezetbe szeretné integrálni a Azure Stackt, akkor mind a szegély, mind a peremhálózati eszköz közötti négy fizikai kapcsolatnak csatlakoztatva kell lennie. A statikus útválasztás működése miatt nem garantálható a magas rendelkezésre állás.

A szegélyt tartalmazó eszközt statikus útvonalakkal kell konfigurálni, amelyek az Edge és a szegély közötti négy P2P IP-cím egyikére mutatnak, a Azure Stackon belüli hálózatra irányuló forgalomra vonatkozóan, de csak a külső vagy a nyilvános VIP-hálózat szükséges a működéshez. Az első telepítéshez statikus útvonalak szükségesek a BMC-hez és a külső hálózatokhoz. A kezelők dönthetnek úgy, hogy statikus útvonalakat hagynak a szegélyben a BMC-ben és az infrastruktúra-hálózaton található felügyeleti erőforrások eléréséhez. Nem kötelező statikus útvonalakat hozzáadni az infrastruktúra és a Switch felügyeleti hálózatok váltásához.

A TOR-eszközök statikus alapértelmezett útvonalon vannak konfigurálva, és a teljes adatforgalmat a szegély eszközeire küldik. Az alapértelmezett szabály alóli egyetlen forgalmi kivétel a privát területre vonatkozik, amely le van tiltva a TOR-ra vonatkozó Access Control lista használatával.

A statikus útválasztás csak az Edge és a Border kapcsolók közötti kapcsolatokra vonatkozik.
A BGP dinamikus útválasztás az állványon belül van használatban, mivel ez nélkülözhetetlen eszköz a SLB és más összetevők számára, és nem tiltható le és nem távolítható el.


\*Az üzembe helyezés után a BMC-hálózat nem kötelező.

\*\*A kapcsoló-infrastruktúra hálózata nem kötelező, mivel a teljes hálózat belefoglalható a váltási felügyeleti hálózatba.

\*\*\*A kapcsoló-felügyeleti hálózatot kötelező megadni, és a kapcsoló infrastruktúra hálózata külön is felvehető.

### <a name="transparent-proxy"></a>Transzparens proxy

Ha az adatközpontban az összes forgalom proxy használatára van szüksége, egy transzparens proxyt kell konfigurálnia az állványról a szabályzatnak megfelelően kezelendő összes forgalom feldolgozásához, a hálózati zónák közötti forgalom elkülönítésével.

A Azure Stack megoldás nem támogatja a normál webproxykat 


Egy transzparens proxy (más néven lehallgatás, beágyazott vagy kényszerített proxy) elfogja a normál kommunikációt a hálózati rétegben anélkül, hogy speciális ügyfél-konfigurációra lenne szükség. Az ügyfeleknek nem kell megismerniük a proxy létezését.



Az SSL-forgalom elfogása nem támogatott, és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. Az identitáshoz szükséges végpontokkal folytatott kommunikáció maximális támogatott időtúllépése 60 – 3 újrapróbálkozási kísérlet.

## <a name="dns"></a>DNS

Ez a szakasz a tartománynévrendszer (DNS) konfigurációját ismerteti.


### <a name="configure-conditional-dns-forwarding"></a>Feltételes DNS-továbbítás konfigurálása

Ez csak egy AD FS üzemelő példányra vonatkozik. 

Ha engedélyezni szeretné a névfeloldást a meglévő DNS-infrastruktúrával, konfigurálja a feltételes továbbítást.

Feltételes továbbító hozzáadásához a privilegizált végpontot kell használnia.

Ehhez az eljáráshoz használjon olyan számítógépet az adatközpont-hálózaton, amely képes kommunikálni a rendszerjogosultságú végponttal Azure Stack.

1.  Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a privilegizált végpont IP-címéhez. Használja a hitelesítő adatokat a CloudAdmin-hitelesítéshez.

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```
    

2.  Miután kapcsolódott a Kiemelt végponthoz, futtassa a következő PowerShell-parancsot. Helyettesítse be a használni kívánt DNS-kiszolgálók tartománynevével és IP-címeivel megadott értékeket.

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Azure Stack DNS-nevek feloldása kívülről Azure Stack

A mérvadó kiszolgálók azok, amelyek a külső DNS-zóna információit és a felhasználó által létrehozott zónákat használják. Integrálhatja ezeket a kiszolgálókat a Zónák delegálása vagy a feltételes továbbítás engedélyezéséhez Azure Stack DNS-nevek feloldásához Azure Stack kívülről.

### <a name="get-dns-server-external-endpoint-information"></a>DNS-kiszolgáló külső végpontjának adatainak beolvasása

A Azure Stack üzembe helyezésének a DNS-infrastruktúrával való integrálásához a következő információkra lesz szüksége:

-   DNS-kiszolgálói teljes tartománynevek

-   DNS-kiszolgáló IP-címei

A Azure Stack DNS-kiszolgálókhoz tartozó teljes tartománynevek formátuma a következő:

*\<NAMINGPREFIX\>– ns01. \<REGION\> .\<EXTERNALDOMAINNAME\>*

*\<NAMINGPREFIX\>– ns02. \<REGION\> .\<EXTERNALDOMAINNAME\>*

A minták értékeit használva a DNS-kiszolgálók teljes tartománynevei a következők:

*azs-ns01.east.cloud.fabrikam.com*

*azs-ns02.east.cloud.fabrikam.com*

Ez az információ a felügyeleti portálon érhető el, de az összes Azure Stack-telepítés végén az *AzureStackStampInformation.json*nevű fájlban is létrejön.
Ez a fájl a központi telepítési virtuális gép *C: \\ CloudDeployment \\ logs*   mappájában található. Ha nem biztos abban, hogy milyen értékeket használt a Azure Stack központi telepítéshez, itt érheti el az értékeket.

Ha az üzembe helyezési virtuális gép már nem érhető el vagy nem érhető el, az értékeket az emelt szintű végponthoz való csatlakozással és a *Get-AzureStackStampInformation PowerShell-* parancsmag futtatásával szerezheti be   . További információ: privilegizált végpont.

### <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Az Azure Stackre történő feltételes továbbítás beállítása

A Azure Stack és a DNS-infrastruktúra integrálásának legegyszerűbb és legbiztonságosabb módja a zóna feltételes továbbítása a szülő zónát futtató kiszolgálóról. Ez a módszer akkor javasolt, ha a Azure Stack külső DNS-névtérhez tartozó szülő zónát üzemeltető DNS-kiszolgálókat közvetlen vezérléssel látja el.

Ha nem tudja, hogyan végezheti el a feltételes továbbítást a DNS-sel, tekintse meg a következő TechNet-cikket: feltételes továbbító kiosztása egy tartománynévhez vagy a DNS-megoldás dokumentációja.

Olyan esetekben, amikor a külső Azure Stack DNS-zónát úgy adta meg, hogy a vállalati tartománynévhez hasonló gyermektartomány legyen, a feltételes továbbítás nem használható. A DNS-delegálást konfigurálni kell.

Példa:

-   Vállalati DNS-tartománynév: *contoso.com*

-   Azure Stack külső DNS-tartomány neve: *azurestack.contoso.com*

### <a name="editing-dns-forwarder-ips"></a>DNS-továbbító IP-címeinek szerkesztése

A DNS-továbbító IP-címei a Azure Stack telepítése során állíthatók be. Ha azonban a továbbítói IP-címeket valamilyen okból frissíteni kell, az értékeket a privilegizált végponthoz való csatlakozással, valamint a *Get-AzSDnsForwarder*   és a *set-AzSDnsForwarder [[-IP_cím] \<IPAddress[]\> *PowerShell-   parancsmagok futtatásával módosíthatja. További információ: privilegizált végpont.

### <a name="delegating-the-external-dns-zone-to-azure-stack"></a>A külső DNS-zóna delegálása az Azure Stackbe

Ahhoz, hogy a DNS-nevek feloldhatók legyenek Azure Stack központi telepítésen kívülről, be kell állítania a DNS-delegálást.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztrátor DNS-kezelés lapján szerkessze az NS-rekordokat, és cserélje le a zóna NS rekordjait a Azure Stack.

A legtöbb DNS-regisztráló megköveteli, hogy legalább két DNS-kiszolgálót adjon meg a delegálás befejezéséhez.

### <a name="firewall"></a>Tűzfal

Azure Stack beállítja az infrastruktúra szerepköreihez tartozó virtuális IP-címeket (VIP).
Ezek a VIP-címek a nyilvános IP-címkészlet alapján vannak lefoglalva. A virtuális IP-címek egy hozzáférés-vezérlési listával (ACL) vannak védve a szoftveresen definiált hálózati rétegben. A rendszer az ACL-eket is használja a fizikai kapcsolókon (a-ben és a BMC-ban) a megoldás további megerősítése érdekében. A rendszer létrehoz egy DNS-bejegyzést a külső DNS-zóna minden olyan végpontja számára, amely a központi telepítés idején van megadva. A felhasználói portál például a portál DNS-gazdagépének bejegyzéséhez van rendelve. *\<region\>.\<fqdn\>*.

A következő építészeti ábrán a különböző hálózati rétegek és ACL-ek láthatók:

![az építészeti ábrán a különböző hálózati rétegek és ACL-ek láthatók](media/network-deployment/network-architecture.png) 

### <a name="ports-and-urls"></a>Portok és URL-címek

Ahhoz, hogy Azure Stack szolgáltatásokat (például a portálok, a Azure Resource Manager, a DNS stb.) elérhetővé kell tenni a külső hálózatok számára, engedélyeznie kell a bejövő forgalmat a végpontok számára adott URL-címek, portok és protokollok esetén.

Egy olyan üzemelő példányban, ahol egy transzparens proxy egy hagyományos proxykiszolgálóhoz vagy egy tűzfallal védi a megoldást, engedélyeznie kell a bejövő és a kimenő kommunikációhoz megadott portokat és URL-címeket. Ezek közé tartoznak az identitáshoz tartozó portok és URL-címek, a piactér, a javítások és a frissítés, a regisztrálás és a használati adatok.

### <a name="outbound-communication"></a>Kimenő kommunikáció

A Azure Stack csak transzparens proxykiszolgálók használatát támogatja. Egy transzparens proxyval rendelkező üzemelő példányban, amely egy hagyományos proxykiszolgálóhoz csatlakozik, engedélyeznie kell a portok és URL-címek használatát az alábbi táblázatban a kimenő kommunikációhoz a csatlakoztatott módban való üzembe helyezéskor.

Az SSL-forgalom elfogása nem támogatott, és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. Az identitáshoz szükséges végpontokkal folytatott kommunikáció maximális támogatott időtúllépése 60-as.

>[!NOTE] 
>Azure Stack nem támogatja a ExpressRoute használatát az alábbi táblázatban felsorolt Azure-szolgáltatások eléréséhez, mert előfordulhat, hogy a ExpressRoute nem tudja átirányítani a forgalmat az összes végpontra.      


|Cél|Cél URL-címe|Protokoll|Portok|Forrásoldali hálózat|
|---------|---------|---------|---------|---------|
|Identitás|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https: \/ /Management.Core.Windows.net<br>ARMUri = https: \/ /Management.Azure.com<br>https: \/ / \* . msftauth.net<br>https: \/ / \* . msauth.net<br>https: \/ / \* . msocdn.com<br>**Azure Government**<br>https: \/ /login.microsoftonline.us/<br>https: \/ /Graph.Windows.net/<br>**Azure China 21Vianet**<br>https: \/ /login.chinacloudapi.cn/<br>https: \/ /Graph.chinacloudapi.cn/<br>**Azure Germany**<br>https: \/ /login.microsoftonline.de/<br>https: \/ /Graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
|Piactéri hírszolgáltatás|**Azure**<br>https:\//management.azure.com<br>https://&#42;. blob.core.windows.net<br>https://&#42;. azureedge.net<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.net/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn/<br>http://&#42;. blob.core.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Javítás & frissítés|https://&#42;. azureedge.net<br>https: \/ /aka.MS/azurestackautomaticupdate|HTTPS|443|Nyilvános VIP-/27|
|Regisztráció|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Használat|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Nyilvános VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br>https: \/ /www.microsoft.com/pkiops/CRL<br>https: \/ /www.microsoft.com/pkiops/certs<br>https: \/ /CRL.microsoft.com/PKI/CRL/Products<br>https: \/ /www.microsoft.com/PKI/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
|NTP|(Az üzemelő példányhoz megadott NTP-kiszolgáló IP-címe)|UDP|123|Nyilvános VIP-/27|
|DNS|(Az üzembe helyezéshez megadott DNS-kiszolgáló IP-címe)|TCP<br>UDP|53|Nyilvános VIP-/27|
|CRL|(URL-cím a CRL terjesztési pontok alatt a tanúsítványon)|HTTP|80|Nyilvános VIP-/27|
|LDAP|A Graph-integrációhoz megadott Active Directory erdő|TCP<br>UDP|389|Nyilvános VIP-/27|
|LDAP SSL|A Graph-integrációhoz megadott Active Directory erdő|TCP|636|Nyilvános VIP-/27|
|LDAP GC|A Graph-integrációhoz megadott Active Directory erdő|TCP|3268|Nyilvános VIP-/27|
|LDAP GC SSL|A Graph-integrációhoz megadott Active Directory erdő|TCP|3269|Nyilvános VIP-/27|
|AD FS|AD FS-integrációhoz megadott AD FS metaadat-végpont|TCP|443|Nyilvános VIP-/27|
|Diagnosztikai naplók gyűjtési szolgáltatása|Azure Storage által biztosított blob SAS URL-cím|HTTPS|443|Nyilvános VIP-/27|
|     |     |     |     |     |
                                                                                                                                                                
### <a name="inbound-communication"></a>Bejövő kommunikáció

Azure Stack-végpontok külső hálózatokra való közzétételéhez infrastruktúra-VIP-készlet szükséges. A *végpont (VIP)*   tábla megjeleníti az egyes végpontokat, a szükséges portot és a protokollt. A további erőforrás-szolgáltatókat, például az SQL-erőforrás-szolgáltatót igénylő végpontok esetében tekintse meg a konkrét erőforrás-szolgáltató telepítési dokumentációját.

A belső infrastruktúra-VIP-címek nincsenek felsorolva, mert nem szükségesek a közzétételi Azure Stack. A felhasználói VIP-EK dinamikusan vannak meghatározva, és a felhasználók maguk határozzák meg, és nem szabályozzák a Azure Stack operátort

>[!NOTE] 
>A IKEv2 VPN egy szabványos IPsec VPN-megoldás, amely az 500-es és 4500-as UDP-portot, valamint a 50-as TCP-portot használja. A tűzfalak nem mindig nyitják meg ezeket a portokat, így előfordulhat, hogy az IKEv2 VPN nem tud áthaladni a proxykat és a tűzfalakat. 


|Végpont (VIP)|Rekord DNS-állomása|Protokoll|Portok|
|---------|---------|---------|---------|
|AD FS|ADFS. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Portál (rendszergazda)|Adminportal. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Adminhosting | *. adminhosting. \<region> .\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (rendszergazda)|Adminmanagement. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Portál (felhasználó)|Portál. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Azure Resource Manager (felhasználó)|Felügyeleti. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Graph|Graph. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Tanúsítvány-visszavonási lista|CRL.* &lt; region>. &lt; teljes tartománynév>*|HTTP|80|
|DNS|&#42;. * &lt; régió>. &lt; teljes tartománynév>*|TCP & UDP|53|
|Hosting | *. hosting. \<region> .\<fqdn> | HTTPS | 443 |
|Key Vault (felhasználó)|&#42;. Vault. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Key Vault (rendszergazda)|&#42;. adminvault. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|443|
|Tárolási üzenetsor|&#42;. üzenetsor. * &lt; régió>. &lt; teljes tartománynév>*|HTTP<br>HTTPS|80<br>443|
|Storage-tábla|&#42;. table. * &lt; régió>. &lt; teljes tartománynév>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;. blob. * &lt; régió>. &lt; teljes tartománynév>*|HTTP<br>HTTPS|80<br>443|
|SQL erőforrás-szolgáltató|sqladapter.dbadapter. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|44300-44304|
|MySQL erőforrás-szolgáltató|mysqladapter.dbadapter. * &lt; régió>. &lt; teljes tartománynév>*|HTTPS|44300-44304|
|App Service|&#42;. appservice. * &lt; régió>. &lt; teljes tartománynév>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;. SCM. appservice. * &lt; régió>. &lt; teljes tartománynév>*|TCP|443 (HTTPS)|
|  |API. appservice. * &lt; régió>. &lt; teljes tartománynév>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |FTP. appservice. * &lt; régió>. &lt; teljes tartománynév>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-átjárók|     |     |[Lásd: VPN Gateway – gyakori kérdések](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

