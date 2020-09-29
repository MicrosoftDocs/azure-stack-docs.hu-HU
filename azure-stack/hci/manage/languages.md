---
title: Nyelvek módosítása Azure Stack HCI-ben
description: Ez a témakör útmutatást nyújt az Azure Stack HCI operációs rendszer, a Windows 10, a Windows felügyeleti központ és a Microsoft Edge nyelvének módosításához.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947333"
---
# <a name="change-languages-in-azure-stack-hci"></a>Nyelvek módosítása Azure Stack HCI-ben

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A felügyelt kiszolgálókon módosíthatja az előnyben részesített nyelvet a felügyeleti számítógép, a Windows felügyeleti központ, a Microsoft Edge és a Azure Stack HCI operációs rendszer használatával. A Windows felügyeleti központ a választott nyelven marad, függetlenül attól, hogy milyen nyelvi változások vannak a többi erőforrásban.

Ez a cikk bemutatja, hogyan módosíthatja a nyelveket a alkalmazásban:
- Windows 10
- Windows felügyeleti központ
- Microsoft Edge
- A Server Core a Azure Stack HCI operációs rendszeren

## <a name="change-the-language-in-the-management-pc"></a>A felügyeleti számítógép nyelvének módosítása
Ha a Windows 10 rendszert használja a felügyeleti számítógépen, a Windows 10-es verzióban, a használt alkalmazásokban és a meglátogatott webhelyeken is telepíthet nyelvi csomagokat. Emellett módosíthatja a billentyűzet nyelvét is, és beállíthatja a szövegbeviteli nyelvet a webhelyek és az alkalmazások nyelvének megfelelő sorrendben.

A Windows 10 rendszerben használt nyelv vagy a billentyűzet nyelvének módosítása nem befolyásolja a Windows felügyeleti központ megjelenítési nyelvét.

   >[!IMPORTANT]
   > Miután módosította a nyelvet a Windows 10-es verzióban, javasoljuk, hogy a kijelentkezés előtt állítsa be az előnyben részesített billentyűzet nyelvét, hogy elkerülje a billentyűzet esetleges bemeneti problémáinak elkerülését.

További információ: [a bemeneti és megjelenítési nyelvi beállítások kezelése a Windows 10](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language)rendszerben.

## <a name="change-the-language-in-windows-admin-center"></a>A nyelv módosítása a Windows felügyeleti központban
A Windows felügyeleti központban és a régió formátumában a tartózkodási helynek megfelelően módosíthatja a nyelvet. Ezeknek a lehetőségeknek a módosítása a Windows felügyeleti központban nincs hatással a Windows 10 rendszerű felügyeleti számítógép nyelvi beállítására.

A nyelv módosítása a Windows felügyeleti központban:
1. A **minden kapcsolat** ablaktáblán válassza a **Beállítások** fogaskerék ikont, majd a **Beállítások**területen válassza a **nyelv/régió**lehetőséget.
1. Bontsa ki a **nyelv** legördülő listát, és válassza ki a kívánt nyelvet, majd bontsa ki a **területi formátum** legördülő listát egy másik régió kiválasztásához, ha szükséges.
1. Válassza **a Mentés és újratöltés**lehetőséget.

    :::image type="content" source="media/languages/language-region.png" alt-text="A Windows felügyeleti központ nyelve/régió lapja":::

További információ: a [Windows felügyeleti központ beállításai](/windows-server/manage/windows-admin-center/configure/settings).

## <a name="change-the-language-in-microsoft-edge"></a>A nyelv módosítása a Microsoft Edge-ben
Támogatott nyelveket adhat hozzá a Microsoft Edge-hez, és átrendezheti a nyelvi beállításokat a böngészőben. A fordítások beszerzéséhez hozzáadhat egy idegen nyelvi fordítói bővítményt is a böngészőhöz.

További információért lásd: a [Microsoft Edge nyelvi támogatása](/deployedge/microsoft-edge-supported-languages).

## <a name="change-the-language-in-server-core"></a>A nyelv módosítása a Server Core-ban
Ha meg kell változtatnia a nyelvet a Azure Stack HCI operációs rendszer Server Core-ban, javasoljuk, hogy a kiszolgálók fürtözése után ezt a műveletet javasolja. Támogatott nyelvi csomagokat is hozzáadhat a Server Core-hoz, majd módosíthatja a nyelvet és a billentyűzetkiosztást a használni kívánthoz. Egy Windows PowerShell-paranccsal felülbírálhatja az aktuális nyelv és a billentyűzet beviteli módszerét is.

Minden nyelvi csomag telepítve van a * \\ (z)% Language-ID%* könyvtárban. Például a *C:\Windows\System32\es-es* a spanyol nyelvi csomag helye. Minden nyelvi csomag körülbelül 50 MB. Ha az összes 38 nyelvi csomagot telepíteni szeretné, a létrehozott szükséges rendszerkép mérete körülbelül 2 GB.

További információ: [a Windowshoz elérhető nyelvek](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

Nyelvi csomagok manuális beszerzése és hozzáadása az operációs rendszerhez:

1. Adjon hozzá egy nyelvi csomagot a Server Core-hoz a **DISM/Add-WindowsPackage** eszköz használatával. A `Add-WindowsPackage` PowerShell-parancs a DISM végrehajtható fájljának megfelelője.

    További információ: [nyelvek hozzáadása Windows-lemezképekhez](/windows-hardware/manufacture/desktop/add-language-packs-to-windows).

1. Javasoljuk, hogy az igény szerinti nyelvi szolgáltatások (FODs) hozzáadásával engedélyezze a hozzáadott nyelv további funkcióit, az alábbi példában látható módon:

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    További információért lásd: [igény szerinti nyelvi és területi funkciók (Franciaország)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod).

1. A PowerShell **set-WinUILanguageOverride** parancsmag használatával módosíthatja a Windows felhasználói felületének nyelvét az aktuális felhasználói fiók operációs rendszerében. A következő példában a `de-DE` német nyelvet adja meg az operációs rendszer jelenlegi nyelvi beállításaként:

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    További információt a [set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps)című témakörben talál.

## <a name="next-steps"></a>Következő lépések
További információért lásd még:

- [Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz](./add-cluster.md)
