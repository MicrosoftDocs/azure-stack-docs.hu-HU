---
title: Az Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványokra vonatkozó követelményei az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Az Azure Stack PKI tanúsítvány központi telepítésére vonatkozó követelmények az Azure Stackkel integrált rendszereket ismerteti.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: e51dd3a467bcba9eaf36fc729e37780cf7b626dd
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985085"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Az Azure Stack nyilvános kulcsokra épülő infrastruktúra tanúsítványkövetelmények

Az Azure Stack egy nyilvános infrastruktúra-hálózaton kívülről hozzáférhető nyilvános IP-címek használatával Azure Stack-szolgáltatásokat, és esetleg a bérlői virtuális gépeknek egy kis készletét rendelt rendelkezik. Azure Stack üzembe helyezése során a megfelelő DNS-nevek Azure Stack-infrastruktúra nyilvános végpontokkal rendelkező PKI-tanúsítványok szükségesek. Ez a cikk nyújt tájékoztatást:

- Milyen tanúsítványokra szükség az Azure Stack üzembe helyezése
- A folyamat az adott a specifikációknak megfelelő tanúsítványok beszerzése
- Készítse elő, ellenőrizze és használja a tanúsítványok központi telepítése során

> [!Note]  
> Üzembe helyezés során tanúsítványokat kell másolnia a telepítési mappát, amely megfelel az identitásszolgáltató alapján (az Azure AD vagy az AD FS) telepítésekor. Összes végponthoz egyetlen tanúsítványt használ, ha a tanúsítványfájl egyes központi telepítési mappába, az alábbi táblázatokban látható módon kell másolnia. A mappastruktúra a központi telepítés virtuális gépen előregyártott és tekinthet meg: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények
Az alábbi lista ismerteti a tanúsítványokra vonatkozó követelményeket, melyek szükségesek ahhoz, hogy az Azure Stack üzembe helyezése: 
- Egy belső hitelesítésszolgáltató vagy egy nyilvános hitelesítésszolgáltató kell kiállítaniuk. Nyilvános hitelesítésszolgáltató használata esetén, szerepelnie kell az alap operációs rendszer lemezképét, a Microsoft megbízható legfelső szintű hatóság Program részeként. A teljes listáját itt találja: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Az Azure Stack-infrastruktúra a hitelesítésszolgáltató visszavont tanúsítványok listája (CRL) helyre a tanúsítványt a közzétett hálózati hozzáféréssel kell rendelkeznie. A CRL-t egy http-végpontot kell lennie.
- Ha a tanúsítványok elforgatása előtti-1903 épít, tanúsítványokat kell lennie, vagy ki az üzembe helyezés vagy bármely nyilvános hitelesítésszolgáltatótól a fent megadott tanúsítványok aláírásához használt azonos belső hitelesítésszolgáltatótól származó. 1903 és azt követő tanúsítványok adhatnak ki minden olyan vállalati vagy nyilvános hitelesítésszolgáltatótól.
- Önaláírt tanúsítványok használata nem támogatottak.
- A rendszerbe állítás és az elforgatás választhatja a tanúsítvány tulajdonos neve és a tulajdonos alternatív nevére (SAN) mezőben minden neve szóközt kiterjedő egyetlen tanúsítványt használjon, vagy használhat egyéni tanúsítványokat az egyes névterek alatt látható, az Azure Stack szükséges szolgáltatások szeretné használják. Mindkét módszerénél a helyettesítő karakterek használatával végpontok, ahol azok szükség, mint például szükséges **KeyVault** és **KeyVaultInternal**. 
- A tanúsítvány PFX-titkosítást kell lennie a 3DES. 
- A tanúsítvány-aláírási algoritmus SHA1 lehet. 
- A tanúsítvány formátuma PFX, kell lennie, mint a nyilvános és titkos kulcsok szükségesek az Azure Stack telepítéséhez. A titkos kulccsal kell rendelkeznie a helyi gép kulcsattribútum beállítása.
- A PFX-titkosítás 3DES (Ez az alapértelmezett Windows 10-es ügyfél vagy a Windows Server 2016 tanúsítványtároló exportálásakor) kell lennie.
- A tanúsítvány pfx-fájlokat a "Kulcs használata" mező a rendelkeznie kell egy értéket "Digitális aláírás" és "KeyEncipherment".
- A tanúsítvány pfx-fájlok a "Kibővített kulcshasználat" mezőben kell rendelkeznie az "Kiszolgálói hitelesítés (1.3.6.1.5.5.7.3.1)" és "Ügyfél-hitelesítés (1.3.6.1.5.5.7.3.2)" értéket.
- A tanúsítvány "kiadott:" mező nem lehet ugyanaz, mint a "kiállító:" mező.
- A jelszavakat, hogy az összes tanúsítvány pfx-fájlok azonosnak kell lennie, a telepítéshez
- A tanúsítvány PFX-fájlba a jelszó nem lehet egy összetett jelszót. Hozzon létre egy jelszót, amely megfelel a következő jelszó összetettségi követelményeknek. A minimális hossza nyolc karakternél. A jelszó tartalmazza a következők közül legalább háromból: nagybetűs levél, kisbetű, számok, a 0-9, különleges karakterek nem nagybetűssé vagy kisbetűssé nagybetű. Jegyezze meg ezt a jelszót. Üzembehelyezési paraméterként alkalmaznak.
- Ellenőrizze, hogy a neveket és alternatív tulajdonosneveket a tulajdonos alternatív neve (x509v3_config) bővítmény egyezés található. A tulajdonos alternatív neve mezőben adja meg a további állomásnevek (webhelyek, IP-címek, köznapi nevek) egy SSL-tanúsítvány által védendő teszi lehetővé.

