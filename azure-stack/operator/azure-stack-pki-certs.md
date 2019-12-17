---
title: A nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelmények Azure Stack | Microsoft Docs
description: Ismerje meg a Azure Stack PKI-tanúsítvány telepítési követelményeit Azure Stack integrált rendszerek esetében.
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
ms.date: 12/16/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: e9276d67c767ec6a08549be830c52bbbe03230ec
ms.sourcegitcommit: 50b7974454e008724817cbb4416ce40368b31ef4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2019
ms.locfileid: "75035506"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>A nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelmények Azure Stack

Azure Stack rendelkezik egy nyilvános infrastruktúra-hálózattal, amely külső módon elérhető nyilvános IP-címeket használ, amelyek egy kis készlethez vannak rendelve Azure Stack szolgáltatásokhoz és esetleg bérlői virtuális gépekhez. Az Azure Stack üzembe helyezése során olyan PKI-tanúsítványokra van szükség, amelyek esetében meg vannak adva a megfelelő DNS-nevek az Azure Stack nyilvánosinfrastruktúra-végpontjaihoz. Ez a cikk a következő információkat tartalmazza:

- Milyen tanúsítványokra van szükség a Azure Stack telepítéséhez.
- A specifikációknak megfelelő tanúsítványok beszerzésének folyamata.
- A tanúsítványok előkészítése, ellenőrzése és használata az üzembe helyezés során.

> [!NOTE]
> A Azure Stack alapértelmezés szerint a belső Active Directory integrált hitelesítésszolgáltatótól (CA) kibocsátott tanúsítványokat is használ a csomópontok közötti hitelesítéshez. A tanúsítvány érvényesítéséhez minden Azure Stack infrastruktúra-gép megbízhatónak tekinti a belső HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványát azáltal, hogy hozzáadja ezt a tanúsítványt a helyi tanúsítványtárolóhoz. A Azure Stack nem rendelkezik tanúsítványok rögzítésével vagy engedélyezési listával. Az egyes kiszolgálói tanúsítványok SAN-ja a cél teljes tartománynevével van érvényesítve. A megbízhatósági láncot is érvényesíti a rendszer, valamint a tanúsítvány lejárati dátumát is (a szabványos TLS-kiszolgáló hitelesítése tanúsítvány-rögzítés nélkül).

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények
A következő lista ismerteti a Azure Stack telepítéséhez szükséges tanúsítványokra vonatkozó követelményeket:

