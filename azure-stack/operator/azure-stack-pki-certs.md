---
title: Azure Stack a nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelményeket Azure Stack integrált rendszerek esetében | Microsoft Docs
description: Ismerteti a Azure Stack PKI-tanúsítvány telepítési követelményeit Azure Stack integrált rendszerek esetében.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 53d8e3daecba269bcdd21fc726e312758f1f6c6f
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902701"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>A nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelmények Azure Stack

Azure Stack rendelkezik egy nyilvános infrastruktúra-hálózattal, amely külső módon elérhető nyilvános IP-címeket használ, amelyek egy kis készlethez vannak rendelve Azure Stack szolgáltatásokhoz és esetleg bérlői virtuális gépekhez. Az Azure Stack üzembe helyezése során olyan PKI-tanúsítványokra van szükség, amelyek esetében meg vannak adva a megfelelő DNS-nevek az Azure Stack nyilvánosinfrastruktúra-végpontjaihoz. Ez a cikk a következő információkat tartalmazza:

- Milyen tanúsítványokra van szükség a Azure Stack telepítéséhez
- A specifikációknak megfelelő tanúsítványok beszerzésének folyamata
- A tanúsítványok előkészítése, ellenőrzése és használata az üzembe helyezés során

> [!NOTE]
> A Azure Stack alapértelmezés szerint a belső Active Directory integrált hitelesítésszolgáltatótól (CA) kibocsátott tanúsítványokat is használ a csomópontok közötti hitelesítéshez. A tanúsítvány érvényesítéséhez minden Azure Stack infrastruktúra-gép megbízhatónak tekinti a belső HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványát azáltal, hogy hozzáadja ezt a tanúsítványt a helyi tanúsítványtárolóhoz. Azure Stack nem rendelkezik tanúsítványok rögzítésével vagy engedélyezési listával. Az egyes kiszolgálói tanúsítványok SAN-ja a cél teljes tartománynevével van érvényesítve. A megbízhatósági láncot is érvényesíti a rendszer, valamint a tanúsítvány lejárati dátumát is (a szabványos TLS-kiszolgáló hitelesítése tanúsítvány-rögzítés nélkül).

## <a name="certificate-requirements"></a>Tanúsítványokra vonatkozó követelmények
A következő lista ismerteti a Azure Stack telepítéséhez szükséges tanúsítványokra vonatkozó követelményeket: 
- A tanúsítványokat a belső hitelesítésszolgáltatótól vagy egy nyilvános hitelesítésszolgáltatótól kell kibocsátani. Ha nyilvános hitelesítésszolgáltató van használatban, azt a Microsoft megbízható legfelső szintű felügyeleti programjának részeként kell szerepeltetni az operációs rendszer alaprendszerképében. A teljes listát itt találja: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- A Azure Stack infrastruktúrájának hálózati hozzáféréssel kell rendelkeznie a hitelesítésszolgáltató visszavont tanúsítványok listájához (CRL) a tanúsítványban közzétett helyhez. A CRL-nek http-végpontnak kell lennie
- Ha a tanúsítványokat előre 1903-buildekben futtatja, akkor a tanúsítványokat ugyanabból a belső hitelesítésszolgáltatótól kell kibocsátani, amely a telepítéskor vagy bármely nyilvános hitelesítésszolgáltatónál elérhető tanúsítványok aláírására használatos. A 1903 & későbbi tanúsítványokat bármely vállalati vagy nyilvános hitelesítésszolgáltató kiállíthatja.
- Az önaláírt tanúsítványok használata nem támogatott.
- Üzembe helyezéshez és elforgatáshoz használhat egyetlen tanúsítványt, amely a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes nevet tartalmazza, vagy egyéni tanúsítványokat is használhat a Azure Stack alábbi névterekhez. a használni kívánt szolgáltatások szükségesek. Mindkét módszerhez szükség van a helyettesítő karakterek használatára, például a kulcstartóra és a **KeyVaultInternal**. 
- A tanúsítvány PFX-titkosításának 3DES-nek kell lennie. 
- A tanúsítvány-aláírási algoritmus nem lehet SHA1. 
- A tanúsítvány formátumának PFX-nek kell lennie, mivel a Azure Stack telepítéséhez mind a nyilvános, mind a titkos kulcs szükséges. A titkos kulcsnak rendelkeznie kell a helyi számítógép kulcs attribútumával.
- A PFX-titkosításnak 3DES formátumúnak kell lennie (Ez alapértelmezés szerint Windows 10-es ügyfélről vagy Windows Server 2016 tanúsítványtárolóból való exportáláskor).
- A tanúsítvány PFX-fájljainak "Digital Signature" és "KeyEncipherment" értékűnek kell lenniük a "kulcshasználat" mezőben.
- A tanúsítvány PFX-fájljainak a "Kibővített kulcshasználat" mezőben szerepelniük kell a "kiszolgálói hitelesítés (1.3.6.1.5.5.7.3.1)" és az "ügyfél-hitelesítés (1.3.6.1.5.5.7.3.2)" értékkel.
- A tanúsítvány "kiállítva:" mezője nem egyezhet meg a "kiállító:" mezővel.
- Az összes tanúsítvány PFX-fájl jelszavának meg kell egyeznie az üzembe helyezés időpontjában.
- A tanúsítvány PFX-jelszavának összetett jelszónak kell lennie. Hozzon létre egy jelszót, amely megfelel az alábbi jelszó-összetettségi követelményeknek. Legalább nyolc karakter hosszúnak kell lennie. A jelszó legalább hármat tartalmaz a következők közül: nagybetűk, kisbetűk, 0-9, speciális karakterek, alfabetikus karakter, amely nem nagybetűs és nem kisbetűs. Jegyezze fel ezt a jelszót. Ezt fogja használni központi telepítési paraméterként.
- Győződjön meg arról, hogy a tulajdonos alternatív neve bővítménnyel (x509v3_config) a tulajdonos neve és a tulajdonos alternatív neve egyezik. A tulajdonos alternatív neve mezőben további állomásnevek (webhelyek, IP-címek, köznapi nevek) adhatók meg, amelyeket egyetlen SSL-tanúsítvánnyal kell védeni.