> [!NOTE]  
> Önkiszolgáló aláírt tanúsítvány használata nem támogatott.

> [!NOTE]  
> Egy tanúsítvány láncot-az-Megbízhatóságok IS a közvetítő hitelesítésszolgáltatók jelenléte támogatott. 

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Ez a szakasz a táblázat az Azure Stack nyilvános végpont nyilvános kulcsokra épülő infrastruktúra szükséges tanúsítványok mind az Azure ad és az AD FS az Azure Stack üzemelő példányok. Tanúsítvány követelményei a területen, valamint a használt névterek szerint vannak csoportosítva, és a tanúsítványokat, amelyek minden névtér esetében szükséges. A mappa, amelyben a megoldásszolgáltató másolja át a különböző tanúsítványok nyilvános végpontonként is ismerteti a táblázat. 

Az Azure Stack infrastruktúra nyilvános végpontot a megfelelő DNS-neveit tanúsítványokra szükség. Minden végpont DNS-név formátumban van kifejezve:  *&lt;előtag >.&lt; régió >. &lt;teljesen minősített tartományneve >*. 

Az üzembe helyezés a [régió] és [externalfqdn] értékeknek egyezniük kell a régiót és a külső tartománynév, az Azure Stack rendszerek számára is választott. Például, ha a régió neve *Redmond* és a külső tartományneve *contoso.com*, a DNS-nevek lenne a formátum *&lt;előtag >. redmond.contoso.com*. A  *&lt;előtag >* értékek vannak predesignated írja le a végpontot a tanúsítvány védi a Microsoft által. Emellett a  *&lt;előtag >* értékeket a külső infrastruktúra végpontok az Azure Stack szolgáltatás, amely az adott végponti függenek. 

