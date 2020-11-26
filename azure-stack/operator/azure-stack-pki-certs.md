---
title: Azure Stack hub nyilvános kulcsokra épülő infrastruktúrájának tanúsítványára vonatkozó követelmények
description: Ismerkedjen meg az Azure Stack hub PKI-tanúsítványának követelményeivel Azure Stack hub integrált rendszerekhez.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: ee0ef7119dfb2255cd97e343f8e7339ab715ed7d
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "93049602"
---
# <a name="azure-stack-hub-public-key-infrastructure-pki-certificate-requirements"></a>Azure Stack hub nyilvános kulcsokra épülő infrastruktúrájának (PKI) tanúsítványára vonatkozó követelmények

Azure Stack hub olyan nyilvános infrastruktúra-hálózattal rendelkezik, amely az Azure Stack hub-szolgáltatások és valószínűleg a bérlői virtuális gépek kis készletéhez rendelt, külsőleg elérhető nyilvános IP-címeket használ. A Azure Stack hub nyilvános infrastruktúra-végpontok megfelelő DNS-neveivel rendelkező PKI-tanúsítványokra van szükség Azure Stack központi telepítés során. Ez a cikk a következő információkat tartalmazza:

- Azure Stack hub tanúsítványokra vonatkozó követelményei.
- Azure Stack hub telepítéséhez kötelező tanúsítványok szükségesek.
- Nem kötelező tanúsítványok szükségesek az érték megadásakor – erőforrás-szolgáltatók hozzáadása.

> [!NOTE]
> Az Azure Stack hub alapértelmezés szerint a belső Active Directory integrált hitelesítésszolgáltatótól (CA) kiállított tanúsítványokat is használ a csomópontok közötti hitelesítéshez. A tanúsítvány érvényesítéséhez minden Azure Stack hub infrastruktúra-gép megbízhatónak tekinti a belső HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványát azáltal, hogy hozzáadja ezt a tanúsítványt a helyi tanúsítványtárolóhoz. Nem található Azure Stack hub tanúsítványának rögzítése vagy szűrése. Az egyes kiszolgálói tanúsítványok SAN-ja a cél teljes tartománynevével van érvényesítve. A megbízhatósági láncot is érvényesíti a rendszer, valamint a tanúsítvány lejárati dátumát is (a szabványos TLS-kiszolgáló hitelesítése tanúsítvány-rögzítés nélkül).

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények
A következő lista az általános tanúsítvány-kiállítási, biztonsági és formázási követelményeket ismerteti:

