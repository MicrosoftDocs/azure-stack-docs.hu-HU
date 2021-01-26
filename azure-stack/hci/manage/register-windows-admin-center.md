---
title: A Windows felügyeleti központ regisztrálása az Azure-ban
description: A Windows felügyeleti központ átjárójának regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/25/2021
ms.openlocfilehash: c2067387d7b03252e7a2cc93aeddcb68dbd4bc83
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810945"
---
# <a name="register-windows-admin-center-with-azure"></a>A Windows felügyeleti központ regisztrálása az Azure-ban

> Azure Stack HCI-v20H2 vonatkozik; Windows Server 2019

Ha az Azure-szolgáltatásokat a Windows felügyeleti központtal szeretné használni, először telepítenie kell a Windows felügyeleti központot egy felügyeleti számítógépre, és el kell végeznie a Windows felügyeleti központ átjárójának egyszeri regisztrációját. Ez a fürt Azure-ban való regisztrálásának előfeltétele, és ugyanazon a felügyeleti számítógépen kell történnie, amelyet a [fürt regisztrációs](../deploy/register-with-azure.md) folyamatának elvégzéséhez kíván használni.

## <a name="complete-the-gateway-registration-process-using-windows-admin-center"></a>Az átjáró regisztrációs folyamatának befejezése a Windows felügyeleti központ használatával

1. Indítsa el a Windows felügyeleti központot, és kattintson a jobb felső sarokban található **Beállítások** fogaskerék ikonra, amely a fiók oldalára kerül. Ezután a bal oldali **átjáró** menüjében válassza az **Azure** lehetőséget, majd kattintson a **regisztráció** elemre.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Válassza a beállítások > átjáró > az Azure elemet, majd kattintson a regisztráció elemre." lightbox="media/register-wac/register-wac.png":::

2. Egyedi kóddal kell megadnia. Kattintson a kód jobb oldalán lévő **Másolás** gombra. Kattintson **a kód megadása** gombra, amely egy másik böngészőablakot nyit meg, amelyen beillesztheti az alkalmazásban vagy az eszközön megjelenő kódot.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Másolja ki az egyedi kódot, kattintson a kód megadása gombra, és illessze be a párbeszédpanelbe." lightbox="media/register-wac/enter-code.png":::

3. A kód beillesztése után értesítést kap arról, hogy be kell jelentkeznie a Windows felügyeleti központba egy távoli eszközön vagy szolgáltatáson. Adja meg az e-mail címét vagy telefonszámát. Ha az eszköz felügyelt, akkor a rendszer a szervezet bejelentkezési lapjára irányítja a hitelesítést. Kövesse az utasításokat, és adja meg a megfelelő hitelesítő adatokat.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Bejelentkezés a Windows felügyeleti központba e-mail-cím vagy telefonszám használatával" lightbox="media/register-wac/sign-in.png":::

   A következő üzenetnek kell megjelennie: "bejelentkezett a Windows felügyeleti központ alkalmazásba az eszközön." A böngészőablak bezárásával térjen vissza az eredeti regisztrációs lapra.

4. A Azure Active Directory (bérlői) azonosító megadásával csatlakozhat a Azure Active Directoryhoz. Ha követte az előző lépéseket, a rendszer előre kitölti az azonosító mezőt. Ha a szervezet nem ad meg egy meglévő azonosítót, hagyja meg **Azure Active Directory új alkalmazás** **létrehozása** lehetőséget. Ha már rendelkezik AZONOSÍTÓval, kattintson a **meglévő használata** elemre, és a rendszergazda által megadott azonosító megadásához egy üres mező jelenik meg. Az azonosító megadása után a Windows felügyeleti központ megerősíti, hogy az adott AZONOSÍTÓval rendelkező fiók található. Ha rendelkezik meglévő AZONOSÍTÓval, de nem tudja, mi az, kövesse az [alábbi lépéseket](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a lekéréséhez.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Azure Active Directory csatlakozhat a meglévő Azure Active Directory (bérlői) azonosító megadásával, vagy újat hozhat létre" lightbox="media/register-wac/connect-to-aad.png":::

5. Kattintson a **Kapcsolódás** gombra az Azure-hoz való kapcsolódáshoz. Meg kell jelennie arról, hogy most már kapcsolódott az Azure AD-hez.

6. Engedélyeket adhat meg az Azure-ban az Azure Portalban az **alkalmazás engedélyeivel** . A **jóváhagyás engedélyezése** területen válassza a **rendszergazdai jóváhagyás megadása** lehetőséget.

7. Zárjuk be az ablakot, és jelentkezzen be a Windows felügyeleti központba az Azure-fiókjával.

## <a name="next-steps"></a>További lépések

Most már készen áll a következőkre:

- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)
- [Azure Stack HCI használata a Windows felügyeleti központtal](../get-started.md)
- [Kapcsolódás az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/)