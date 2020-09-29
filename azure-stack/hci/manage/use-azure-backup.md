---
title: Windows-kiszolgálók biztonsági mentése Azure Backup használatával
description: Ez a cikk útmutatást nyújt arról, hogyan használható Azure Backup a Windows felügyeleti központban a Windows-kiszolgálók biztonsági mentéséhez.
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947503"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>Windows-kiszolgálók biztonsági mentése Azure Backup használatával

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A Windows felügyeleti központ egyszerűsíti a Windows-kiszolgálók biztonsági mentését Microsoft Azurera. A folyamat emellett védelmet nyújt a véletlen vagy rosszindulatú törléstől, a sérüléstől és akár a ransomware is. A telepítő automatizálásához a Windows felügyeleti központot az Azure-hoz kapcsolja a Azure Backup szolgáltatás használatához.

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Backupt, és hogyan hozhat létre biztonsági mentési szabályzatot a kiszolgálói kötetek és a Windows rendszerállapot számára a Windows felügyeleti központból. Az útmutató célja a virtuális gépeken futó munkaterhelések biztonsági mentése az Azure Stack HCI operációs rendszeren az Azure-ba.

További információ az Azure-integrációról a Windows felügyeleti központtal: a [Windows Server csatlakoztatása az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Hogyan működik a Azure Backup a Windows felügyeleti központtal?
**Azure Backup** egy szolgáltatás, amellyel biztonsági mentést készíthet, védetté teheti és visszaállíthatja adatait az Azure-ban. A Azure Backup a meglévő helyszíni vagy helyszíni biztonsági mentési megoldásokat egy megbízható, biztonságos és költséghatékony felhőalapú megoldással helyettesíti.

További információ: [Mi a Azure Backup szolgáltatás?](/azure/backup/backup-overview)

Az Azure Backup több összetevőjét letöltheti és telepítheti a megfelelő számítógépre, kiszolgálóra vagy a felhőbe. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Az összes Azure Backup-összetevő képes biztonsági másolatot készíteni az adatokról egy Recovery Services-tárolóba az Azure-ban, függetlenül attól, hogy a helyszínen vagy az Azure-ban védi az adatok védelmét.

Azure Backup a Windows felügyeleti központtal való integráció ideális megoldás a kötetek és a Windows rendszerállapotának biztonsági mentésére a helyi Windows-kiszolgálókon vagy virtuális kiszolgálókon. Az átfogó folyamat a fájlkiszolgálók, a tartományvezérlők és az IIS-webkiszolgálók biztonsági mentését is támogatja.

A Azure Backup a **biztonsági mentési** eszközön keresztül érheti el a Windows felügyeleti központban. A **biztonságimásolat** -készítő eszköz beállítása, kezelése és figyelési képességei lehetővé teszik a kiszolgálók biztonsági mentését, a biztonsági mentési és visszaállítási műveletek elvégzését, valamint a Windows-kiszolgálók általános biztonsági mentési állapotának figyelését.

## <a name="prerequisites"></a>Előfeltételek
A Azure Backup használatához a következő előfeltételek szükségesek:
- Legalább egy aktív előfizetéssel rendelkező Azure-fiók
- Internet-hozzáférés az Azure-hoz a cél Windows-kiszolgálókhoz
- A Windows felügyeleti központ átjárója és az Azure közötti kapcsolatok

    További tudnivalókért tekintse meg az [Azure-integráció konfigurálását](/windows-server/manage/windows-admin-center/azure/azure-integration)ismertető témakört.

## <a name="getting-started-with-azure-backup"></a>A Azure Backup első lépései
Amikor először kiválasztja a **biztonsági mentési** eszközt a Windows felügyeleti központban az Azure-hoz való kiszolgálói kapcsolat létesítéséhez, megjelenik az **Üdvözöljük a Azure Backup** oldalon. A Azure Backup telepítővarázslójának elindításához válassza a **Azure Backup beállítása** elemet. A következő részek részletesen ismertetik a varázsló lépéseit.

Ha Azure Backup már konfigurálva van egy kiszolgálói kapcsolatban, a **biztonsági mentési** eszköz megnyitásával megnyílik a **biztonsági mentési irányítópult**. Az irányítópultról elvégezhető műveletekkel és feladatokkal kapcsolatos információkért tekintse meg a [kezelési és figyelési](#management-and-monitoring) szakaszt.

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>1. lépés: bejelentkezés a Microsoft Azure Portalba
Jelentkezzen be Azure-fiókjába.

> [!NOTE]
> Ha már csatlakoztatta a Windows felügyeleti központ átjáróját az Azure-hoz, automatikusan be kell jelentkeznie a portálra. Válassza a **kijelentkezés** lehetőséget, majd jelentkezzen be másik felhasználóként.

### <a name="step-2-set-up-azure-backup"></a>2. lépés: a Azure Backup beállítása
Válassza ki a Azure Backup következő beállításait:
- **Előfizetés azonosítója:** A Windows Server Azure-ba történő biztonsági mentéséhez használni kívánt Azure-előfizetés. A rendszer az összes Azure-erőforrást, például az Azure-erőforráscsoportot és a Recovery Services-tárolót hozza létre és társítja a kiválasztott előfizetéshez.
- Tároló **:** A Recovery Services tároló az a hely, ahol a kiszolgálók biztonsági másolatait tárolni fogja. Egy meglévő tárolót vagy Windows felügyeleti központot is használhat, amely létrehoz egy újat.  
- **Erőforráscsoport:** Az Azure-erőforráscsoport egy erőforrások gyűjteményének tárolója. A Recovery Services-tárolót a rendszer létrehozta, vagy a megadott erőforráscsoport tárolja. Egy meglévő erőforráscsoportot vagy egy Windows felügyeleti központot is használhat, amely létrehoz egy újat.
- **Hely:** Az az Azure-régió, ahol a Recovery Services-tároló létre lesz hozva. Azt javasoljuk, hogy válassza ki a Windows Serverhez legközelebb eső Azure-régiót, amelyről biztonsági másolatot készít.

### <a name="step-3-select-backup-items-and-schedule"></a>3. lépés: biztonsági mentési elemek és ütemterv kiválasztása
1. Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni a kiszolgálóról. A Windows felügyeleti központ lehetővé teszi a kötetek és a Windows rendszerállapotok kombinációjának kiválasztását, valamint a biztonsági mentésre kijelölt adatmennyiség becsült méretét.

    > [!NOTE]
    > Az első biztonsági mentés az összes kijelölt adat teljes biztonsági mentése. A későbbi biztonsági másolatok növekményes, és csak az előző biztonsági mentés óta bekövetkezett adatok módosításait továbbítják.

1. Válassza ki a Windows rendszerállapotának és-köteteinek több előre beállított **biztonsági mentési ütemtervét** .

### <a name="step-4-enter-an-encryption-passphrase"></a>4. lépés: titkosítási jelszó megadása
1. Írjon be egy legalább 16 karakterből álló **titkosítási jelszót** . Azure Backup a biztonsági mentési adatait egy Ön által létrehozott és kezelt titkosítási jelszóval védi. Az adatok Azure Backupból való helyreállításához a titkosítási jelszó szükséges.

    > [!NOTE]
    > Tárolja a jelszót biztonságos külső helyen, például egy másik kiszolgálón vagy a [Azure Key Vault](/azure/key-vault/quick-create-portal). A Microsoft nem tárolja a jelszót, és nem tudja lekérni vagy alaphelyzetbe állítani a jelszót, ha az elveszett vagy elfelejtett.

1. Tekintse át a varázsló lapon található összes beállítást, majd kattintson az **alkalmaz**gombra.

A Windows felügyeleti központ a következő műveleteket végzi el:
1. Létrehoz egy Azure-erőforráscsoportot, ha még nem létezik ilyen.
1. Egy Azure Recovery Services-tárolót hoz létre a megadott módon.
1. Telepíti és regisztrálja a Microsoft Azure Recovery Services ügynököt a tárolóban.
1. A biztonsági mentési és adatmegőrzési ütemtervet hozza létre a Windows Serverhez társított kiválasztott beállításoknak megfelelően.

## <a name="management-and-monitoring"></a>Kezelés és monitorozás
A Azure Backup sikeres beállítása után megjelenik a **biztonsági mentési irányítópult** , amikor megnyit egy meglévő kiszolgálói kapcsolatok **biztonsági mentési** eszközét. Az irányítópultról a következő feladatokat végezheti el:
- **Hozzáférés a tárolóhoz az Azure-ban:** Az **Áttekintés** lapon válassza ki  **Recovery Services** tárolót a tár eléréséhez, ahol számos felügyeleti műveletet végezhet. További információ: Recovery Services-tárolók [figyelése és kezelése](/azure/backup/backup-azure-manage-windows-server).
- **Ad hoc biztonsági mentés:** Válassza a **biztonsági mentés** lehetőséget, ha ad hoc biztonsági mentést szeretne készíteni. 
- **Feladatok figyelése és riasztási értesítések konfigurálása:** Navigáljon a **feladatok** lapra a folyamatban lévő vagy a múltbeli feladatok figyeléséhez, valamint a [Riasztási értesítések konfigurálásához](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts) a sikertelen feladatok és egyéb biztonsági mentési riasztások fogadásához.
- **Helyreállítási pontok megtekintése és adatok helyreállítása:** Válassza a **helyreállítási pontok** lapot a helyreállítási pontok megtekintéséhez, majd válassza az **adatok helyreállítása** lehetőséget az Azure-ból származó adatok helyreállításának lépéseihez.

## <a name="next-steps"></a>Következő lépések
További információért lásd még:
- [Azure Stack HCI-alapú virtuális gépek védelmi Azure Site Recovery használatával](./azure-site-recovery.md)