- A tanúsítványokat a belső hitelesítésszolgáltatótól vagy egy nyilvános hitelesítésszolgáltatótól kell kibocsátani. Ha nyilvános hitelesítésszolgáltató van használatban, azt a Microsoft megbízható legfelső szintű felügyeleti programjának részeként kell szerepeltetni az operációs rendszer alaprendszerképében. A teljes listát itt tekintheti meg [: Microsoft megbízható legfelső szintű tanúsítvány programja: résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
- A Azure Stack infrastruktúrájának hálózati hozzáféréssel kell rendelkeznie a hitelesítésszolgáltató visszavont tanúsítványok listájához (CRL) a tanúsítványban közzétett helyhez. A CRL-nek http-végpontnak kell lennie.
- Ha a tanúsítványokat előre 1903-buildekben futtatja, akkor a tanúsítványokat ugyanabból a belső hitelesítésszolgáltatótól kell kibocsátani, amely a telepítéskor vagy bármely nyilvános hitelesítésszolgáltatónál elérhető tanúsítványok aláírására használatos. A 1903-es és újabb verzióiban a tanúsítványokat bármely vállalati vagy nyilvános hitelesítésszolgáltató kiállíthatja.
- Az önaláírt tanúsítványok használata nem támogatott.
- Üzembe helyezéshez és elforgatáshoz használhat egyetlen tanúsítványt, amely a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes nevet tartalmazza, vagy egyéni tanúsítványokat is használhat a Azure Stack alábbi névterekhez. a használni kívánt szolgáltatások szükségesek. Mindkét módszerhez szükség van olyan végpontokhoz, ahol szükség van rájuk, **például a kulcstartó és** a **KeyVaultInternal**.
- A tanúsítvány PFX-titkosításának 3DES-nek kell lennie.
- A tanúsítvány-aláírási algoritmus nem lehet SHA1.
- A tanúsítvány formátumának PFX-nek kell lennie, mivel a Azure Stack telepítéséhez mind a nyilvános, mind a titkos kulcs szükséges. A titkos kulcsnak rendelkeznie kell a helyi számítógép kulcs attribútumával.
- A PFX-titkosításnak 3DES-nek kell lennie (ez a titkosítás alapértelmezés szerint a Windows 10-es ügyfélről vagy a Windows Server 2016 tanúsítványtárolóból való exportáláskor).
- A tanúsítvány PFX-fájljainak "Digital Signature" és "KeyEncipherment" értékűnek kell lenniük a "kulcshasználat" mezőben.
- A tanúsítvány PFX-fájljainak a "Kibővített kulcshasználat" mezőben szerepelniük kell a "kiszolgálói hitelesítés (1.3.6.1.5.5.7.3.1)" és az "ügyfél-hitelesítés (1.3.6.1.5.5.7.3.2)" értékkel.
- A tanúsítvány "kiállítva:" mezője nem egyezhet meg a "kiállító:" mezővel.
- Az összes tanúsítvány PFX-fájl jelszavának meg kell egyeznie az üzembe helyezés időpontjában.
- A tanúsítvány PFX-jelszavának összetett jelszónak kell lennie. Jegyezze fel ezt a jelszót, mert központi telepítési paraméterként fogja használni. A jelszónak meg kell felelnie az alábbi jelszó-összetettségi követelményeknek:
    - Legalább nyolc karakter hosszúnak kell lennie.
    - A következő karakterek legalább hármat tartalmazhatnak: nagybetűs, kisbetűk, 0-9, speciális karakterek, alfabetikus karakter, amely nem nagybetűs vagy kisbetűs.
- Győződjön meg arról, hogy a tulajdonos alternatív neve bővítmény (x509v3_config) a tulajdonos neve és a tulajdonos alternatív neve egyezik. A tulajdonos alternatív neve mezőben további állomásnevek (webhelyek, IP-címek, köznapi nevek) adhatók meg, amelyeket egyetlen SSL-tanúsítvánnyal kell védeni.

> [!NOTE]  
> Az önaláírt tanúsítványok nem támogatottak.  
> Azure Stack hub leválasztott módban való telepítésekor javasolt a vállalati hitelesítésszolgáltató által kiadott tanúsítványok használata. Ez azért fontos, mert az Azure Stack-végpontokhoz hozzáférő ügyfeleknek képesnek kell lenniük a visszavont tanúsítványok listájához (CRL) való kapcsolatfelvételre.

> [!NOTE]  
> A tanúsítvány megbízhatósági láncában lévő közvetítő hitelesítésszolgáltatók jelenléte *támogatott.*

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Az ebben a szakaszban szereplő táblázat az Azure AD-hez és a AD FS Azure Stack-környezetekhez szükséges Azure Stack nyilvános végpontú PKI-tanúsítványokat ismerteti. A tanúsítványokra vonatkozó követelmények terület szerint vannak csoportosítva, valamint a használt névterek és az egyes névterekhez szükséges tanúsítványok. A tábla azt a mappát is leírja, amelyben a megoldás szolgáltatója a különböző tanúsítványokat a nyilvános végponton másolja.

Minden Azure Stack nyilvános infrastruktúra-végponthoz megfelelő DNS-névvel rendelkező tanúsítványokra van szükség. Az egyes végpontok DNS-neve a következők egyike: *&lt;előtag >.&lt;régió >.&lt;fqdn >* .

Az üzembe helyezéshez a [region] és a [externalfqdn] értékeknek meg kell egyezniük a régió és a Azure Stack rendszer számára kiválasztott külső tartománynevek nevével. Ha például a régió neve *Redmond* volt, és a külső tartománynév *contoso.com*volt, a DNS-nevek formátuma *&lt;előtag lesz >. Redmond. contoso. com*. A *&lt;előtag >* értékeit a Microsoft a tanúsítvány által védett végpont leírására kijelölte. Emellett a *&lt;előtag >* a külső infrastruktúra-végpontok értékei az adott végpontot használó Azure stack szolgáltatástól függenek.

Az éles környezetek esetében ajánlott egyéni tanúsítványokat létrehozni az egyes végpontokhoz, és a megfelelő könyvtárba másolva. A fejlesztési környezetekben a tanúsítványok a tulajdonos és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes névtérre kiterjedő, egyetlen Wild kártyás tanúsítványként is elérhetők. Egyetlen tanúsítvány, amely az összes végpontot és szolgáltatást lefedi, nem biztonságos testhelyzet, ezért csak fejlesztési célokra szolgál. Ne feledje, hogy mindkét beállításhoz helyettesítő tanúsítványokat kell használnia a végpontokhoz, például az **ACS** -hez és a Key Vaulthoz, ahol szükség van rájuk.

> [!Note]  
> Az üzembe helyezés során a tanúsítványokat a központi telepítési mappába kell másolnia, amely megfelel az Ön által az Azure AD-ben központilag üzembe helyezett identitás-szolgáltatónak (Azure AD vagy AD FS). Ha egyetlen tanúsítványt használ az összes végponthoz, akkor az alábbi táblázatokban vázolt módon minden központi telepítési mappába kell másolnia a tanúsítványfájl. A mappa szerkezete az üzembe helyezési virtuális gépen előre be van építve, és a következő helyen található: C:\CloudDeployment\Setup\Certificates.

| Telepítési mappa | Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN) | Hatókör (régiónként) | Altartomány névtere |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Nyilvános portál | portál.&lt;régió >.&lt;FQDN > | Portals | &lt;régió >.&lt;FQDN > |
| Felügyeleti portál | adminportal.&lt;régió >.&lt;FQDN > | Portals | &lt;régió >.&lt;FQDN > |
| Nyilvános Azure Resource Manager | felügyeleti.&lt;régió >.&lt;FQDN > | Azure Resource Manager | &lt;régió >.&lt;FQDN > |
| Azure Resource Manager-rendszergazda | adminmanagement.&lt;régió >.&lt;FQDN > | Azure Resource Manager | &lt;régió >.&lt;FQDN > |
| ACSBlob | *. blob.&lt;régió >.&lt;FQDN ><br>(Helyettesítő karakteres SSL-tanúsítvány) | Blob Storage | BLOB.&lt;régió >.&lt;FQDN > |
| ACSTable | *. table.&lt;régió >.&lt;FQDN ><br>(Helyettesítő karakteres SSL-tanúsítvány) | Table Storage | tábla.&lt;régió >.&lt;FQDN > |
| ACSQueue | *. üzenetsor.&lt;régió >.&lt;FQDN ><br>(Helyettesítő karakteres SSL-tanúsítvány) | Queue Storage | várólista.&lt;régió >.&lt;FQDN > |
| KeyVault | *. Vault.&lt;régió >.&lt;FQDN ><br>(Helyettesítő karakteres SSL-tanúsítvány) | Key Vault | Vault.&lt;régió >.&lt;FQDN > |
| KeyVaultInternal | *.adminvault.&lt;régió >.&lt;FQDN ><br>(Helyettesítő karakteres SSL-tanúsítvány) |  Belső kulcstartó |  adminvault.&lt;régió >.&lt;FQDN > |
| Felügyeleti bővítmény gazdagépe | *.adminhosting.\<régió >.\<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Felügyeleti bővítmény gazdagépe | adminhosting.\<régió >.\<FQDN > |
| Nyilvános kiterjesztésű gazdagép | *. hosting.\<régió >.\<FQDN > (helyettesítő karakteres SSL-tanúsítványok) | Nyilvános kiterjesztésű gazdagép | üzemeltetési.\<régió >.\<FQDN > |