> [!NOTE]  
> Az önaláírt tanúsítványok nem támogatottak.

> [!NOTE]  
> A tanúsítvány megbízhatósági láncában lévő közvetítő hitelesítésszolgáltatók jelenléte *támogatott.* 

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Az ebben a szakaszban szereplő táblázat az Azure AD-hez és a AD FS Azure Stack-környezetekhez szükséges Azure Stack nyilvános végpontú PKI-tanúsítványokat ismerteti. A tanúsítványokra vonatkozó követelmények terület szerint vannak csoportosítva, valamint a használt névterek és az egyes névterekhez szükséges tanúsítványok. A tábla azt a mappát is leírja, amelyben a megoldás szolgáltatója a különböző tanúsítványokat a nyilvános végponton másolja. 

Minden Azure Stack nyilvános infrastruktúra-végponthoz megfelelő DNS-névvel rendelkező tanúsítványokra van szükség. Minden egyes végpont DNS-neve a (z  *&lt;) előtaggal > formátumban&lt; van kifejezve. régió >. FQDN&lt;>* . 

Az üzembe helyezéshez a [region] és a [externalfqdn] értékeknek meg kell egyezniük a régió és a Azure Stack rendszer számára kiválasztott külső tartománynevek nevével. Ha például a régió neve *Redmond* volt, és a külső tartománynév *contoso.com*volt, a DNS-névnek  *&lt;>. Redmond. contoso. com*formátumúnak kell lennie. *Az&lt;előtag >* értékeket a Microsoft a tanúsítvány által védett végpont leírására kijelölte. Emellett a külső infrastruktúra-végpontok  *előtag->értékeiazadottvégpontothasználóAzurestackszolgáltatástólfüggenek.&lt;* 

Az éles környezetek esetében ajánlott egyéni tanúsítványokat létrehozni az egyes végpontokhoz, és a megfelelő könyvtárba másolva. A fejlesztési környezetekben a tanúsítványok a tulajdonos és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes névtérre kiterjedő, egyetlen Wild kártyás tanúsítványként is elérhetők. Az összes végpontra és szolgáltatásra kiterjedő egyetlen tanúsítvány nem biztonságos, ezért csak fejlesztési célokra szolgál. Ne feledje, hogy mindkét beállításhoz helyettesítő tanúsítványokat kell használni a végpontokhoz, például az **ACS** -hez és a Key Vaulthoz, ahol szükség van rájuk. 

> [!Note]  
> Az üzembe helyezés során át kell másolnia a tanúsítványokat a telepítési mappába, amely megfelel az Ön által az Azure AD-ben központilag telepített identitás-szolgáltatónak (Azure AD vagy AD FS) Ha egyetlen tanúsítványt használ az összes végponthoz, akkor az alábbi táblázatokban vázolt módon minden központi telepítési mappába kell másolnia a tanúsítványfájl. A mappa szerkezete az üzembe helyezési virtuális gépen előre be van építve, és a következő helyen érhető el: C:\CloudDeployment\Setup\Certificates. 


