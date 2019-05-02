---
title: Az Azure Stack adatközpont integrációja – végpontok közzététele |} A Microsoft Docs
description: A helyi adatközpontban Azure Stack-végpontok közzététele
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 1f87633568a5a66c016b53f062f11ca733730092
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985227"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Az Azure Stack adatközpont integrációja – végpontok közzététele

Az Azure Stack állít be az infrastruktúra-szerepkör virtuális IP-címek (VIP). A virtuális IP-címek vannak lefoglalva a nyilvános IP-címkészletből. Minden egyes virtuális IP-CÍMEK hozzáférés-vezérlési lista (ACL) a szoftveresen definiált hálózati rétegben védi. Hozzáférés-vezérlési listák is használhatók a fizikai kapcsolón (szűrése és BMC) tovább erősíti a megoldást. DNS-bejegyzés jön létre a külső DNS-zónában, hogy az üzembe helyezéskor meghatározott végpontot.


A következő architekturális diagram látható a különböző hálózati rétegek és ACL-ek:

![Szerkezeti kép](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

Infrastruktúra készlete virtuális IP-cím megadása kötelező közzététele az Azure Stack-végpontok külső hálózatokhoz. A *végpontot (VIP)* táblázat minden egyes végpontot, a szükséges port és protokoll. Tekintse meg az adott erőforrás szolgáltató telepítési dokumentációban további erőforrás-szolgáltatók, például az SQL erőforrás-szolgáltató igénylő végpontok.

Virtuális IP-címek nem jelennek meg, mert azok még nem szükséges a közzététel az Azure Stack belső infrastruktúra.

> [!Note]  
> Felhasználói virtuális IP-címek dinamikusak, maguk a felhasználók számára nem az Azure Stack operátorait szerint határozza meg.

> [!Note]
> Kezdődően a 1811 frissítés 12495-30015 tartományán portok már nem szükséges miatt is nyitva a [bővítmény gazdagép](azure-stack-extension-host-prepare.md).

|Endpoint (VIP)|DNS host A record|Protocol|Portok|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (rendszergazda)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Az Azure Resource Manager (rendszergazda)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (felhasználó)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Az Azure Resource Manager (felhasználó)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Visszavont tanúsítványok listája|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|A TCP ÉS UDP|53|
|Üzemeltetés | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|A Key Vault (felhasználó)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|A Key Vault (rendszergazda)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tárolási üzenetsor|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage-táblából|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-erőforrásszolgáltató|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL-erőforrásszolgáltató|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172-es (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (az azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-átjárók|     |     |[Tekintse meg a VPN gateway – gyakori kérdések](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

Az Azure Stack csak transzparens proxy kiszolgálók támogatja. A központi telepítés, ha egy transzparens proxy kimenő portokhoz hagyományos proxykiszolgálónak engedélyeznie kell a következő portokat és URL-címek a kimenő kommunikáció:

> [!Note]  
> Az Azure Stack nem támogatja az alábbi táblázatban felsorolt Azure-szolgáltatások eléréséhez ExpressRoute-tal.

|Cél|Destination URL|Protocol|Portok|Forráshálózat|
|---------|---------|---------|---------|---------|
|Identitás|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|Nyilvános virtuális IP - / 27-eset<br>Nyilvános infrastruktúra hálózati|
|Marketplace-en szindikálás|https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|Nyilvános virtuális IP - / 27-eset|
|Javítás & frissítése|https://&#42;.azureedge.net|HTTPS|443|Nyilvános virtuális IP - / 27-eset|
|Regisztráció|https:\//management.azure.com|HTTPS|443|Nyilvános virtuális IP - / 27-eset|
|Használat|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|Nyilvános virtuális IP - / 27-eset|
|Windows Defender|\*.wdcp.microsoft.com<br>\*.wdcpalt.microsoft.com<br>\*.wd.microsoft.com<br>\*.update.microsoft.com<br>\*. jövőben a Microsoft<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Nyilvános virtuális IP - / 27-eset<br>Nyilvános infrastruktúra hálózati|
|NTP|(IP az NTP-kiszolgáló a megadott központi telepítés)|UDP|123|Nyilvános virtuális IP - / 27-eset|
|DNS|(IP-megadott DNS-kiszolgáló üzembe helyezéshez)|TCP<br>UDP|53|Nyilvános virtuális IP - / 27-eset|
|CRL|(A tanúsítvány CRL terjesztési pontok alapján URL)|HTTP|80|Nyilvános virtuális IP - / 27-eset|
|LDAP|Graph-integráció a megadott Active Directory-erdő|TCP<br>UDP|389|Nyilvános virtuális IP - / 27-eset|
|LDAP SSL|Graph-integráció a megadott Active Directory-erdő|TCP|636|Nyilvános virtuális IP - / 27-eset|
|LDAP GC|Graph-integráció a megadott Active Directory-erdő|TCP|3268|Nyilvános virtuális IP - / 27-eset|
|LDAP GC SSL|Graph-integráció a megadott Active Directory-erdő|TCP|3269|Nyilvános virtuális IP - / 27-eset|
|AD FS|Az AD FS metaadatok végpontja biztosított az AD FS-integráció|TCP|443|Nyilvános virtuális IP - / 27-eset|
|     |     |     |     |     |

> [!Note]  
> Kimenő URL-címek az Azure traffic Managerrel a földrajzi hely alapján a legjobb lehetséges csatlakozási elosztott terhelésű rendszer. Az elosztott terhelésű URL-címek betöltése, a Microsoft update, és a háttéralkalmazás végpontjainak módosítása ügyfelek befolyásolása nélkül. A Microsoft nem oszt meg IP-címek listája az elosztott terhelésű URL-címek számára. Használjon olyan eszköz, amely támogatja a szűrést, URL-cím helyett IP-cím alapján.

## <a name="next-steps"></a>További lépések

[Az Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md)