Ha az Azure AD-alapú üzembe helyezési mód használatával telepíti Azure Stack, akkor csak az előző táblázatban felsorolt tanúsítványokat kell igényelnie. Ha azonban a Azure Stack az AD FS üzembe helyezési módban telepíti, akkor az alábbi táblázatban ismertetett tanúsítványokat is meg kell adnia:

|Telepítési mappa|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN)|Hatókör (régiónként)|Altartomány névtere|
|-----|-----|-----|-----|
|ADFS|ADFS. *&lt;régió >.&lt;fqdn >*<br>(SSL-tanúsítvány)|ADFS|*&lt;régió >.&lt;FQDN >*|
|Gráf|Graph. *&lt;régió >.&lt;fqdn >*<br>(SSL-tanúsítvány)|Gráf|*&lt;régió >.&lt;FQDN >*|
|

> [!IMPORTANT]
> Az ebben a szakaszban felsorolt összes tanúsítványnak ugyanazzal a jelszóval kell rendelkeznie.

## <a name="optional-paas-certificates"></a>Választható Péter-tanúsítványok
Ha a további Azure Stack Péter-szolgáltatások (SQL, MySQL és App Service) üzembe helyezését tervezi a Azure Stack telepítése és konfigurálása után, további tanúsítványokat kell kérnie a Pásti-szolgáltatások végpontjának lefedéséhez.

