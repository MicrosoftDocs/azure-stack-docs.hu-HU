---
title: Az Azure stack-identitás – áttekintés |} A Microsoft Docs
description: Ismerje meg az identitáskezelési rendszerek is használhatja az Azure Stack használatával.
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
ms.openlocfilehash: f57ded9df4fe799a5795ee541f7a03e650202aab
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459053"
---
# <a name="overview-of-identity-for-azure-stack"></a>Az Azure stack-identitás – áttekintés

Az Azure Stack az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltatóként az Active Directory által támogatott igényel. A szolgáltató, amely egy egyszeri döntés, hogy az első Azure Stack üzembe. A fogalmakat és engedélyezési részleteket ebben a cikkben segíthet a Identitásszolgáltatók közül választhat.

A kiválasztott Azure AD vagy az AD FS az Azure Stack üzembe mód határozzák meg:

- Amikor telepít egy csatlakoztatott módban, vagy az Azure AD vagy AD FS is használhatja.
- Kapcsolat nélküli módban, az internethez, kapcsolat nélkül telepítésekor csak az AD FS használata támogatott.

További információ a lehetőségeket, amelyek függnek az Azure Stack környezettel, tekintse meg a következő cikkeket:

- Az Azure Stack and deployment kit: [Identitás szempontok](azure-stack-datacenter-integration.md#identity-considerations).
- Az Azure Stackkel integrált rendszerek: [Üzembe helyezési, tervezési megfontolások az Azure Stack integrált rendszerek](azure-stack-connection-models.md).

## <a name="common-concepts-for-identity"></a>Közös fogalmait identitás

A következő szakaszok tárgyalják általánosan használt fogalmat identitás-szolgáltatóktól és azok használatáról, az Azure Stackben.

![Identitás-szolgáltatóktól terminológiája](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Directory-bérlők és a szervezetek számára

Egy könyvtár egy olyan tároló, kapcsolatos információkat tartalmazó *felhasználók*, *alkalmazások*, *csoportok*, és *egyszerű szolgáltatások*.

Címtárbérlő van egy *szervezet*, például a Microsoft vagy a saját vállalat.

- Az Azure AD támogatja a több-bérlős működést, valamint akár több szervezetet is, mindegyiket külön címtárban. Ha használja az Azure ad-ben, és több bérlővel rendelkezik, meg lehet adni, alkalmazások és a egy bérlő hozzáférés felhasználók más bérlők is ugyanazt a címtárat.
- Az AD FS támogatja a csak egyetlen új bérlő, ezért csak egyetlen szervezet.

### <a name="users-and-groups"></a>Felhasználók és csoportok

Felhasználói fiókok (azonosítók) lesznek a standard szintű fiók, amely a felhasználók hitelesítése egy felhasználói azonosító és jelszó használatával. Csoportok tartalmazhatják, felhasználókat vagy további csoportokat.

Az identitáskezelési megoldás használata függ, hogyan hozzon létre és kezelheti a felhasználókat és csoportokat.

Az Azure Stackben, felhasználói fiókok:

- Hozza létre a rendszer a *felhasználónév\@tartomány* formátumban. Bár az AD FS felhasználói fiókok képez le egy Active Directory-példányból, az AD FS használatát nem támogatja a  *\\ \<tartomány >\\\<alias >* formátumban.
- Beállíthatja a multi-factor authentication használatára.
- Korlátozva arra a könyvtárra, ahol azok először regisztrálnia, amely a szervezet címtárában.
- A helyszíni címtárakban importálhatók. További információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Ha a szervezet bérlői portál jelentkezik be, használhatja a *https:\//portal.local.azurestack.external* URL-CÍMÉT. Amikor bejelentkezik az Azure Stack portálon eltérő Azure Stack regisztrálásához használt, a tartománynév regisztrálása az Azure Stack segítségével kell hozzáfűzi a portál URL-címét. Például, ha a fabrikam.onmicrosoft.com Azure Stack regisztrálva van, és a bejelentkezett felhasználói fiók van admin@contoso.com, lenne használatával jelentkezzen be a felhasználói portál URL-címe: https:\//portal.local.azurestack.external/ Fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Vendégfelhasználók

Más címtárbérlők, hogy kapott hozzáférést az erőforrásokhoz a címtárban lévő felhasználói fiókok, a vendég felhasználók. Vendég felhasználók támogatására, használja az Azure ad-ben, és több-bérlős támogatásának engedélyezése. Ha támogatási engedélyezve van, meghívhatja a vendégfelhasználók a directory-bérlőjéhez, amely lehetővé teszi az együttműködés külső szervezetekkel erőforrásaihoz.

A vendégfelhasználók meghívása a felhő üzemeltetői és a felhasználók használható [Azure AD B2B együttműködés](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Meghívott felhasználók hozzáférhetnek a dokumentumok, erőforrások és alkalmazások a címtárból, és Ön szabályozhatja a saját erőforrások és adatok. 

Meg vendégként bejelentkezhet egy másik szervezet directory-bérlőhöz. Ehhez a szervezet könyvtárnév hozzáfűzése a portál URL-CÍMÉT. Ha például a Contoso szervezetben tartozik, és jelentkezzen be a Fabrikam könyvtárat szeretne, ha a HTTPS protokollt használja:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Alkalmazások

Az Azure AD vagy AD FS-alkalmazások regisztrálását, és ezután kínálnak az alkalmazásokat a felhasználók a szervezetben.

Alkalmazások a következők:

- **Webes alkalmazás**: Ilyenek például az Azure portal és az Azure Resource Manager. Támogatják a webes API-hívások.
- **Natív ügyfél**: Ilyenek például az Azure PowerShell, a Visual Studio és az Azure parancssori felület.

Alkalmazások bérlős két típusú támogatják:

- **Egybérlős**: Támogatja a felhasználók és a szolgáltatások csak az ugyanabban a könyvtárban, ahol az alkalmazás regisztrálva van-e.

  > [!NOTE]
  > Mivel az AD FS támogatja a csak egyetlen címtárban, alkalmazások hoz létre egy AD FS-topológia olyan, a kialakításból fakadóan egybérlős alkalmazások.

- **Több-bérlős**: Felhasználók és a könyvtárban, ahol az alkalmazás regisztrálva van-e és a bérlői további címtárak szolgáltatásai által használatát támogatja. Több-bérlős alkalmazások, a felhasználók egy másik bérlőben (egy másik Azure AD-bérlő) könyvtárat is, jelentkezzen be az alkalmazást. 

  Több-bérlős kapcsolatos további információkért lásd: [több bérlős üzemmód engedélyezése](azure-stack-enable-multitenancy.md).

  Egy több-bérlős alkalmazás fejlesztésével kapcsolatos további információkért lásd: [több-bérlős alkalmazások](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Amikor regisztrál egy alkalmazás, hozzon létre két objektum:

- **Alkalmazásobjektum**: Az alkalmazás az összes bérlőre kiterjedő globális ábrázolása. Ezt a kapcsolatot egy az egyhez típusú szoftverek alkalmazásával és létezik csak a könyvtárban, ahol az alkalmazás regisztrálva van.

- **Szolgáltatásnév-objektum**: A könyvtárban, ahol az alkalmazás regisztrálva van egy alkalmazás számára létrehozott hitelesítő adatokat. Egy egyszerű szolgáltatást is jön létre a címtárban, az egyes további bérlők, ahol az alkalmazás szolgál. Ez a kapcsolat egy-a-többhöz rendelkező a szoftver alkalmazás lehet.

Alkalmazás és egyszerű szolgáltatási objektumok kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Az egyszerű szolgáltatások

Egyszerű szolgáltatás olyan készlete, *hitelesítő adatok* adott alkalmazás vagy szolgáltatás, amely az Azure Stackben erőforrásokhoz való hozzáférést. Egyszerű szolgáltatás használata elkülöníti az alkalmazás a felhasználó alkalmazás engedélyeit.

Egyszerű szolgáltatás jön létre az egyes bérlők, ahol az alkalmazás használata. Az egyszerű szolgáltatás be- és hozzáférés a bérlő által védett erőforrások (például a felhasználók számára) egy identitást hoz létre.

- Egy egybérlős alkalmazást csak egy szolgáltatásnevet, a könyvtárban, ahol azt először létre rendelkezik. Egyszerű szolgáltatás jön létre, és hozzájárul az alkalmazás regisztrációja során használt.
- Egy webalkalmazás több-bérlős vagy API már minden bérlő létrehozott egyszerű szolgáltatás ahol a bérlőt a felhasználó jóváhagy az alkalmazás használatát.

Az egyszerű szolgáltatások hitelesítő adatait az Azure Portalon létrehozott kulcs, vagy a tanúsítvány lehet. Egy tanúsítvány használatát az automation esetén használhatók, mert tanúsítványok biztonságosabb, mint a kulcsok számítanak. 

> [!NOTE]
> Az AD FS az Azure Stack használatakor csak a rendszergazdák egyszerű szolgáltatásokat hozhat létre. Az AD FS-sel az egyszerű szolgáltatásnevekről tanúsítványokra van szükségük, és jönnek létre a kiemelt végponthoz (EGP) keresztül. További információkért lásd: [alkalmazások elérést biztosíthat az Azure Stack](azure-stack-create-service-principals.md).

Az Azure Stack szolgáltatásnevekkel kapcsolatos tudnivalókért lásd: [szolgáltatásnevek létrehozása](azure-stack-create-service-principals.md).

### <a name="services"></a>Szolgáltatások

Azure Stack szolgáltatásban, hogy az identitásszolgáltató együttműködő identitásszolgáltató-alkalmazásokként vannak regisztrálva. Alkalmazások, például a regisztráció lehetővé teszi, hogy egy szolgáltatást, hogy az identitás-rendszer a hitelesítéshez.

Azure-szolgáltatásokhoz használatához [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokollok és [JSON webes jogkivonatainak](/azure/active-directory/develop/active-directory-token-and-claims) a személyazonosságukat. Mivel az Azure AD és az AD FS-protokollokra egységesen, akkor használhatja [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) végeznek helyszíni hitelesítést vagy az Azure-ba (a csatlakoztatott forgatókönyv). Az adal-KÓDTÁRRAL is használhatja eszközök, például az Azure PowerShell és az Azure CLI-vel a több felhőre kiterjedő és a helyszíni erőforrás-kezelést.

### <a name="identities-and-your-identity-system"></a>Az identitások és a identitásrendszer

Azure stack-identitások felhasználói fiókok, csoportok és az egyszerű szolgáltatások közé tartozik.

Azure Stack, a számos beépített alkalmazások és szolgáltatások telepítésekor automatikusan regisztrálja az identitásszolgáltató a directory-bérlőben. Egyes szolgáltatások, amelyeket regisztrálni felügyeleti célokra szolgálnak. Egyéb szolgáltatások érhetők el a felhasználók számára. Az alapértelmezett regisztrációk alapvető szolgáltatások identitások, amelyek képesek interakciót is egymással, és később hozzáadott identitásokkal biztosítanak.

Ha beállította az Azure AD a több-bérlős, egyes alkalmazások az új címtárakhoz propagálása.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

### <a name="authentication-by-applications-and-users"></a>Alkalmazások és a felhasználók hitelesítése

![Az Azure Stack rétegek között identitás](media/azure-stack-identity-overview/identity-layers.png)

Alkalmazások és felhasználók számára az Azure Stack-architektúra négy réteget írja le. Ezek a rétegek közötti interakciók használható hitelesítési típust.

|Réteg    |Rétegek közötti hitelesítés  |
|---------|---------|
|Eszközök és ügyfelek, például a felügyeleti portálon     | Eszközök eléréséhez, vagy módosítani az erőforrást az Azure Stackben, és az ügyfelek használják a [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) egy hívás az Azure Resource Manager. <br>Az Azure Resource Manager ellenőrzi a JSON Web Token, és betekintés a *jogcímek* engedélyezési szinten becslése érdekében a kiállított jogkivonat a felhasználó vagy szolgáltatás rendelkezik az Azure Stackben. |
|Az Azure Resource Manager és az alapvető szolgáltatások     |Az Azure Resource Manager kommunikál a kommunikáció átviteléhez a felhasználók erőforrás-szolgáltatók. <br> Használat továbbítja *közvetlen imperatív* hívások vagy *deklaratív* keresztül meghívja [Azure Resource Manager-sablonok](/azure-stack/user/azure-stack-arm-templates).|
|Erőforrás-szolgáltatók     |Erőforrás-szolgáltatók átadott hívások tanúsítványalapú hitelesítéssel biztonságosak. <br>Az Azure Resource Manager és az erőforrás-szolgáltató egy API-n keresztüli kommunikáció majd maradjon. Minden hívás, amely az Azure Resource Manager érkezik az erőforrás-szolgáltató érvényesíti a hívást a tanúsítvánnyal.|
|Infrastruktúra és az üzleti logika     |Erőforrás-szolgáltatók tetszés szerinti hitelesítési mód használatával kommunikálnak az üzleti logikát és infrastruktúra. Az alapértelmezés szerinti erőforrás-szolgáltatók, amely az Azure Stack használatával, hogy a Windows-hitelesítés a kommunikáció védelméhez.|

![A hitelesítéshez szükséges információk](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Hitelesítés az Azure Resource Manager

Az identitásszolgáltató a hitelesítéshez, és megjelenik egy JSON Web Token, a következő információkat kell rendelkeznie:

1. **Az identitás rendszerhez (szolgáltató) URL-cím**: Az URL-cím, amelyen az identitásszolgáltató érhető el. Ha például *https:\//login.windows.net*.
2. **Alkalmazásazonosító URI-t az Azure Resource Manager**: Egyedi azonosítója az Azure Resource Manager, amely regisztrálva van az identitásszolgáltatóval. Emellett akkor is csak az egyes Azure Stack-telepítés.
3. **hitelesítő adatok**: A hitelesítő adatokat az identitásszolgáltatónál történő hitelesítésre használható.
4. **URL-cím az Azure Resource Manager**: Az URL-címet az a hely az Azure Resource Manager-szolgáltatás. Ha például *https:\//management.azure.com* vagy *https:\//management.local.azurestack.external*.

Amikor egy egyszerű (egy ügyfél, alkalmazás vagy felhasználó) hitelesítési kérelmet egy erőforráshoz való hozzáféréshez, a kérésnek tartalmaznia kell:

- Az egyszerű hitelesítő adatokat.
- Az alkalmazás Alkalmazásazonosító URI-ja az erőforrás, amely az egyszerű szeretne hozzáférni.

Az identitásszolgáltató által a hitelesítő adatok ellenőrzése megtörtént. Az identitásszolgáltató is ellenőrzi, hogy az alkalmazás Alkalmazásazonosító URI-ja regisztrált alkalmazásra, és, hogy a rendszerbiztonsági tag egy adott erőforrás-jogkivonat beszerzése a megfelelő jogosultságokkal rendelkezik-e. Ha a kérés érvényes, a JSON Web Token kapnak.

A jogkivonatot az Azure Resource Manager ezután a kérelem fejlécében meg kell felelnie. Az Azure Resource Manager a következő nem meghatározott sorrendben hajtja végre:

- Ellenőrzi a *kibocsátó* (iss) jogcím meggyőződni arról, hogy a jogkivonat a megfelelő identitásszolgáltatótól.
- Ellenőrzi a *célközönség* (aud) jogcím annak ellenőrzéséhez, hogy a jogkivonat kiállított Azure Resource Manager.
- Ellenőrzi, hogy a JSON Web Token van a tanúsítvánnyal aláírt OpenID keresztül konfigurált az Azure Resource Manager ismert.
- Tekintse át a *kiállított* (iat) és *lejárati* (exp), győződjön meg arról, hogy a jogkivonat aktív jogcímek és az elfogadható.

Ha minden ellenőrzés befejeződött, az Azure Resource Manager használ a *hozzáadást* (oid) és a *csoportok* jogcímeket, hogy a rendszerbiztonsági tag által elérhető erőforrások listáját.

![A jogkivonatcsere protokoll ábrája](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Az üzembe helyezést követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen újbóli regisztrációra a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.

### <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure Stackben a szerepköralapú hozzáférés-vezérlés (RBAC) azonosan működik a Microsoft Azure-ban elérhető implementációval. Az erőforrásokhoz való hozzáférést a megfelelő RBAC-szerepkörök a felhasználókhoz, a csoportokhoz és alkalmazásokhoz való hozzárendelésével szabályozhatja.
RBAC használata az Azure Stack használatával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure Portalon](/azure/role-based-access-control/overview).
- [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](/azure/role-based-access-control/role-assignments-portal).
- [Egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/custom-roles).
- [Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md) az Azure Stackben.

### <a name="authenticate-with-azure-powershell"></a>Hitelesítés az Azure PowerShell használatával

Azure PowerShell használatával történő hitelesítéséhez az Azure Stack használatával kapcsolatos részleteket tekinthet meg [konfigurálása a PowerShell-környezet az Azure Stack felhasználói](../user/azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

Azure PowerShell használatával történő hitelesítéséhez az Azure Stack használatával kapcsolatos információkért lásd: [telepítése és konfigurálása az Azure CLI az Azure Stackkel való használathoz](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>További lépések

- [Identitás-architektúra](azure-stack-identity-architecture.md)
- [Adatközpont integrációja - identitás](azure-stack-integrate-identity.md)
