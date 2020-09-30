---
title: A Azure Stack HCI operációs rendszer üzembe helyezése
description: Ismerje meg, hogyan helyezheti üzembe a Azure Stack HCI operációs rendszert, majd a Windows felügyeleti központ használatával csatlakozhat a kiszolgálókhoz. Ismerkedjen meg a kiszolgálófürt létrehozásával, valamint annak megismerésével, hogyan kérheti le a legújabb Windows-frissítéseket és belső vezérlőprogramot a kiszolgálókhoz.
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: cf34506f5fbeec1c6e0531eda231219ae2949b59
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572602"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>A Azure Stack HCI operációs rendszer üzembe helyezése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Miután [elvégezte a lépéseket a Azure stack HCI üzembe helyezése előtt](before-you-start.md#install-windows-admin-center), az Azure stack HCI üzembe helyezésének első lépéseként [töltse le Azure stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -t, és telepítse az operációs rendszert minden olyan kiszolgálóra, amelyet fürtbe szeretne helyezni. Ez a cikk az operációs rendszer központi telepítésének különböző módszereit ismerteti, és a Windows felügyeleti központ használatával csatlakozik a kiszolgálókhoz.

Az operációs rendszer telepítése után készen áll a kiszolgálófürt létrehozására vonatkozó útmutatás használatára, valamint a kiszolgálók legújabb Windows-frissítéseinek és belső vezérlőprogram-frissítéseinek beszerzésére az [Azure stack HCI-fürt létrehozása](create-cluster.md)című témakörben leírtak szerint.

## <a name="prerequisites"></a>Előfeltételek