- A tanúsítványokat a belső hitelesítésszolgáltatótól vagy egy nyilvános hitelesítésszolgáltatótól kell kibocsátani. Ha nyilvános hitelesítésszolgáltató van használatban, azt a Microsoft megbízható legfelső szintű felügyeleti programjának részeként kell szerepeltetni az operációs rendszer alaprendszerképében. A teljes listát itt tekintheti meg [: Microsoft megbízható legfelső szintű tanúsítvány programja: résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
- Az Azure Stack hub-infrastruktúrának a tanúsítványban közzétett tanúsítvány-visszavonási lista (CRL) elérési helyéhez hálózati hozzáféréssel kell rendelkeznie. A CRL-nek egy HTTP-végponton kell lennie.
::: moniker range="< azs-1903"
- Ha a tanúsítványokat előre 1903-buildekben futtatja, akkor a tanúsítványokat ugyanabból a belső hitelesítésszolgáltatótól kell kibocsátani, amely a telepítéskor vagy bármely nyilvános hitelesítésszolgáltatónál elérhető tanúsítványok aláírására használatos.
::: moniker-end
::: moniker range=">= azs-1903"
- A 1903-es és újabb buildek tanúsítványainak elforgatásakor a tanúsítványokat bármely vállalati vagy nyilvános hitelesítésszolgáltató kiállíthatja.
::: moniker-end
- Az önaláírt tanúsítványok használata nem támogatott.
- Üzembe helyezéshez és elforgatáshoz használhatja a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes névtérre kiterjedő egyetlen tanúsítványt, vagy használhat egyéni tanúsítványokat a használni kívánt Azure Stack hub-szolgáltatások által igényelt egyes névterekhez. Mindkét módszerhez szükség van olyan végpontokhoz, ahol szükség van rájuk, **például a kulcstartó és** a **KeyVaultInternal**.
- A tanúsítvány PFX-titkosításának 3DES-nek kell lennie.
- A tanúsítvány-aláírási algoritmus nem lehet SHA1.
- A tanúsítvány formátumának PFX-nek kell lennie, mivel az Azure Stack hub telepítéséhez mind a nyilvános, mind a titkos kulcs szükséges. A titkos kulcsnak rendelkeznie kell a helyi számítógép kulcs attribútumával.
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
> Azure Stack hub leválasztott módban való telepítésekor javasolt a vállalati hitelesítésszolgáltató által kiadott tanúsítványok használata. Ez azért fontos, mert az Azure Stack hub-végpontokhoz hozzáférő ügyfeleknek képesnek kell lenniük a visszavont tanúsítványok listájához (CRL) való kapcsolatfelvételre.

> [!NOTE]  
> A tanúsítvány megbízhatósági láncában lévő közvetítő hitelesítésszolgáltatók jelenléte *támogatott.*

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Az ebben a szakaszban szereplő táblázat az Azure AD-hez és a AD FS Azure Stack hub-környezetekhez szükséges Azure Stack hub nyilvános végpontú PKI-tanúsítványokat ismerteti. A tanúsítványokra vonatkozó követelmények terület szerint vannak csoportosítva, valamint a használt névterek és az egyes névterekhez szükséges tanúsítványok. A tábla azt a mappát is leírja, amelyben a megoldás szolgáltatója a különböző tanúsítványokat a nyilvános végponton másolja.

Minden Azure Stack hub nyilvános infrastruktúra-végpontjának megfelelő DNS-névvel rendelkező tanúsítványokra van szükség. Minden egyes végpont DNS-neve a (z *&lt; ) előtaggal> formátumban van kifejezve. &lt; régió>. &lt; FQDN>*.

Az üzembe helyezéshez a [region] és a [externalfqdn] értékeknek meg kell egyezniük az Azure Stack hub rendszerhez választott régióval és külső tartománynevek nevével. Ha például a régió neve *Redmond* volt, és a külső tartománynév *contoso.com* volt, a DNS-névnek *&lt;>. Redmond.contoso.com formátumú előtaggal* kell rendelkeznie. Az *&lt; előtag>* értékeket a Microsoft a tanúsítvány által védett végpont leírására kijelölte. Emellett a külső infrastruktúra-végpontok *&lt; előtag->* értékei az adott végpontot használó Azure stack hub szolgáltatástól függenek.

Az éles környezetek esetében ajánlott egyéni tanúsítványokat létrehozni az egyes végpontokhoz, és a megfelelő könyvtárba másolva. A fejlesztési környezetekben a tanúsítványok a tulajdonos és a tulajdonos alternatív neve (SAN) mezőiben szereplő összes névtérre kiterjedő, egyetlen helyettesítő helyettesítő tanúsítványként is elérhetők. Egyetlen tanúsítvány, amely az összes végpontot és szolgáltatást lefedi, nem biztonságos testhelyzet, ezért csak fejlesztési célokra szolgál. Ne feledje, hogy mindkét beállításhoz helyettesítő tanúsítványokat kell használnia a végpontokhoz, például az **ACS** -hez és a Key Vaulthoz, ahol szükség van rájuk.

> [!Note]  
> Az üzembe helyezés során a tanúsítványokat a központi telepítési mappába kell másolnia, amely megfelel az Ön által az Azure AD-ben központilag üzembe helyezett identitás-szolgáltatónak (Azure AD vagy AD FS). Ha egyetlen tanúsítványt használ az összes végponthoz, akkor az alábbi táblázatokban vázolt módon minden központi telepítési mappába kell másolnia a tanúsítványfájl.A mappa szerkezete az üzembe helyezési virtuális gépen előre be van építve, és a következő helyen található: C:\CloudDeployment\Setup\Certificates.

| Telepítési mappa | Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN) | Hatókör (régiónként) | Altartomány névtere |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Nyilvános portál | portálon. &lt; régió>. &lt; teljes tartománynév> | Portálok | &lt;régió>. &lt; teljes tartománynév> |
| Felügyeleti portál | adminportal. &lt; régió>. &lt; teljes tartománynév> | Portálok | &lt;régió>. &lt; teljes tartománynév> |
| Nyilvános Azure Resource Manager | felügyelet. &lt; régió>. &lt; teljes tartománynév> | Azure Resource Manager | &lt;régió>. &lt; teljes tartománynév> |
| Azure Resource Manager-rendszergazda | adminmanagement. &lt; régió>. &lt; teljes tartománynév> | Azure Resource Manager | &lt;régió>. &lt; teljes tartománynév> |
| ACSBlob | *. blob. &lt; régió>. &lt; teljes tartománynév><br>(Helyettesítő karakteres SSL-tanúsítvány) | Blob Storage | BLOB. &lt; régió>. &lt; teljes tartománynév> |
| ACSTable | *. table. &lt; régió>. &lt; teljes tartománynév><br>(Helyettesítő karakteres SSL-tanúsítvány) | Table Storage | tábla. &lt; régió>. &lt; teljes tartománynév> |
| ACSQueue | *. üzenetsor. &lt; régió>. &lt; teljes tartománynév><br>(Helyettesítő karakteres SSL-tanúsítvány) | Queue Storage | üzenetsor. &lt; régió>. &lt; teljes tartománynév> |
| KeyVault | *. Vault. &lt; régió>. &lt; teljes tartománynév><br>(Helyettesítő karakteres SSL-tanúsítvány) | Key Vault | tároló. &lt; régió>. &lt; teljes tartománynév> |
| KeyVaultInternal | *. adminvault. &lt; régió>. &lt; teljes tartománynév><br>(Helyettesítő karakteres SSL-tanúsítvány) |  Belső kulcstartó |  adminvault. &lt; régió>. &lt; teljes tartománynév> |
| Felügyeleti bővítmény gazdagépe | *. adminhosting. \<region> .\<fqdn> (Helyettesítő karakteres SSL-tanúsítványok) | Felügyeleti bővítmény gazdagépe | adminhosting. \<region> .\<fqdn> |
| Nyilvános kiterjesztésű gazdagép | *. hosting. \<region> .\<fqdn> (Helyettesítő karakteres SSL-tanúsítványok) | Nyilvános kiterjesztésű gazdagép | üzemeltetés. \<region> ..\<fqdn> |

