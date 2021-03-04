---
title: A Windows felügyeleti központ regisztrálása az Azure-ban
description: A Windows felügyeleti központ átjárójának regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 93bba2336ab482e1a2bf0fc8e7545f537211382d
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116784"
---
# <a name="register-windows-admin-center-with-azure"></a>A Windows felügyeleti központ regisztrálása az Azure-ban

> Azure Stack HCI-v20H2 vonatkozik; Windows Server 2019

Ha az Azure-szolgáltatásokat a Windows felügyeleti központtal szeretné használni, először [telepítenie kell a Windows felügyeleti központot](/windows-server/manage/windows-admin-center/deploy/install) egy felügyeleti számítógépre, és regisztrálnia kell a Windows felügyeleti központ átjáróját. Ez a fürt Azure-ban való [regisztrálásának](../deploy/register-with-azure.md) előfeltétele.

   > [!IMPORTANT]
   > Regisztrálja a Windows felügyeleti központot ugyanazon a felügyeleti számítógépen, amelyet a fürt (ek) regisztrálásához kíván használni, ugyanazzal a Azure Active Directory (bérlői) AZONOSÍTÓval és alkalmazás-AZONOSÍTÓval.

## <a name="complete-the-registration-process"></a>A regisztrációs folyamat befejezése

1. Indítsa el a Windows felügyeleti központot, és kattintson a jobb felső sarokban található **Beállítások** fogaskerék ikonra, amely a fiók oldalára kerül. Ezután a bal oldali **átjáró** menüjében válassza az **Azure** lehetőséget, majd kattintson a **regisztráció** elemre.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Válassza a beállítások > átjáró > az Azure elemet, majd kattintson a regisztráció elemre." lightbox="media/register-wac/register-wac.png":::

2. Egyedi kóddal kell megadnia. Kattintson a kód jobb oldalán lévő **Másolás** gombra. Kattintson **a kód megadása** gombra, amely egy másik böngészőablakot nyit meg, amelyen beillesztheti az alkalmazásban vagy az eszközön megjelenő kódot.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Másolja ki az egyedi kódot, kattintson a kód megadása gombra, és illessze be a párbeszédpanelbe." lightbox="media/register-wac/enter-code.png":::

3. A kód beillesztése után értesítést kap arról, hogy be kell jelentkeznie a Windows felügyeleti központba egy távoli eszközön vagy szolgáltatáson. Adja meg az e-mail címét vagy telefonszámát. Ha az eszköz felügyelt, akkor a rendszer a szervezet bejelentkezési lapjára irányítja a hitelesítést. Kövesse az utasításokat, és adja meg a megfelelő hitelesítő adatokat.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Bejelentkezés a Windows felügyeleti központba e-mail-cím vagy telefonszám használatával" lightbox="media/register-wac/sign-in.png":::

   A következő üzenetnek kell megjelennie: "bejelentkezett a Windows felügyeleti központ alkalmazásba az eszközön." A böngészőablak bezárásával térjen vissza az eredeti regisztrációs lapra.

4. Kapcsolódjon Azure Active Directoryhoz a Azure Active Directory (bérlői) AZONOSÍTÓjának és az alkalmazás-AZONOSÍTÓnak a beszerzésével. Ha már rendelkezik Azure-bérlői AZONOSÍTÓval, és követte az előző lépéseket, előfordulhat, hogy a bérlői azonosító mező előre ki van töltve, és több beállítást is tartalmazhat. Válassza ki a megfelelő bérlői azonosítót. Ha az Azure AD-rendszergazda megadta az alkalmazás AZONOSÍTÓját, kattintson a **meglévő használata** elemre, és megjelenik egy üres mező a rendszergazda által megadott azonosító megadásához. Az azonosító megadása után a Windows felügyeleti központ megerősíti, hogy az adott AZONOSÍTÓval rendelkező fiók található. Ha rendelkezik meglévő AZONOSÍTÓval, de nem tudja, mi az, kövesse az [alábbi lépéseket](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a lekéréséhez. Ha a szervezet nem adott meg egy meglévő azonosítót, hagyja meg **Azure Active Directory az alkalmazás** **új létrehozása** lehetőséget.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Azure Active Directory csatlakozhat a meglévő Azure Active Directory (bérlői) azonosító megadásával, vagy újat hozhat létre" lightbox="media/register-wac/connect-to-aad.png":::

5. Kattintson a **Kapcsolódás** gombra az Azure-hoz való kapcsolódáshoz. Ha Ön Azure AD-rendszergazda, vagy meglévő alkalmazás-azonosítót használt, akkor meg kell jelennie arról, hogy most már csatlakozik az Azure AD-hez. Ha nem, akkor a rendszergazdai jóváhagyást igénylő üzenet jelenik meg. Ha ez az eset áll fenn, válassza a **Visszatérés az alkalmazásba a jóváhagyás megadása nélkül** lehetőséget, és forduljon az Azure ad-rendszergazdához, és adja meg az engedélyeket a regisztráció során létrehozott új alkalmazás-azonosítóhoz a 6. lépés utasításait követve.

6. Ha Ön Azure AD-rendszergazda, adja meg az engedélyeket az Azure-ban a **Azure Active Directoryra** való navigáláshoz, majd **Alkalmazásregisztrációk**. Válassza ki a regisztrálni kívánt átjáró után megnevezett alkalmazás-identitást, és navigáljon az **API-engedélyek** elemre. A **jóváhagyás engedélyezése** területen válassza a **rendszergazdai jóváhagyás megadása** lehetőséget.

7. Zárjuk be az ablakot, és jelentkezzen be a Windows felügyeleti központba az Azure-fiókjával.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a következőkre:

- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)
- [Azure Stack HCI használata a Windows felügyeleti központtal](../get-started.md)
- [Kapcsolódás az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/)