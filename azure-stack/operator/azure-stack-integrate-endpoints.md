---
title: Azure Stack Datacenter-integráció – végpontok közzététele | Microsoft Docs
description: Ismerje meg, hogyan tehet közzé Azure Stack-végpontokat az adatközpontban
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/30/2019
ms.author: justinha
ms.reviewer: wamota
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 7b8bae02fdb3f85b856f6ccdb9d90155e6bde768
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188357"
---
# <a name="azure-stack-datacenter-integration---publish-azure-stack-services"></a>Azure Stack Datacenter-integráció – Azure Stack szolgáltatások közzététele

Azure Stack beállítja az infrastruktúra szerepköreihez tartozó virtuális IP-címeket (VIP). Ezek a VIP-címek a nyilvános IP-címkészlet alapján vannak lefoglalva. A virtuális IP-címek egy hozzáférés-vezérlési listával (ACL) vannak védve a szoftveresen definiált hálózati rétegben. A rendszer az ACL-eket is használja a fizikai kapcsolókon (a-ben és a BMC-ban) a megoldás további megerősítése érdekében. A rendszer létrehoz egy DNS-bejegyzést a külső DNS-zóna minden olyan végpontja számára, amely a központi telepítés időpontjában meg van adva. A felhasználói portál például a portál DNS-gazdagépének bejegyzéséhez van rendelve. *régió >.&lt; &lt; FQDN >* .

A következő építészeti ábrán a különböző hálózati rétegek és ACL-ek láthatók:

![Szerkezeti kép](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

### <a name="ports-and-urls"></a>Portok és URL-címek
Ahhoz, hogy Azure Stack szolgáltatásokat (például a portálokat, a Azure Resource Managereket, a DNS-t stb.) elérhetővé tegye a külső hálózatok számára, engedélyeznie kell a bejövő forgalmat a végpontok számára adott URL-címek, portok és protokollok esetén.
 
Egy olyan üzemelő példányban, ahol egy transzparens proxy egy hagyományos proxykiszolgálóhoz vagy egy tűzfallal védi a megoldást, engedélyeznie kell a [bejövő](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) és a [kimenő](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) kommunikációhoz megadott portokat és URL-címeket. Ezek közé tartoznak az identitáshoz tartozó portok és URL-címek, a piactér, a javítások és a frissítés, a regisztrálás és a használati adatok.

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

Azure Stack-végpontok külső hálózatokra való közzétételéhez infrastruktúra-VIP-készlet szükséges. A *végpont (VIP)* tábla megjeleníti az egyes végpontokat, a szükséges portot és a protokollt. A további erőforrás-szolgáltatókat, például az SQL-erőforrás-szolgáltatót igénylő végpontok esetében tekintse meg a konkrét erőforrás-szolgáltató telepítési dokumentációját.

A belső infrastruktúra-VIP-címek nincsenek felsorolva, mert nem szükségesek a közzétételi Azure Stack. A felhasználó virtuális IP-címei dinamikusan vannak meghatározva, és a felhasználók maguk határozzák meg, hogy a Azure Stack operátor nem rendelkezik hozzáféréssel.

> [!Note]  
> A IKEv2 VPN egy szabványos IPsec VPN-megoldás, amely az 500-es és 4500-as UDP-portot, valamint a 50-as TCP-portot használja. A tűzfalak nem mindig nyitják meg ezeket a portokat, így előfordulhat, hogy egy IKEv2 VPN-kapcsolat nem tud áthaladni a proxykat és a tűzfalakat.

A [bővítmény-gazdagép](azure-stack-extension-host-prepare.md)hozzáadásával a 12495-30015-es tartományba tartozó portok nem szükségesek.

|Végpont (VIP)|DNS host A record|Protocol|Portok|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (rendszergazda)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (rendszergazda)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (felhasználó)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (felhasználó)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tanúsítvány-visszavonási lista|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP & UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (felhasználó)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (rendszergazda)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tárolási üzenetsor|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage-tábla|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-erőforrásszolgáltató|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL-erőforrásszolgáltató|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-átjárók|     |     |[Lásd: VPN Gateway – gyakori kérdések](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

A Azure Stack csak transzparens proxykiszolgálók használatát támogatja. Egy transzparens proxyval rendelkező üzemelő példányban, amely egy hagyományos proxykiszolgálóhoz csatlakozik, engedélyeznie kell a portok és URL-címek használatát a következő táblázatban a kimenő kommunikációhoz.

> [!Note]  
> A Azure Stack nem támogatja a ExpressRoute használatát az alábbi táblázatban felsorolt Azure-szolgáltatások eléréséhez.

|Cél|Destination URL|Protocol|Portok|Forráshálózat|
|---------|---------|---------|---------|---------|
|Identitás|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//Graph.Windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//Graph.chinacloudapi.cn/<br>**Azure Germany**<br>https:\//login.microsoftonline.de/<br>https:\//Graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
|Piactéri hírszolgáltatás|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//Management.usgovcloudapi.net/<br>https://&#42;. blob.Core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//Management.chinacloudapi.cn/<br>http://&#42;. blob.Core.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Javítás & frissítés|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Nyilvános VIP-/27|
|Regisztráció|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//Management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//Management.chinacloudapi.cn|HTTPS|443|Nyilvános VIP-/27|
|Használat|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Nyilvános VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br>https:\//www.microsoft.com/pkiops/CRL<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//CRL.microsoft.com/PKI/CRL/Products<br>https:\//www.microsoft.com/PKI/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Nyilvános VIP-/27<br>Nyilvános infrastruktúra hálózata|
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

A kimenő URL-címek terheléselosztása az Azure Traffic Manager használatával történik a lehető legjobb kapcsolat biztosításához a földrajzi hely alapján. Elosztott terhelésű URL-címek esetén a Microsoft a háttérbeli végpontokat az ügyfelek befolyásolása nélkül tudja frissíteni és módosítani. A Microsoft nem osztja meg a terheléselosztási URL-címek IP-címeinek listáját. Olyan eszközt kell használnia, amely támogatja az URL-címeken alapuló szűrést, nem pedig az IP-címet.

A kimenő DNS-t mindig kötelező megadni, ami változó a külső DNS lekérdezésének forrását, valamint azt, hogy milyen típusú identitás-integráció lett kiválasztva. Ha ez egy csatlakoztatott forgatókönyv, az üzembe helyezés során a BMC-hálózaton található DVM-nek szüksége van a kimenő hozzáférésre, de az üzembe helyezés után a DNS-szolgáltatás egy belső összetevőbe kerül, amely nyilvános VIP-en keresztül küld lekérdezéseket. Ekkor a rendszer eltávolítja a kimenő DNS-hozzáférést a BMC-hálózaton keresztül, de a DNS-kiszolgálóhoz való nyilvános VIP-hozzáférésnek továbbra is meg kell maradnia, vagy más hitelesítés sikertelen lesz.

## <a name="next-steps"></a>További lépések

[PKI-követelmények Azure Stack](azure-stack-pki-certs.md)
