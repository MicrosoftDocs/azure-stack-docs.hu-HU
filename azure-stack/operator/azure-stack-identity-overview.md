---
title: A Azure Stack identitás-szolgáltatóinak áttekintése | Microsoft Docs
description: Ismerkedjen meg a Azure Stack használható identitás-szolgáltatókkal.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 94a7dc11c4711bcdf39215ccaab69e95ea5c57f3
ms.sourcegitcommit: b72729305234e13c65de3771cb08678d46ba1348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72543843"
---
# <a name="overview-of-identity-providers-for-azure-stack"></a>A Azure Stack identitás-szolgáltatóinak áttekintése

A Azure Stack Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) szükséges, amelyet a Active Directory identitás-szolgáltatóként támogat. A szolgáltató választása egy egyszeri döntés, amelyet a Azure Stack első telepítésekor hajt végre. Az ebben a cikkben szereplő fogalmak és engedélyezési információk segítséget nyújtanak az identitás-szolgáltatók választásához.

Az Azure AD-t vagy a AD FS Ön által választott módon a Azure Stack üzembe helyezésének módja határozza meg:

- Ha csatlakoztatott módban telepíti azt, használhatja az Azure AD-t vagy a AD FS.
- Ha leválasztott módban telepíti, és nem csatlakozik az internethez, csak AD FS támogatott.