| Telepítési mappa | Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN) | Hatókör (régiónként) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Nyilvános portál | portál. &lt;régió >. &lt;teljes tartománynév > | Portálok | &lt;region>.&lt;fqdn> |
| Felügyeleti portál | adminportal.&lt;region>.&lt;fqdn> | Portálok | &lt;region>.&lt;fqdn> |
| Nyilvános Azure Resource Manager | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure Resource Manager-rendszergazda | adminmanagement. &lt;régió >. &lt;teljes tartománynév > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Helyettesítő karakteres SSL-tanúsítvány) | Blobtároló | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Helyettesítő karakteres SSL-tanúsítvány) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Helyettesítő karakteres SSL-tanúsítvány) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Helyettesítő karakteres SSL-tanúsítvány) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Helyettesítő karakteres SSL-tanúsítvány) |  Belső kulcstartó |  adminvault.&lt;region>.&lt;fqdn> |
| Felügyeleti bővítmény gazdagépe | *.adminhosting. \<régió >. \<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Felügyeleti bővítmény gazdagépe | adminhosting.\<region>.\<fqdn> |
| Nyilvános kiterjesztésű gazdagép | *. hosting. \<régió >. \<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Nyilvános kiterjesztésű gazdagép | hosting.\<region>.\<fqdn> |

Ha az Azure AD-alapú üzembe helyezési mód használatával telepíti Azure Stack, akkor csak az előző táblázatban felsorolt tanúsítványokat kell igényelnie. Ha azonban a AD FS üzembe helyezési módban telepíti a Azure Stackt, akkor az alábbi táblázatban ismertetett tanúsítványokat is meg kell kérnie:

|Telepítési mappa|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN)|Hatókör (régiónként)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph. *&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Az ebben a szakaszban felsorolt összes tanúsítványnak ugyanazzal a jelszóval kell rendelkeznie. 

## <a name="optional-paas-certificates"></a>Választható Péter-tanúsítványok
Ha a további Azure Stack Péter-szolgáltatások (SQL, MySQL és App Service) telepítését tervezi a Azure Stack telepítése és konfigurálása után, további tanúsítványokat kell kérnie a Pásti-szolgáltatások végpontjának lefedéséhez. 

> [!IMPORTANT]
> A App Service, az SQL és a MySQL erőforrás-szolgáltatóhoz használt tanúsítványoknak ugyanazzal a legfelső szintű szolgáltatóval kell rendelkezniük, mint a globális Azure Stack-végpontokhoz. 

A következő táblázat ismerteti az SQL-és MySQL-adapterekhez szükséges végpontokat és tanúsítványokat, valamint a App Service. Ezeket a tanúsítványokat nem kell átmásolnia a Azure Stack telepítési mappájába. Ehelyett a további erőforrás-szolgáltatók telepítésekor adja meg ezeket a tanúsítványokat. 

|Hatókör (régiónként)|Tanúsítvány|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SANs)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL és MySQL|&#42;.dbadapter. *&lt;region>.&lt;fqdn>*<br>(Helyettesítő karakteres SSL-tanúsítvány)|dbadapter. *&lt;region>.&lt;fqdn>*|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítványa|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;region>.&lt;fqdn>*<br>(Több tartományos helyettesítő karakteres SSL-tanúsítvány<sup>1</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|

<sup>1</sup> egy olyan tanúsítványt igényel, amelyben több helyettesítő karakteres alternatív név is szerepel. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltató nem támogatja több helyettesítő karaktert egyetlen tanúsítványon sem 

<sup>2</sup> &#42;. appservice. *régió >.&lt; &lt; a teljes tartománynév >* a helyettesítő karakterek nem használhatók fel a három tanúsítvány helyett (API. appservice. *&lt;régió >. FQDN&lt;>* , FTP. appservice. *régió >.&lt; &lt; FQDN >* és SSO. appservice. *régió >.&lt; &lt; FQDN >* . A Appservice explicit módon külön tanúsítványokat kell használni ezekhez a végpontokhoz. 

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
Ismerje meg, hogyan [hozhatja Azure stack központi telepítéshez PKI-tanúsítványokat](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>További lépések
[Identitás-integráció](azure-stack-integrate-identity.md)