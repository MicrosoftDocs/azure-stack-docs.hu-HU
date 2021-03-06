---
title: A Windows felügyeleti központ regisztrálása az Azure-ban
description: A Windows felügyeleti központ átjárójának regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/05/2021
ms.openlocfilehash: 699ad4b3bcfe9c1c7738ecf9bcedbdea50a25ab2
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250380"
---
# <a name="register-windows-admin-center-with-azure"></a>A Windows felügyeleti központ regisztrálása az Azure-ban

> A Azure Stack HCI 20H2; verzióra vonatkozik. Windows Server 2019

Ha az Azure-szolgáltatásokat a Windows felügyeleti központtal szeretné használni, először [telepítenie kell a Windows felügyeleti központot](/windows-server/manage/windows-admin-center/deploy/install) egy felügyeleti számítógépre, és regisztrálnia kell a Windows felügyeleti központ átjáróját. Ez a fürt Azure-ban való [regisztrálásának](../deploy/register-with-azure.md) előfeltétele.

   > [!IMPORTANT]
   > Ha a Windows felügyeleti központot a Azure Stack HCI-fürtök regisztrálásához szeretné használni, a Windows felügyeleti központ átjáróját regisztrálni kell egy Azure AD-alkalmazás AZONOSÍTÓjában, amelyet a szervezete Azure AD-rendszergazdája jóváhagy. regisztrálja a Windows felügyeleti központot ugyanazon a felügyeleti számítógépen, amelyet a fürt (ek) ugyanazon Azure Active Directory (bérlő) azonosítója és az alkalmazás-azonosító használatával szeretne regisztrálni.

## <a name="complete-the-registration-process"></a>A regisztrációs folyamat befejezése

1. Indítsa el a Windows felügyeleti központot, és válassza a jobb felső sarokban található **Beállítások** fogaskerék ikont, amely a fiók oldalára kerül. Ezután a bal oldali **átjáró** menüjében válassza az **Azure** lehetőséget, majd kattintson a **regisztráció** elemre.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Válassza a beállítások > átjáró > az Azure elemet, majd kattintson a regisztráció elemre." lightbox="media/register-wac/register-wac.png":::

2. Egyedi kóddal kell megadnia. Kattintson a kód jobb oldalán lévő **Másolás** gombra. Kattintson **a kód megadása** gombra, amely egy másik böngészőablakot nyit meg, amelyen beillesztheti az alkalmazásban vagy az eszközön megjelenő kódot.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Másolja ki az egyedi kódot, kattintson a kód megadása gombra, és illessze be a párbeszédpanelbe." lightbox="media/register-wac/enter-code.png":::

3. A kód beillesztése után értesítést kap arról, hogy be kell jelentkeznie a Windows felügyeleti központba egy távoli eszközön vagy szolgáltatáson. Adja meg az e-mail címét vagy telefonszámát. Ha az eszköz felügyelt, akkor a rendszer a szervezet bejelentkezési lapjára irányítja a hitelesítést. Kövesse az utasításokat, és adja meg a megfelelő hitelesítő adatokat.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Bejelentkezés a Windows felügyeleti központba e-mail-cím vagy telefonszám használatával" lightbox="media/register-wac/sign-in.png":::

   A következő üzenetnek kell megjelennie: "bejelentkezett a Windows felügyeleti központ alkalmazásba az eszközön." A böngészőablak bezárásával térjen vissza az eredeti regisztrációs lapra.