Ha az Azure AD üzembe helyezési móddal telepíti Azure Stack hub-t, csak az előző táblázatban felsorolt tanúsítványokat kell igényelnie. Ha azonban Azure Stack hub-t a AD FS üzembe helyezési móddal telepíti, akkor az alábbi táblázatban ismertetett tanúsítványokat is meg kell kérnie:

|Telepítési mappa|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SAN)|Hatókör (régiónként)|Altartomány névtere|
|-----|-----|-----|-----|
|ADFS|ADFS. *&lt; régió>. &lt; teljes tartománynév>*<br>(SSL-tanúsítvány)|ADFS|*&lt;régió>. &lt; teljes tartománynév>*|
|Graph|Graph. *&lt; régió>. &lt; teljes tartománynév>*<br>(SSL-tanúsítvány)|Graph|*&lt;régió>. &lt; teljes tartománynév>*|
|

> [!IMPORTANT]
> Az ebben a szakaszban felsorolt összes tanúsítványnak ugyanazzal a jelszóval kell rendelkeznie.

## <a name="optional-paas-certificates"></a>Opcionális PaaS-tanúsítványok
Ha a további Azure Stack hub-szolgáltatások (például SQL, MySQL, App Service vagy Event Hubs) üzembe helyezését tervezi az Azure Stack hub üzembe helyezése és konfigurálása után, akkor további tanúsítványokat kell kérnie a Pásti-szolgáltatások végpontjának lefedéséhez.

