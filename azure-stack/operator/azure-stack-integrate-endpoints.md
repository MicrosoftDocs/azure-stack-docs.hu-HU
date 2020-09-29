---
title: Azure Stack hub-szolgáltatások közzététele az adatközpontban
description: Ismerje meg, hogyan tehet közzé Azure Stack hub-szolgáltatásokat az adatközpontjában.
author: myoung
ms.topic: article
ms.date: 09/24/2020
ms.author: v-myoung
ms.reviewer: wamota
ms.lastreviewed: 09/24/2020
ms.openlocfilehash: ed9f2a8eeeeb2cfdfcc4e96c1672d7da8fff64b2
ms.sourcegitcommit: 703be61f2f1565bf478b8c184753869c29e5c33c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91495745"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Azure Stack hub-szolgáltatások közzététele az adatközpontban

Azure Stack hub beállítja az infrastruktúra szerepköreihez tartozó virtuális IP-címeket (VIP). Ezek a VIP-címek a nyilvános IP-címkészlet alapján vannak lefoglalva. A virtuális IP-címek egy hozzáférés-vezérlési listával (ACL) vannak védve a szoftveresen definiált hálózati rétegben. A rendszer az ACL-eket is használja a fizikai kapcsolókon (a-ben és a BMC-ban) a megoldás további megerősítése érdekében. A rendszer létrehoz egy DNS-bejegyzést a külső DNS-zóna minden olyan végpontja számára, amely a központi telepítés idején van megadva. A felhasználói portál például a portál DNS-gazdagépének bejegyzéséhez van rendelve. * &lt; régió>. &lt; FQDN>*.

A következő építészeti ábrán a különböző hálózati rétegek és ACL-ek láthatók:

![Különböző hálózati rétegeket és ACL-eket bemutató diagram](media/azure-stack-integrate-endpoints/integrate-endpoints-01.svg)

## <a name="ports-and-urls"></a>Portok és URL-címek

Ahhoz, hogy a külső hálózatok számára elérhetővé váljon Azure Stack hub-szolgáltatások (például a portálok, a Azure Resource Manager, a DNS stb.), engedélyeznie kell a bejövő forgalmat a végpontok számára adott URL-címek, portok és protokollok esetén.

Egy olyan üzemelő példányban, ahol egy transzparens proxy egy hagyományos proxykiszolgálóhoz vagy egy tűzfallal védi a megoldást, engedélyeznie kell a [bejövő](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) és a [kimenő](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) kommunikációhoz megadott portokat és URL-címeket. Ezek közé tartoznak az identitáshoz tartozó portok és URL-címek, a piactér, a javítások és a frissítés, a regisztrálás és a használati adatok.