4. Kapcsolódjon Azure Active Directoryhoz a Azure Active Directory (bérlői) AZONOSÍTÓjának és az alkalmazás-AZONOSÍTÓnak a beszerzésével. Ha már rendelkezik Azure-bérlői AZONOSÍTÓval, és követte az előző lépéseket, előfordulhat, hogy a bérlői azonosító mező előre ki van töltve, és több beállítást is tartalmazhat. Válassza ki a megfelelő bérlői azonosítót. 

   Ha az Azure AD-rendszergazda megadta az alkalmazás AZONOSÍTÓját, kattintson a **meglévő használata** elemre, és megjelenik egy üres mező a rendszergazda által megadott azonosító megadásához. Az azonosító megadása után a Windows felügyeleti központ megerősíti, hogy az adott AZONOSÍTÓval rendelkező fiók található. Ha rendelkezik meglévő AZONOSÍTÓval, de nem tudja, mi az, kövesse az [alábbi lépéseket](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a lekéréséhez. Ha a szervezet nem adott meg egy meglévő azonosítót, hagyja meg **Azure Active Directory az alkalmazás** **új létrehozása** lehetőséget.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Kapcsolódás Azure Active Directoryhoz a meglévő Azure Active Directory (bérlői) azonosító és alkalmazás-azonosító beszerzésével, vagy új alkalmazás-azonosító létrehozásával" lightbox="media/register-wac/connect-to-aad.png":::

5. Kattintson a **Kapcsolódás** gombra az Azure-hoz való kapcsolódáshoz. Ha Ön Azure AD-rendszergazda, vagy ha már használta az alkalmazás AZONOSÍTÓját, akkor meg kell jelennie arról, hogy az Azure AD-hez csatlakozik, ami azt jelzi, hogy a folyamat befejeződött. Ha nem, akkor a rendszergazdai jóváhagyást igénylő üzenet jelenik meg. Ha ez az eset áll fenn, válassza a **Visszatérés az alkalmazásba a jóváhagyás megadása nélkül** lehetőséget, és kérje meg az Azure ad-rendszergazdát, hogy adja meg az engedélyt a regisztráció során létrehozott új alkalmazás-azonosítóhoz az alábbi 6. lépés utasításait követve.

6. Ha Ön Azure AD-rendszergazda, adja meg az engedélyeket az Azure-ban a **Azure Active Directoryra** való navigáláshoz, majd **Alkalmazásregisztrációk**. Válassza a **minden alkalmazás** lehetőséget, és keresse meg a **WindowsAdminCenter**. Válassza ki a regisztrálni kívánt átjáró megjelenítendő nevét. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** , amely az oldal tetején jelenik meg, ahogy a felhasználónak meg kell adnia. Ezután navigáljon az **API-engedélyek** elemre. A **jóváhagyás engedélyezése** területen válassza a **rendszergazdai jóváhagyás megadása** lehetőséget. Ezután adja meg az alkalmazás AZONOSÍTÓját a felhasználónak. Ha több felhasználó esetében ugyanazt az azonosítót tervezi használni, folytassa a 7. lépéssel; Ellenkező esetben ugorjon a 8. lépésre.

7. A kényelem és a könnyű kezelhetőség érdekében lehetővé teheti, hogy egy szervezet több felhasználója is regisztrálja a Windows felügyeleti központot ugyanazzal az Azure-alkalmazás-AZONOSÍTÓval. Ehhez minden felhasználónak regisztrálnia kell az átjárókat ugyanahhoz a tartományhoz és porthoz, például például *https://localhost:6516* :. Ehhez az is szükséges, hogy az Azure AD rendszergazdája további lépéseket tegyen, és adjon hozzá átirányítási URI-ket a Azure Portal.

   A Windows felügyeleti központban válassza a jobb felső sarokban található **Beállítások** fogaskerék ikont. Ezután a bal oldali **átjáró** menüjében válassza az **Azure** lehetőséget, majd kattintson az Azure-beli **Megtekintés** elemre, amely megjeleníti az Azure ad részleteit. A Azure Portal válassza a bal oldali menüben a **> hitelesítés kezelése** lehetőséget. Az **átirányítási URI** -k mezőben megjelenik egy meglévő URI, amely az alkalmazás-azonosítóhoz regisztrált első átjárót jelképezi. Válassza az **URI hozzáadása** lehetőséget, és adjon hozzá két új átirányítási URI-t, például: *http://localhost:6516* és *https://localhost:6516* .

   :::image type="content" source="media/register-wac/add-redirect-uris.png" alt-text="Ha engedélyezni szeretné, hogy egy szervezet több felhasználója regisztrálja a Windows felügyeleti központot ugyanazzal az Azure-alkalmazás-AZONOSÍTÓval, adja hozzá az átirányítási URI" lightbox="media/register-wac/add-redirect-uris.png":::

   > [!IMPORTANT]
   > Ha az Azure AD rendszergazdája nem ad hozzá átirányítási URI-t, és több felhasználó is megpróbál regisztrálni a Windows felügyeleti központot ugyanarra az alkalmazás-AZONOSÍTÓra, a felhasználó hibaüzenetet kap, hogy a válasz URL-címe nem egyezik.

7. Ezen a ponton a felhasználónak meg kell ismételnie a regisztrációs folyamatot, ezúttal a meglévő alkalmazás-azonosító **használata** lehetőséget kell választania, és meg kell adnia az Azure ad-rendszergazda által biztosított alkalmazás-azonosítót.

8. A **Bejelentkezés** lehetőség kiválasztásával jelentkezzen be a Windows felügyeleti központba az Azure-fiókjával.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a következőkre:

- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)
- [Azure Stack HCI használata a Windows felügyeleti központtal](../get-started.md)
- [Kapcsolódás az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/)