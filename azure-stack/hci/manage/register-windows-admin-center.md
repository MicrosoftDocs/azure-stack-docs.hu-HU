---
title: A Windows felügyeleti központ regisztrálása az Azure-ban
description: A Windows felügyeleti központ regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: ee6794c0798bc388bc3d9313665eb0b7917cf8eb
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867334"
---
# <a name="register-windows-admin-center-with-azure"></a>A Windows felügyeleti központ regisztrálása az Azure-ban

> Azure Stack HCI-v20H2 vonatkozik; Windows Server 2019

Ha az Azure-szolgáltatásokat a Windows felügyeleti központtal szeretné használni, először telepítenie kell a Windows felügyeleti központot egy felügyeleti számítógépre, és végre kell hajtania egy egyszeri regisztrációt.

## <a name="complete-the-registration-process-in-windows-admin-center"></a>A regisztrációs folyamat befejezése a Windows felügyeleti központban

1. Indítsa el a Windows felügyeleti központot, és kattintson a jobb felső sarokban található **Beállítások** fogaskerék ikonra, amely a fiók oldalára kerül. Ezután a bal oldali **átjáró** menüjében válassza az **Azure**lehetőséget, majd kattintson a **regisztráció**elemre.
1. Egyedi kóddal kell megadnia. Kattintson a kód jobb oldalán lévő **Másolás** gombra.
1. Kattintson **a kód megadása**gombra, amely egy másik böngészőablakot nyit meg, amelyen beillesztheti az alkalmazásban vagy az eszközön megjelenő kódot.
1. A kód beillesztése után értesítést kap arról, hogy be kell jelentkeznie a Windows felügyeleti központba egy távoli eszközön vagy szolgáltatáson. 
1. Adja meg az e-mail címét vagy telefonszámát. Ha az eszköz felügyelt, akkor a rendszer a szervezet bejelentkezési lapjára irányítja a hitelesítést. Kövesse az utasításokat, és adja meg a megfelelő hitelesítő adatokat.
1. A következő üzenetnek kell megjelennie: "bejelentkezett a Windows felügyeleti központ alkalmazásba az eszközön." A böngészőablak bezárásával térjen vissza az eredeti regisztrációs lapra.
1. A Azure Active Directory (bérlői) azonosító megadásával csatlakozhat a Azure Active Directoryhoz. Ha követte az előző lépéseket, a rendszer előre kitölti az azonosító mezőt. Ha a szervezet nem ad meg egy meglévő azonosítót, hagyja meg **Azure Active Directory új alkalmazás** **létrehozása** lehetőséget. Ha már rendelkezik AZONOSÍTÓval, kattintson a **meglévő használata**elemre, és a rendszergazda által megadott azonosító megadásához egy üres mező jelenik meg. Az azonosító megadása után a Windows felügyeleti központ megerősíti, hogy az adott AZONOSÍTÓval rendelkező fiók található. Ha rendelkezik meglévő AZONOSÍTÓval, de nem tudja, mi az, kövesse az [alábbi lépéseket](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a lekéréséhez.
1. Kattintson a **Kapcsolódás** gombra az Azure-hoz való kapcsolódáshoz. Meg kell jelennie arról, hogy most már kapcsolódott az Azure AD-hez.
1. Engedélyeket adhat meg az Azure-ban az Azure Portalban az **alkalmazás engedélyeivel** . A **jóváhagyás engedélyezése**területen válassza a **rendszergazdai jóváhagyás megadása**lehetőséget.
1. Zárjuk be az ablakot, és jelentkezzen be a Windows felügyeleti központba az Azure-fiókjával.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a következőkre:

- [Azure Stack HCI használata a Windows felügyeleti központtal](../get-started.md)
- [Kapcsolódás az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/)