Az SSL-forgalom elfogása [nem támogatott](azure-stack-firewall.md#ssl-interception) , és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. 

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

A Azure Stack hub-végpontok külső hálózatokra való közzétételéhez infrastruktúra-VIP-készlet szükséges. A *végpont (VIP)* tábla megjeleníti az egyes végpontokat, a szükséges portot és a protokollt. A további erőforrás-szolgáltatókat, például az SQL-erőforrás-szolgáltatót igénylő végpontok esetében tekintse meg a konkrét erőforrás-szolgáltató telepítési dokumentációját.

A belső infrastruktúra VIP-címei nem szerepelnek a felsorolásban, mert nem szükségesek az Azure Stack hub közzétételéhez. A felhasználói VIP-EK dinamikusak, és a felhasználók maguk határozzák meg, és nem szabályozzák az Azure Stack hub-kezelőt.

> [!Note]  
> A IKEv2 VPN egy szabványos IPsec VPN-megoldás, amely az 500-es és 4500-as UDP-portot, valamint a 50-as TCP-portot használja. A tűzfalak nem mindig nyitják meg ezeket a portokat, így előfordulhat, hogy az IKEv2 VPN nem tud áthaladni a proxykat és a tűzfalakat.

A [bővítmény-gazdagép](azure-stack-extension-host-prepare.md)hozzáadásával a 12495-30015-es tartományba tartozó portok nem szükségesek.

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
|VPN-átjárók|     |     |[Lásd: VPN Gateway – gyakori kérdések](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

Azure Stack hub csak transzparens proxykiszolgálók használatát támogatja. Egy transzparens proxyval rendelkező üzemelő példányban, amely egy hagyományos proxykiszolgálóhoz csatlakozik, engedélyeznie kell a portok és URL-címek használatát a következő táblázatban a kimenő kommunikációhoz.

Az SSL-forgalom elfogása [nem támogatott](azure-stack-firewall.md#ssl-interception) , és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. Az identitáshoz szükséges végpontokkal folytatott kommunikáció maximális támogatott időtúllépése 60-as.

> [!Note]  
> Azure Stack hub nem támogatja a ExpressRoute használatát az alábbi táblázatban felsorolt Azure-szolgáltatások eléréséhez, mert előfordulhat, hogy a ExpressRoute nem tudja átirányítani a forgalmat az összes végpontra.

|Szerep|Cél URL-címe|Protokoll|Portok|Forrásoldali hálózat|
|---------|---------|---------|---------|---------|
|Identitás|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https: \/ /Management.Core.Windows.net<br>ARMUri = https: \/ /Management.Azure.com<br>https: \/ / \* . msftauth.net<br>https: \/ / \* . msauth.net<br>https: \/ / \* . msocdn.com<br>**Azure Government**<br>https: \/ /login.microsoftonline.us/<br>https: \/ /Graph.Windows.net/<br>**Azure China 21Vianet**<br>https: \/ /login.chinacloudapi.cn/<br>https: \/ /Graph.chinacloudapi.cn/<br>**Azure Germany**<br>https: \/ /login.microsoftonline.de/<br>https: \/ /Graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
|Piactéri hírszolgáltatás|**Azure**<br>https:\//management.azure.com<br>https://&#42;. blob.core.windows.net<br>https://&#42;. azureedge.net<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.net/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn/<br>http://&#42;. blob.core.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Javítás & frissítés|https://&#42;. azureedge.net<br>https: \/ /aka.MS/azurestackautomaticupdate|HTTPS|443|Nyilvános VIP-/27|
|Regisztráció|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Használat|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Nyilvános VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br><br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
|NTP|(Az üzemelő példányhoz megadott NTP-kiszolgáló IP-címe)|UDP|123|Nyilvános VIP-/27|
|DNS|(Az üzembe helyezéshez megadott DNS-kiszolgáló IP-címe)|TCP<br>UDP|53|Nyilvános VIP-/27|
|SYSLOG|(Az üzembe helyezéshez megadott SYSLOG-kiszolgáló IP-címe)|TCP<br>UDP|6514<br>514|Nyilvános VIP-/27|
|CRL|(URL-cím a CRL terjesztési pontok alatt a tanúsítványon)<br>http://crl.microsoft.com/pki/crl/products<br>http://mscrl.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pki/certs<br>http://www.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>|HTTP|80|Nyilvános VIP-/27|
|LDAP|A Graph-integrációhoz megadott Active Directory erdő|TCP<br>UDP|389|Nyilvános VIP-/27|
|LDAP SSL|A Graph-integrációhoz megadott Active Directory erdő|TCP|636|Nyilvános VIP-/27|
|LDAP GC|A Graph-integrációhoz megadott Active Directory erdő|TCP|3268|Nyilvános VIP-/27|
|LDAP GC SSL|A Graph-integrációhoz megadott Active Directory erdő|TCP|3269|Nyilvános VIP-/27|
|AD FS|AD FS-integrációhoz megadott AD FS metaadat-végpont|TCP|443|Nyilvános VIP-/27|
| Diagnosztikai naplók gyűjteménye |https://*. blob. Core. Windows. net<br>https://azsdiagprdlocalwestus02.blob.core.windows.net<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com | HTTPS | 443 | Nyilvános VIP-/27 |
|     |     |     |     |     |

A kimenő URL-címek terheléselosztása az Azure Traffic Managerrel történik, hogy a lehető legjobb kapcsolatot biztosítsa a földrajzi hely alapján. Elosztott terhelésű URL-címek esetén a Microsoft a háttérbeli végpontokat a felhasználók befolyásolása nélkül tudja frissíteni és módosítani. A Microsoft nem osztja meg a terheléselosztási URL-címek IP-címeinek listáját. Használjon olyan eszközt, amely támogatja az URL-címek szerinti szűrést, nem pedig az IP-címet.

A kimenő DNS-t mindig kötelező megadni; a változó a külső DNS lekérdezési forrását, valamint az identitás-integráció típusát választotta. Egy csatlakoztatott forgatókönyv esetén a BMC-hálózaton található DVM kimenő hozzáférésre van szüksége. Az üzembe helyezést követően azonban a DNS szolgáltatás olyan belső összetevőre kerül, amely nyilvános VIP-en keresztül küld lekérdezéseket. Ekkor a BMC-hálózaton keresztüli kimenő DNS-hozzáférés el lehet távolítani, de a DNS-kiszolgálóhoz való nyilvános VIP-hozzáférésnek továbbra is meg kell maradnia, vagy más hitelesítés sikertelen lesz.

## <a name="next-steps"></a>További lépések

[Azure Stack hub PKI-követelményei](azure-stack-pki-certs.md)