- A Windows felügyeleti központ olyan rendszerre van beállítva, amely hozzáfér a fürthöz használni kívánt kiszolgálókhoz, a [Azure stack HCI telepítése előtt](before-you-start.md#install-windows-admin-center)ismertetett módon.
- Egy Azure Stack HCI-megoldás, amely a Microsoft által ellenőrzött hardvereket biztosít a kívánt hardvergyártó, a Azure Stack HCI operációs rendszer és az Azure-szolgáltatások számára, a [Azure stack HCI-megoldásokban](https://azure.microsoft.com/products/azure-stack/hci/)leírtak szerint.

## <a name="deployment-preparation"></a>Üzembe helyezés előkészítése

Miután megszerezte a Azure Stack HCI-megoldás kiszolgálói hardverét, itt az ideje, hogy az állványt és a kábelt csatlakoztassa. A következő lépésekkel előkészítheti a kiszolgálói hardvert az operációs rendszer központi telepítéséhez.

1. A kiszolgálófürtön használni kívánt összes kiszolgálói csomópont összekötése.
1. Csatlakoztatja a kiszolgáló-csomópontokat a hálózati kapcsolókhoz.
1. Konfigurálja a kiszolgálók BIOS-át vagy Unified Extensible Firmware Interfaceét (UEFI) úgy, hogy az Azure Stack HCI hardvergyártó által ajánlott módon maximalizálja a teljesítményt és a megbízhatóságot.

## <a name="operating-system-deployment-options"></a>Operációs rendszer központi telepítési beállításai

A Azure Stack HCI operációs rendszert ugyanúgy telepítheti, mint a más Microsoft operációs rendszerek üzembe helyezéséhez:

- A kiszolgáló gyártójának előtelepítése.
- Fej nélküli üzembe helyezés válaszfájl használatával.
- System Center Virtual Machine Manager (VMM).
- Hálózati telepítés.
- Manuális üzembe helyezéshez csatlakoztassa a billentyűzetet és a monitort közvetlenül az adatközpontjában található kiszolgálói hardverhez, vagy egy KVM-hardvereszközt csatlakoztathat a kiszolgálói hardverhez.

### <a name="server-manufacturer-preinstallation"></a>Kiszolgáló gyártójának előtelepítése

A Azure Stack HCI operációs rendszer nagyvállalati szintű üzembe helyezéséhez javasoljuk, hogy Azure Stack HCI integrált rendszermegoldási hardvert a kívánt hardveres partnertől. A megoldás hardvere előre telepített operációs rendszerrel érkezik, és támogatja az illesztőprogramok és a belső vezérlőprogram telepítését és frissítését a Windows felügyeleti központban a hardver gyártójától.

A megoldási hardverek 2 – 16 csomópontra terjednek, és a Microsoft és a partnerek gyártói tesztelik és érvényesítik azokat. Azure Stack HCI-megoldás hardverének az előnyben részesített hardveres partnertől való megtalálásához tekintse meg a [Azure stack HCI katalógust](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog).

### <a name="headless-deployment"></a>Fej nélküli üzembe helyezés

A válaszfájl használatával az operációs rendszer fej nélküli központi telepítését végezheti el. A válaszfájl egy XML-formátum használatával határozza meg a konfigurációs beállításokat és az értékeket az operációs rendszer felügyelet nélküli telepítése során.

Ennél a központi telepítési beállításnál a Windows rendszerlemezkép-kezelővel létrehozhat egy unattend.xml válaszfájlt az operációs rendszer központi telepítéséhez a kiszolgálókon. A Windows System rendszerkép-kezelő egy grafikus eszközön keresztül hozza létre a felügyelet nélküli válaszfájlt, amely meghatározza a "válaszokat" a konfigurációs kérdésekre, majd gondoskodik a fájl helyes formátumáról és szintaxisáról.
A Windows System rendszerkép-kezelő eszköz a Windows Assessment and Deployment Kit (Windows ADK) csomagban érhető el. Első lépések: [töltse le és telepítse a Windows ADK](/windows-hardware/get-started/adk-install).

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>System Center Virtual Machine Manager (VMM) üzembe helyezése

System Center Virtual Machine Manager (VMM) a System Center csomag része. A VMM használatával üzembe helyezheti az Azure Stack HCI operációs rendszert operációs rendszer nélküli hardvereken, valamint a kiszolgálók fürtjét is. További információ a VMM: [System Center Virtual Machine Manager rendszerkövetelményei](/system-center/vmm/system-requirements).

További információ a VMM operációs rendszer nélküli központi telepítésének használatáról: [Hyper-V-gazdagép vagy-fürt](/system-center/vmm/hyper-v-bare-metal)üzembe helyezése operációs rendszer nélküli számítógépekről.

### <a name="network-deployment"></a>Hálózati telepítés

Egy másik lehetőség az Azure Stack HCI operációs rendszer telepítése a hálózaton keresztül a [Központi Windows-telepítési szolgáltatások](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11))használatával.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

Ha manuálisan szeretné telepíteni a Azure Stack HCI operációs rendszert az egyes kiszolgálók rendszermeghajtóján, telepítse az operációs rendszert a kívánt módszerrel, például DVD-ről vagy USB-meghajtóról történő rendszerindításhoz. A kiszolgálók fürtözéshez való előkészítéséhez végezze el a telepítési folyamatot a kiszolgálói konfigurációs eszköz (Sconfig) használatával. További információ az eszközről: [Server Core telepítés konfigurálása a Sconfig](/windows-server/get-started/sconfig-on-ws2016).

Az Azure Stack HCI operációs rendszer manuális telepítése:
1. Indítsa el a Azure Stack HCI telepítése varázslót azon kiszolgáló rendszermeghajtóján, amelyre telepíteni kívánja az operációs rendszert.
1. Válassza ki a telepíteni kívánt nyelvet, vagy fogadja el az alapértelmezett nyelvi beállításokat, kattintson a **tovább**gombra, majd a varázsló következő oldalán válassza a **Telepítés most**lehetőséget.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

1. A vonatkozó közlemények és licencfeltételek lapon tekintse át a licencfeltételeket, jelölje be az **Elfogadom a licencfeltételeket** jelölőnégyzetet, majd kattintson a **tovább**gombra.
1. Milyen típusú telepítési típust szeretne? lapon válassza **az egyéni: telepítse a csak Azure stack HCI újabb verzióját (speciális)**.

    > [!NOTE]
    > Az operációs rendszer ezen kiadása nem támogatja a frissítési telepítéseket.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

1. A hová szeretné telepíteni a Azure Stack HCI-t? lapon erősítse meg azt a meghajtót, ahová telepíteni szeretné az operációs rendszert, vagy frissítse, majd válassza a **tovább**lehetőséget.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

1. Az Azure Stack HCI telepítése lap megjeleníti a folyamat állapotának megjelenítését.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

    > [!NOTE]
    > A telepítési folyamat kétszer újraindítja az operációs rendszert a folyamat befejezéséhez, és a rendszergazdai Parancssor megnyitása előtt értesítéseket jelenít meg a szolgáltatások elindításáról.

1. A rendszergazdai parancssorban kattintson az **OK** gombra a felhasználó jelszavának módosításához, mielőtt bejelentkezne az operációs rendszerbe, majd nyomja le az ENTER billentyűt.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

1. Az új hitelesítő adatok megadása a rendszergazda számára párbeszédpanelen adjon meg egy új jelszót, írja be újra a megerősítéshez, majd nyomja le az ENTER billentyűt.
1. A jelszó megadásának megerősítése megtörtént, nyomja le az ENTER billentyűt.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja.":::

Most már készen áll arra, hogy a kiszolgálói konfigurációs eszközt (Sconfig) használja a fontos feladatok elvégzéséhez. A Sconfig használatához jelentkezzen be az Azure Stack HCI operációs rendszert futtató kiszolgálóra. Ez lehet helyileg billentyűzettel és figyelővel, illetve távfelügyeleti (fej-vagy BMC-) vezérlő vagy Távoli asztal használatával. A Sconfig eszköz automatikusan megnyílik, amikor bejelentkezik a kiszolgálóra.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="A Azure Stack HCI telepítése varázsló nyelv lapja." lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

A Sconfig eszköz főoldalán a következő kezdeti konfigurációs feladatokat végezheti el:
- Konfigurálja a hálózatkezelést, vagy ellenőrizze, hogy a hálózat automatikusan lett-e konfigurálva Dynamic Host Configuration Protocol (DHCP) használatával.
- Nevezze át a kiszolgálót, ha az alapértelmezett automatikusan generált kiszolgálónév nem megfelelő.
- Csatlakoztassa a kiszolgálót egy Active Directory tartományhoz.
- Adja hozzá a tartományi felhasználói fiókot vagy a kijelölt tartományi csoportot a helyi rendszergazdákhoz.
- Engedélyezze a Rendszerfelügyeleti webszolgáltatások (WinRM) elérését, ha azt tervezi, hogy a kiszolgálót a helyi alhálózaton kívülről felügyeli, és úgy döntött, hogy még nem csatlakozik a tartományhoz. (Az alapértelmezett tűzfalszabályok lehetővé teszik a felügyeletet a helyi alhálózatból és a Active Directory tartományi szolgáltatásokban található bármely alhálózatból.)

Miután az operációs rendszer igény szerint konfigurálta az Sconfig-t az egyes kiszolgálókon, készen áll a Windows felügyeleti központban a fürt létrehozása varázsló használatára a kiszolgálók fürtözéséhez.

## <a name="next-steps"></a>További lépések

A cikkel kapcsolatos következő felügyeleti feladat végrehajtásához lásd:
> [!div class="nextstepaction"]
> [Azure Stack HCI-fürt létrehozása](../deploy/create-cluster.md)