> [!IMPORTANT]
> Az erőforrás-szolgáltatóhoz használt tanúsítványoknak ugyanazzal a legfelső szintű szolgáltatóval kell rendelkezniük, mint a globális Azure Stack hub-végpontokhoz.

Az alábbi táblázat az erőforrás-szolgáltatókhoz szükséges végpontokat és tanúsítványokat ismerteti. Ezeket a tanúsítványokat nem kell átmásolnia az Azure Stack hub telepítési mappájába. Ehelyett ezeket a tanúsítványokat az erőforrás-szolgáltató telepítésekor kell megadnia.

|Hatókör (régiónként)|Tanúsítvány|Kötelező tanúsítvány tárgya és a tulajdonos alternatív nevei (SANs)|Altartomány névtere|
|-----|-----|-----|-----|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítványa|&#42;. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>&#42;. SCM. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>&#42;. SSO. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>(Több tartományos helyettesítő karakteres SSL-tanúsítvány<sup>1</sup>)|appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>SCM. appservice. *&lt; régió>. &lt; teljes tartománynév>*|
|App Service|API|API. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>SCM. appservice. *&lt; régió>. &lt; teljes tartománynév>*|
|App Service|FTP|FTP. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>SCM. appservice. *&lt; régió>. &lt; teljes tartománynév>*|
|App Service|SSO|SSO. appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice. *&lt; régió>. &lt; teljes tartománynév>*<br>SCM. appservice. *&lt; régió>. &lt; teljes tartománynév>*|
|Event Hubs|SSL|&#42;. eventhub. *&lt; régió>. &lt; teljes tartománynév>* | eventhub. *&lt; régió>. &lt; teljes tartománynév>* |
|IoT Hub|SSL|&#42;. mgmtiothub. *&lt; régió>. &lt; teljes tartománynév>* | mgmtiothub. *&lt; régió>. &lt; teljes tartománynév>* |
|SQL, MySQL|SQL és MySQL|&#42;. dbadapter. *&lt; régió>. &lt; teljes tartománynév>*<br>(Helyettesítő karakteres SSL-tanúsítvány)|dbadapter. *&lt; régió>. &lt; teljes tartománynév>*|

<sup>1</sup> egy olyan tanúsítványt igényel, amelyben több helyettesítő karakteres alternatív név is szerepel. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltató nem támogatja több helyettesítő karaktert egyetlen tanúsítványon.

<sup>2</sup> &#42;. appservice. *&lt; régió>. &lt; a teljes tartománynév>* helyettesítő tanúsítvány nem használható fel a három tanúsítvány helyett (API. appservice.*&lt; régió>. &lt; FQDN>*, FTP. appservice. *&lt; régió>. &lt; FQDN>* és SSO. appservice. *&lt; régió>. &lt; FQDN>*. A Appservice explicit módon külön tanúsítványokat kell használni ezekhez a végpontokhoz.

## <a name="learn-more"></a>Tudjon meg többet
Ismerje meg, hogyan [hozhatja ki a PKI-tanúsítványokat Azure stack hub telepítéséhez](azure-stack-get-pki-certs.md).

## <a name="next-steps"></a>Következő lépések
[AD FS identitás integrálása az Azure stack hub-adatközpontba](azure-stack-integrate-identity.md).