A Azure Stack-környezettől függő beállításokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- Azure Stack Deployment Kit: [identitással kapcsolatos szempontok](azure-stack-datacenter-integration.md#identity-considerations).
- Azure Stack integrált rendszerek: [üzembe helyezési tervezési döntések Azure stack integrált rendszerekhez](azure-stack-connection-models.md).

## <a name="common-concepts-for-identity-providers"></a>Az identitás-szolgáltatók általános fogalmai

A következő fejezetek az identitás-szolgáltatókkal kapcsolatos általános fogalmakat és azok használatát ismertetik Azure Stackban.

![Identitás-szolgáltatók terminológiája](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Címtárbeli bérlők és szervezetek

A címtár egy olyan tároló, amely a *felhasználókra*, *alkalmazásokra*, *csoportokra*és *egyszerű szolgáltatásokra*vonatkozó információkat tartalmazza.

A címtár-bérlő egy *szervezet*, például a Microsoft vagy a saját vállalata.

- Az Azure AD támogatja a több-bérlős működést, valamint akár több szervezetet is, mindegyiket külön címtárban. Ha az Azure AD-t használja, és több Bérlővel rendelkezik, az alkalmazásokat és a felhasználókat egy bérlő más bérlői számára is megadhatja ugyanazon a címtáron.
- AD FS csak egyetlen bérlőt támogat, ezért csak egyetlen szervezet.

### <a name="users-and-groups"></a>Felhasználók és csoportok

A felhasználói fiókok (identitások) olyan szabványos fiókok, amelyek felhasználói azonosító és jelszó használatával hitelesítik az egyéni felhasználókat. A csoportok tartalmazhatnak felhasználókat vagy más csoportokat.

A felhasználók és csoportok létrehozása és kezelése a használt identitási megoldástól függ.

Azure Stack felhasználói fiókok:

- Létrehozása a *felhasználónév \@domain* formátumban történik. Bár a AD FS leképezi a felhasználói fiókokat egy Active Directory példányra, a AD FS nem támogatja a *\\ \<domain* > \\ \<alias > formátum használatát.
- Beállítható a többtényezős hitelesítés használatára.
- Csak arra a könyvtárra korlátozódik, amelyre először regisztrálnak, amely a szervezet címtára.
- A helyszíni címtárakból is importálható. További információ: a [helyszíni címtárak integrálása Azure Active Directorysal](/azure/active-directory/connect/active-directory-aadconnect).

Amikor bejelentkezik a szervezet bérlői portálján, a *https: \//Portal.local.azurestack.external* URL-címet használja. A Azure Stack-portálra való bejelentkezéskor a Azure Stack regisztrálásához használt tartománytól eltérő tartományokban a Azure Stack regisztrálásához használt tartománynevet hozzá kell fűzni a portál URL-címéhez. Ha például Azure Stack regisztrálva van a fabrikam.onmicrosoft.com-ben, és a felhasználói fiók bejelentkezve admin@contoso.com, a felhasználói portálra való bejelentkezéshez használt URL-cím a következő lesz: https: \//Portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Vendég felhasználók

A vendég felhasználók olyan más címtárbeli bérlők felhasználói fiókjai, amelyek hozzáférést kaptak a címtárban található erőforrásokhoz. A vendég felhasználók támogatásához használja az Azure AD-t, és engedélyezze a több-bérlős támogatását. Ha a támogatás engedélyezve van, meghívhatja a vendég felhasználókat, hogy hozzáférjenek a címtár-bérlő erőforrásaihoz, ami viszont lehetővé teszi a külső szervezetekkel való együttműködést.

A vendég felhasználók meghívásához a Felhőbeli operátorok és a felhasználók használhatják az [Azure ad B2B-együttműködést](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). A meghívott felhasználók hozzáférést kapnak a címtárhoz, az erőforrásokhoz és az alkalmazásokhoz, és megtarthatja a saját erőforrásai és adatai feletti irányítást.

Vendég felhasználóként bejelentkezhet egy másik szervezet címtár-bérlőbe. Ehhez fűzze hozzá a szervezet címtárának nevét a portál URL-címéhez. Ha például a contoso-szervezethez tartozik, és szeretne bejelentkezni a fabrikam-címtárba, használja a https: \//Portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="apps"></a>Alkalmazások

Alkalmazásokat regisztrálhat az Azure AD-be vagy a AD FSba, majd az alkalmazásokat felkínálhatja a szervezet felhasználói számára.

Az alkalmazások a következők:

- **Webalkalmazások**: ilyenek például a Azure Portal és a Azure Resource Manager. Támogatják a webes API-hívásokat.
- **Natív ügyfél**: ilyenek például a Azure PowerShell, a Visual Studio és az Azure parancssori felület.

Az alkalmazások két típusú bérletet támogatnak:

- **Egybérlős**: csak abban a címtárban támogatja a felhasználókat és szolgáltatásokat, amelyben az alkalmazás regisztrálva van.

  > [!NOTE]
  > Mivel a AD FS csak egyetlen könyvtárat támogat, a AD FS topológiában létrehozott alkalmazások tervezése, egybérlős alkalmazások.

- **Több-bérlős**: a támogatja a felhasználók és a szolgáltatások használatát abban a könyvtárban, ahol az alkalmazás regisztrálva van, és további bérlői címtárakat is használhat. A több-bérlős alkalmazások esetében egy másik bérlői címtár (egy másik Azure AD-bérlő) felhasználói bejelentkezhetnek az alkalmazásba.

  További információ a több-bérlős szolgáltatásról: a [több-bérlő engedélyezése](azure-stack-enable-multitenancy.md).

  A több-bérlős alkalmazások fejlesztésével kapcsolatos további információkért lásd: [több-bérlős alkalmazások](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Amikor regisztrál egy alkalmazást, két objektumot hoz létre:

- **Application Object**: az alkalmazás globális ábrázolása az összes bérlő között. Ez a kapcsolat egy-az-egyhez a szoftveres alkalmazással, és csak abban a címtárban létezik, amelyben az alkalmazás regisztrálva van.

- **Egyszerű szolgáltatásnév objektum**: az alkalmazáshoz létrehozott hitelesítő adat abban a könyvtárban, amelyben az alkalmazást először regisztrálták. A szolgáltatás minden további bérlő címtárában is létrejön egy egyszerű szolgáltatásnév, amelyben az alkalmazás használatban van. Ez a kapcsolat lehet egy-a-többhöz a szoftveres alkalmazással.

Az alkalmazás-és egyszerű szolgáltatás objektumaival kapcsolatos további tudnivalókért tekintse meg [az alkalmazások és szolgáltatások egyszerű objektumainak Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects)című témakört.

### <a name="service-principals"></a>Egyszerű szolgáltatások

Az egyszerű szolgáltatásnév egy olyan alkalmazás vagy szolgáltatás *hitelesítő adatai* , amely hozzáférést biztosít a Azure stack erőforrásaihoz. Egy egyszerű szolgáltatásnév használata elkülöníti az alkalmazás engedélyeit az alkalmazás felhasználójának engedélyeitől.

A rendszer minden olyan bérlőn létrehoz egy szolgáltatásnevet, amelyben az alkalmazás használatban van. Az egyszerű szolgáltatás identitást hoz létre a bejelentkezéshez, és hozzáfér az adott bérlő által védett erőforrásokhoz (például felhasználókhoz).

- Egy egybérlős alkalmazáshoz csak egy egyszerű szolgáltatásnév tartozik, amely abban a címtárban található, ahol elsőként létrehozták. Ez az egyszerű szolgáltatásnév létrejött, és az alkalmazás regisztrálása során használatban van.
- A több-bérlős webalkalmazások vagy API-k rendelkeznek egy olyan szolgáltatással, amely minden olyan bérlőn létrejön, ahol az adott bérlő felhasználója hozzájárul az alkalmazás használatához.

Az egyszerű szolgáltatásokhoz tartozó hitelesítő adatok lehetnek a Azure Portalon vagy tanúsítványon keresztül generált kulcsok is. A tanúsítvány használata az Automation számára alkalmas, mert a tanúsítványok a kulcsoknál biztonságosabbnak tekintendők.

> [!NOTE]
> Ha a Azure Stack használatával AD FS használ, csak a rendszergazda hozhat létre egyszerű szolgáltatásokat. A AD FS az egyszerű szolgáltatásokhoz tanúsítványokra van szükség, és a Kiemelt végponton (PEP) keresztül jönnek létre. További információ: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](azure-stack-create-service-principals.md).

Az Azure Stack egyszerű szolgáltatásainak megismeréséhez lásd: [egyszerű szolgáltatásnév létrehozása](azure-stack-create-service-principals.md).

### <a name="services"></a>Szolgáltatások

Az identitás-szolgáltatóval kommunikáló Azure Stack szolgáltatások regisztrálva vannak az identitás-szolgáltató alkalmazásával. Az alkalmazásokhoz hasonlóan a regisztráció lehetővé teszi, hogy a szolgáltatás hitelesítse az identitás rendszerét.

Az összes Azure-szolgáltatás [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokollokat és [JSON webes jogkivonatokat](/azure/active-directory/develop/active-directory-token-and-claims) használ az identitásuk létrehozásához. Mivel az Azure AD és a AD FS a protokollok konzisztens használatát teszi lehetővé, a [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) használatával hitelesítheti a helyszíni vagy az Azure-t (egy csatlakoztatott forgatókönyvben). A ADAL segítségével olyan eszközöket is használhat, mint a Azure PowerShell és az Azure CLI a Felhőbeli és a helyszíni erőforrás-kezeléshez.

### <a name="identities-and-your-identity-system"></a>Identitások és az Identity System

A Azure Stack identitásai közé tartoznak a felhasználói fiókok, csoportok és egyszerű szolgáltatások.

A Azure Stack telepítésekor számos beépített alkalmazás és szolgáltatás automatikusan regisztrálja az identitás-szolgáltatót a címtár-bérlőben. Bizonyos, a regisztrációhoz használt szolgáltatások a felügyelethez használatosak. Más szolgáltatások is elérhetők a felhasználók számára. Az alapértelmezett regisztrációk olyan alapszolgáltatási identitásokat biztosítanak, amelyek egymással és a később hozzáadott identitásokkal is kezelhetik egymást.

Ha több-Bérlővel állítja be az Azure AD-t, néhány alkalmazás propagálja az új címtárakat.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

### <a name="authentication-by-apps-and-users"></a>Hitelesítés alkalmazások és felhasználók szerint

![Azure Stack rétegek közötti identitás](media/azure-stack-identity-overview/identity-layers.png)

Alkalmazások és felhasználók esetében a Azure Stack architektúráját négy réteg írja le. Az egyes rétegek közötti interakciók különböző típusú hitelesítéseket használhatnak.

|Réteg    |A rétegek közötti hitelesítés  |
|---------|---------|
|Eszközök és ügyfelek, például a felügyeleti portál     | A Azure Stackban lévő erőforrások eléréséhez vagy módosításához az eszközök és az ügyfelek egy [JSON web token](/azure/active-directory/develop/active-directory-token-and-claims) használatával helyezik el a Azure Resource Manager hívását. <br>Azure Resource Manager ellenőrzi JSON Web Token a kiállított jogkivonatban lévő *jogcímeket* , hogy megbecsülje, hogy a felhasználó vagy a szolgáltatásnév milyen szintű engedélyekkel rendelkezik Azure Stackban. |
|Azure Resource Manager és az alapvető szolgáltatásai     |Azure Resource Manager kommunikál az erőforrás-szolgáltatókkal, hogy átvigye a kommunikációt a felhasználóktól. <br> Az átvitelek [Azure Resource Manager sablonokon](/azure-stack/user/azure-stack-arm-templates)keresztül *közvetlen, kényszerített* hívásokat vagy *deklaratív* hívásokat használnak.|
|Erőforrás-szolgáltatók     |Az erőforrás-szolgáltatóknak átadott hívások biztonsága tanúsítványalapú hitelesítéssel történik. <br>A Azure Resource Manager és az erőforrás-szolgáltató egy API-n keresztül marad a kommunikációban. Az erőforrás-szolgáltató a Azure Resource Managertól kapott összes hívás esetében érvényesíti a hívást a tanúsítvánnyal.|
|Infrastruktúra és üzleti logika     |Az erőforrás-szolgáltatók az üzleti logikával és az infrastruktúrával kommunikálnak az általuk választott hitelesítési mód használatával. A Azure Stack a szolgáltatással szállított alapértelmezett erőforrás-szolgáltatók Windows-hitelesítéssel védik a kommunikációt.|

![A hitelesítéshez szükséges információk](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Hitelesítés Azure Resource Manager

Az identitás-szolgáltatóval való hitelesítéshez és a JSON Web Token fogadásához a következő információk szükségesek:

1. **Az Identity rendszer (Authority) URL-címe**: az a URL-cím, amelyen az identitás-szolgáltató elérhető. Például *https: \//login.Windows.net*.
2. **Azure Resource Manager alkalmazás-azonosító URI-ja**: az identitás-szolgáltatónál regisztrált Azure Resource Manager egyedi azonosítója. Emellett minden Azure Stack-telepítéshez egyedi.
3. **Hitelesítő adatok**: az identitás-szolgáltatóval történő hitelesítéshez használt hitelesítő adatok.
4. **Azure Resource Manager URL-címe**: az URL-cím a Azure Resource Manager szolgáltatás helye. Például *https: \//Management.Azure.com* vagy *https: \//Management.local.azurestack.external*.

Ha egy rendszerbiztonsági tag (az ügyfél, az alkalmazások vagy a felhasználó) hitelesítési kérelmet küld az erőforrásokhoz való hozzáféréshez, a kérelemnek tartalmaznia kell a következőket:

- A rendszerbiztonsági tag hitelesítő adatai.
- Annak az erőforrásnak az alkalmazás-azonosító URI azonosítója, amelyet a résztvevő szeretne elérni.

A hitelesítő adatokat az identitás-szolgáltató ellenőrzi. Az identitás-szolgáltató azt is ellenőrzi, hogy az alkalmazás-azonosító URI-ja regisztrált alkalmazás-e, és hogy a rendszerbiztonsági tag megfelelő jogosultságokkal rendelkezik-e az adott erőforráshoz tartozó jogkivonat beszerzéséhez. Ha a kérelem érvényes, a rendszer JSON Web Token biztosít.

Ezután a tokennek továbbítania kell egy kérelem fejlécét Azure Resource Managerba. A Azure Resource Manager a következő műveleteket végzi el a megadott sorrendben:

- Ellenőrzi a *kibocsátó* (ISS) jogcímet annak ellenőrzéséhez, hogy a jogkivonat a megfelelő identitás-szolgáltatótól származik-e.
- Ellenőrzi a *célközönség* (AUD) jogcímet annak ellenőrzéséhez, hogy a jogkivonat ki lett-e állítva a Azure Resource Manager.
- Ellenőrzi, hogy a JSON Web Token az OpenID-n keresztül konfigurált tanúsítvánnyal van-e aláírva, és hogy a Azure Resource Manager ismert-e.
- Tekintse át a *kiállított* (IAT) és a *lejárati* (exp) jogcímeket annak ellenőrzéséhez, hogy a jogkivonat aktív-e, és hogy elfogadható-e.

Ha az összes érvényesítés befejeződik, a Azure Resource Manager az *objektumazonosító* (OID) és a *csoportok* jogcímeit használja a rendszerbiztonsági tag által elérhető erőforrások listájának létrehozásához.

![A jogkivonat Exchange protokoll diagramja](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Az üzembe helyezést követően Azure Active Directory globális rendszergazdai jogosultság nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra (például egy erőforrás-szolgáltatói telepítő parancsfájlra vagy egy olyan új szolgáltatásra, amely engedélyt kér). Ideiglenesen újra megadhatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés*tulajdonosa.

### <a name="use-role-based-access-control"></a>Szerepköralapú Access Control használata

Az Azure Stackben a szerepköralapú hozzáférés-vezérlés (RBAC) azonosan működik a Microsoft Azure-ban elérhető implementációval. Az erőforrásokhoz való hozzáférést úgy kezelheti, hogy a megfelelő RBAC-szerepkört rendeli hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz. A RBAC és a Azure Stack használatának módjáról a következő cikkekben talál további információt:

- [Ismerkedjen meg a Azure Portal szerepköralapú Access Controlával](/azure/role-based-access-control/overview).
- [A szerepköralapú Access Control használatával kezelheti az Azure-előfizetések erőforrásaihoz való hozzáférést](/azure/role-based-access-control/role-assignments-portal).
- [Egyéni szerepköröket hozhat létre az Azure szerepköralapú Access Controlhoz](/azure/role-based-access-control/custom-roles).
- [Szerepköralapú Access Control kezelése](azure-stack-manage-permissions.md) Azure Stackban.

### <a name="authenticate-with-azure-powershell"></a>Hitelesítés az Azure PowerShell-lel

A Azure Stack Azure PowerShell hitelesítésének részletei [a Azure stack felhasználó PowerShell-környezetének konfigurálása](../user/azure-stack-powershell-configure-user.md)című részében találhatók.

### <a name="authenticate-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

További információ a Azure Stack használatával végzett hitelesítés Azure PowerShell használatáról: [Az Azure CLI telepítése és konfigurálása a Azure stack használatával történő használathoz](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>Következő lépések

- [Identitás-architektúra](azure-stack-identity-architecture.md)
- [Adatközpont-integráció – identitás](azure-stack-integrate-identity.md)