> [!IMPORTANT]
> A App Service, az SQL és a MySQL erőforrás-szolgáltatóhoz használt tanúsítványoknak ugyanazzal a legfelső szintű szolgáltatóval kell rendelkezniük, mint a globális Azure Stack-végpontokhoz.

A következő táblázat ismerteti az SQL-és MySQL-adapterekhez szükséges végpontokat és tanúsítványokat, valamint a App Service. Ezeket a tanúsítványokat nem kell átmásolnia a Azure Stack telepítési mappájába. Ehelyett a további erőforrás-szolgáltatók telepítésekor adja meg ezeket a tanúsítványokat.

|Hatókör (régiónként)|Tanúsítvány|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SANs)|Altartomány névtere|
|-----|-----|-----|-----|
|SQL, MySQL|SQL és MySQL|&#42;.dbadapter. *&lt;régió >.&lt;fqdn >*<br>(Helyettesítő karakteres SSL-tanúsítvány)|dbadapter. *&lt;régió >.&lt;fqdn >*|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítványa|&#42;appservice. *&lt;régió >.&lt;fqdn >*<br>&#42;. SCM. appservice. *&lt;régió >.&lt;fqdn >*<br>&#42;. SSO. appservice. *&lt;régió >.&lt;fqdn >*<br>(Több tartományos helyettesítő karakteres SSL-tanúsítvány<sup>1</sup>)|appservice. *&lt;régió >.&lt;fqdn >*<br>SCM. appservice. *&lt;régió >.&lt;fqdn >*|
|App Service|API|API. appservice. *&lt;régió >.&lt;fqdn >*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;régió >.&lt;fqdn >*<br>SCM. appservice. *&lt;régió >.&lt;fqdn >*|
|App Service|FTP|FTP. appservice. *&lt;régió >.&lt;fqdn >*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;régió >.&lt;fqdn >*<br>SCM. appservice. *&lt;régió >.&lt;fqdn >*|
|App Service|SSO|SSO. appservice. *&lt;régió >.&lt;fqdn >*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt;régió >.&lt;fqdn >*<br>SCM. appservice. *&lt;régió >.&lt;fqdn >*|

<sup>1</sup> egy olyan tanúsítványt igényel, amelyben több helyettesítő karakteres alternatív név is szerepel. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltató nem támogatja több helyettesítő karaktert egyetlen tanúsítványon.

<sup>2</sup> &#42;. appservice. *&lt;régió >.&lt;fqdn >* a helyettesítő karakterek nem használhatók fel a három tanúsítvány helyett (API. appservice. *&lt;régió >.&lt;FQDN >* , ftp. appservice. *&lt;régió >.&lt;FQDN >* és sso. appservice. *&lt;régió >.&lt;FQDN >* . A Appservice explicit módon külön tanúsítványokat kell használni ezekhez a végpontokhoz.

## <a name="learn-more"></a>További információ
Ismerje meg, hogyan [hozhatja Azure stack központi telepítéshez PKI-tanúsítványokat](azure-stack-get-pki-certs.md).

## <a name="next-steps"></a>Következő lépések
[Integrálja AD FS identitását az Azure stack adatközpontba](azure-stack-integrate-identity.md).