> [!note]  
> Az éles környezetek esetében javasoljuk az egyes tanúsítványok jönnek létre, végpontok és a megfelelő könyvtárba másolja. Fejlesztési környezetek esetén tanúsítványokat is meg kell adni a tulajdonos és a tulajdonos alternatív nevére (SAN) mezőket, átmásolja az összes könyvtár összes névtér kiterjedő egyetlen helyettesítő tanúsítványt. Végpontok és a szolgáltatások egy tanúsítvány egy nem biztonságos helyzetét, ezért csak fejlesztési. Ne feledje, hogy mindkét lehetőség használatát írják elő helyettesítő tanúsítványok végpontok például **acs** és a Key Vault, amennyiben azok szükségesek. 

| Telepítési mappa | Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosneveket (SAN) | Hatókör (régiónként) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Nyilvános portálra | portal.&lt;region>.&lt;fqdn> | Portálok | &lt;region>.&lt;fqdn> |
| Felügyeleti portál | adminportal.&lt;region>.&lt;fqdn> | Portálok | &lt;region>.&lt;fqdn> |
| Azure Resource Manager-nyilvános | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Az Azure Resource Manager-rendszergazda | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) |  Belső Keyvault |  adminvault.&lt;region>.&lt;fqdn> |
| Admin Extension Host | *.adminhosting. \<régió >. \<teljesen minősített tartományneve > (altartományokra is kibővített SSL-tanúsítványok) | Admin Extension Host | adminhosting.\<region>.\<fqdn> |
| A bővítmény nyilvános állomás | *.hosting.\<region>.\<fqdn> (Wildcard SSL Certificates) | A bővítmény nyilvános állomás | hosting.\<region>.\<fqdn> |

Ha az Azure Stack az Azure ad-ben üzembe helyezési mód használatával telepíti, csak az előző táblázatban felsorolt tanúsítványokat kell. Azonban ha telepíti az AD FS üzembe helyezési mód használata az Azure Stack, is kérnie kell a tanúsítványokat, a következő táblázat ismerteti:

|Telepítési mappa|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosneveket (SAN)|Hatókör (régiónként)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> A jelen szakaszban felsorolt összes tanúsítvány ugyanazt a jelszót kell rendelkeznie. 

## <a name="optional-paas-certificates"></a>Nem kötelező PaaS-tanúsítványok
Ha azt tervezi, a további Azure Stack PaaS-szolgáltatások (SQL, a MySQL és az App Service) telepítése után az Azure Stack telepítése és beállítása megtörtént, meg kell kérnie ahhoz, hogy biztosítsák a PaaS-szolgáltatások végpontjait további tanúsítványokra. 

> [!IMPORTANT]
> A tanúsítványok, használja az App Service-ben, az SQL és a MySQL erőforrás-szolgáltatók a azonos legfelső szintű hitelesítésszolgáltató, mint a globális Azure Stack-végpontok van szükség. 

A következő táblázat ismerteti a végpontok és a szükséges az SQL- és MySQL-adapterek és az App Service-tanúsítványok. Nem kell ezeket a tanúsítványokat az Azure Stack üzembe helyezési mappába másolja. Ehelyett nyújtanak ezekről a tanúsítványokról további erőforrás-szolgáltató telepítésekor. 

|Hatókör (régiónként)|Tanúsítvány|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevek (SAN)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL- és MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Altartományokra is kibővített SSL-tanúsítvány)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítvány|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Altartományokra is kibővített SSL-tanúsítvány több tartomány<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> több helyettesítő alternatív tulajdonosnevek több tanúsítványt is igényel. Előfordulhat, hogy több altartományokra is kibővített San egyetlen tanúsítványt a nem támogatott nyilvános hitelesítésszolgáltatók által 

<sup>2</sup> A &#42;.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >* helyettesítő tanúsítvány nem használható helyett ezeket a tanúsítványokat (api.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, ftp.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, és sso.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*. Az App Service explicit módon a végpontok külön tanúsítványok használata szükséges. 

## <a name="learn-more"></a>Részletek
Ismerje meg, hogyan [készítése a PKI-tanúsítványokat az Azure Stack üzemelő példányához](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>További lépések
[Identitásintegráció](azure-stack-integrate-identity.md)

