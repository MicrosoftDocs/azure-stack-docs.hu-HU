---
title: Azure Stack HCI-alapú virtuális gépek védelmi Azure Site Recovery használatával
description: A Windows felügyeleti központ használatával Azure Stack HCI-alapú virtuális gépeket Azure Site Recovery-mel védhető.
ms.topic: article
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 01b6f16b3812b5f11f95d9d11f6563a1631fd690
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783934"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>Azure Stack HCI-alapú virtuális gépek védelmi Azure Site Recovery használatával

>A következőkre vonatkozik: Windows Server 2019

A Windows felügyeleti központ leegyszerűsíti a virtuális gépek (VM-EK) replikálásának folyamatát a kiszolgálókon vagy fürtökön, így lehetővé teszi, hogy kihasználhassa az Azure-t a saját adatközpontjában. A telepítő automatizálásához kapcsolja össze a Windows felügyeleti központot az Azure-ban.

Ez a cikk bemutatja, hogyan használhatók a Azure Site Recovery a replikációs beállítások konfigurálásához és helyreállítási terv létrehozásához a Azure Portal. Ezeknek a feladatoknak a végrehajtásával a Windows felügyeleti központ elindíthatja a VM-replikációt a virtuális gépek számára. A Azure Site Recovery a fizikai kiszolgálók és fürtcsomópontok védelmét is biztosítja.

További információ: [a Windows Server csatlakoztatása az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Hogyan működik a Azure Site Recovery a Windows felügyeleti központtal?
*Azure site Recovery* egy Azure-szolgáltatás, amely a virtuális gépeken futó számítási feladatokat replikálja, így az üzleti szempontból kritikus fontosságú infrastruktúrája a katasztrófától védve van. További információ: [About site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

A Azure Site Recovery két összetevőből áll: a *replikációból* és a *feladatátvételből*. A replikálási rész megvédi a virtuális gépeket a katasztrófától a cél virtuális gép VHD-fájljának Azure Storage-fiókba való replikálásával. A virtuális gépeket később átadhatja, és vészhelyzet esetén futtathatja őket az Azure-ban. A feladatátvételi tesztet anélkül is elvégezheti, hogy az elsődleges virtuális gépeken tesztelje az Azure-ban a helyreállítási folyamatot.

A replikációs összetevő telepítésének befejezése önmagában elegendő a virtuális gépek vészhelyzetek elleni védeleméhez. A virtuális gépek azonban nem indíthatók el az Azure-ban, amíg nem konfigurálja a folyamat feladatátvételi részét.

Beállíthatja a feladatátvételi részt, ha feladatátvételt szeretne végezni egy Azure-beli virtuális gépen; a kezdeti beállítás során nem szükséges. Ha a gazdagép-kiszolgáló leáll, beállíthatja a feladatátvételi összetevőt, és elérheti a védett virtuális gép munkaterheléseit. Javasoljuk azonban, hogy a feladatátvételsel kapcsolatos beállításokat a katasztrófa előtt konfigurálja.

## <a name="prerequisites-and-planning"></a>Előfeltételek és tervezés
A cikk lépéseinek végrehajtásához a következők szükségesek:

- A védelemmel ellátni kívánt virtuális gépeket üzemeltető célkiszolgálón internetkapcsolattal kell rendelkeznie az Azure-ba való replikáláshoz.
- A Windows felügyeleti központból az Azure-ba való kapcsolódás. További információ: az [Azure-integráció konfigurálása](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Tekintse át a kapacitás-tervezési eszközt a sikeres replikálás és feladatátvétel követelményeinek kiértékeléséhez. További információ: [Tudnivalók a Hyper-V vész-helyreállítás az Azure-ba való Azure site Recovery Deployment Planneráról](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity).

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>1. lépés: virtuális gépek védelmének beállítása a célállomáson
A következő lépéseket egyszer kell végrehajtania egy gazdagép-kiszolgálón vagy-fürtön, amely tartalmazza a védelem céljára szánt virtuális gépeket:
1. A **minden kapcsolat** lapon a Windows felügyeleti központban kapcsolódjon a védelemmel ellátni kívánt virtuális gépeket üzemeltető kiszolgálóhoz vagy fürthöz.
1. Az **eszközök**területen válassza a **virtuális gépek**lehetőséget, majd válassza a **leltár** lapot.
1. Válasszon ki egy virtuális gépet (nem kell a védelemmel ellátni kívánt virtuális gépnek lennie).
1. Bontsa ki a **további** almenü elemet, majd válassza a **virtuális gép védelmének beállítása**lehetőséget.

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="A virtuális gép védelmi almenüjének beállítása beállítás a Windows felügyeleti központban.":::

1. Jelentkezzen be az Azure-fiókjába.
1. A gazdagép és Azure Site Recovery beállítása lapon adja meg a szükséges adatokat, majd válassza a **beállítás**lehetőséget:

   - **Előfizetés:** A gazdagépen a virtuális gépek replikálásához használni kívánt Azure-előfizetés.
   - **Erőforráscsoport:** Egy új erőforráscsoport-név.
   - **Recovery Services tároló:** A gazdagépen található védett virtuális gépek Azure Site Recovery tárolójának neve.  
   - **Hely:** Az az Azure-régió, ahol a Azure Site Recovery erőforrásokat létre kell hozni.

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="A gazdagép beállítása Azure Site Recovery lapra a Windows felügyeleti központban.":::

1. Várjon, amíg megjelenik az értesítés: **site Recovery beállítás befejeződött**.
 
Ez a folyamat akár 10 percet is igénybe vehet. Az előrehaladást az **értesítések** (a Windows felügyeleti központ jobb felső sarkában található harang ikon) alatt tekintheti meg.

>[!NOTE]
> Ez a folyamat automatikusan telepíti a Azure Site Recovery ügynököt a célkiszolgálóra vagy a csomópontokra (ha fürtöt konfigurál), és létrehoz egy **erőforráscsoportot** a megadott Storage- **fiókkal** és **-** tárolóval a megadott **helyen**. Emellett regisztrálja a cél gazdagépet a Azure Site Recovery szolgáltatással, és konfigurálja az alapértelmezett replikációs házirendet.

## <a name="step-2-select-vms-to-protect"></a>2. lépés: a védelemmel ellátni kívánt virtuális gépek kiválasztása
A virtuális gépek elleni védelem érdekében hajtsa végre a következő lépéseket:
1. A Windows felügyeleti központban térjen vissza az előző feladatban konfigurált kiszolgálóhoz vagy fürthöz.
1. Az **eszközök**területen válassza a **virtuális gépek**lehetőséget, majd válassza a **leltár** lapot.
1. Válassza ki a védelemmel ellátni kívánt virtuális gépet, bontsa ki a **további** almenü elemet, majd válassza a **védelem virtuális gép**lehetőséget.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="A virtuális gép védelemének beállítása beállítás a Windows felügyeleti központban.":::

1. Tekintse át a virtuális gép védelmének kapacitására vonatkozó követelményeket. További információ: [a kapacitás megtervezése a Hyper-V virtuális gép vész-helyreállításához](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner).

    Ha prémium szintű Storage-fiókot szeretne használni, hozzon létre egyet a Azure Portal. További információ: az [Azure-ban elérhető lemezek](https://docs.microsoft.com/azure/storage/common/storage-premium-storage) **prémium SSD** szakasza? A Windows felügyeleti központban elérhető **Create New** lehetőség létrehoz egy standard szintű Storage-fiókot.

1. Adja meg a virtuális gép replikálásához használni kívánt **Storage-fiók** nevét, majd válassza a virtuális gép **védetté** tétele lehetőséget a virtuális gép replikálásának engedélyezéséhez.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="A Azure Site Recovery Storage-fiókjának meghatározása a virtuális gépek Windows felügyeleti központban való védelemmel való ellátásához.":::

    Azure Site Recovery elindítja a replikálási folyamatot. A virtuális gép védett, ha a **virtuálisgép-leltári** rács **védett** oszlopában lévő érték **Igen**értékűre változik. Ez a folyamat több percig is eltarthat.  

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>3. lépés: feladatátvételi teszt konfigurálása és futtatása a Azure Portalban
A virtuális gép replikációjának elindítása előtt nem szükséges végrehajtani ezt a lépést. A virtuális gép csak replikációval védett. A Azure Site Recovery beállításakor azonban ajánlott a feladatátvételi beállítások konfigurálása.
 
Az alábbi lépések végrehajtásával készítse elő a feladatátvételt egy Azure-beli virtuális gépre:
1. Állítson be egy Azure-hálózatot, amelyet a feladatátvételt használó virtuális gép csatlakoztatni fog ehhez a VNET. További információ: a helyszíni [Hyper-V virtuális gépek vész-helyreállításának beállítása az Azure-](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure)ba.

    >[!NOTE]
    > A Windows felügyeleti központ automatikusan végrehajtja az erőforrás lépéseit. Csak az Azure-hálózatot kell beállítania.

1. Futtasson feladatátvételi tesztet. További információt a vész- [helyreállítási gyakorlat futtatása az Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover)-ban című témakörben talál.

## <a name="step-4-create-recovery-plans"></a>4. lépés: helyreállítási tervek létrehozása
A Azure Site Recovery helyreállítási tervei lehetővé teszik, hogy a teljes alkalmazás virtuális gépek gyűjteményét átadja és helyreállítsa. A védett virtuális gépeket egyenként lehet helyreállítani. Azonban jobb módszer, ha egy alkalmazás virtuális gépei hozzáadását a helyreállítási tervhez adja. Ezt követően a helyreállítási terv segítségével elvégezheti a teljes alkalmazás feladatátvételét. Az alkalmazás helyreállításának teszteléséhez egy helyreállítási terv feladatátvételi teszt szolgáltatását is használhatja.

A helyreállítási tervek lehetővé teszik a virtuális gépek csoportosítását, a feladatátvétel során elinduló sorrend sorrendjét, valamint a további helyreállítási lépések automatizálását. A virtuális gépek védelme után megtekintheti a Azure Portal Azure Site Recovery-tárolóját a helyreállítási tervek létrehozásához. További információ: [helyreállítási tervek létrehozása és testreszabása](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).

## <a name="step-5-monitor-replicated-vms-in-azure"></a>5. lépés: replikált virtuális gépek figyelése az Azure-ban
Ha nem szeretne hibát ellenőrizni a kiszolgáló regisztrációs folyamatában, lépjen a **Azure Portal**, válassza a **minden erőforrás**lehetőséget, válassza a **Recovery Services**tároló elemet, válassza a **feladatok**, majd a **site Recovery feladatok**lehetőséget. A **Recovery Services** tár neve a cikk első feladatának 6. lépésében megadott név.

A virtuális gépek replikálásának figyeléséhez nyissa meg a **Recovery Services**-tárolót, majd **replikálja az elemeket**.

A tárolóban regisztrált összes kiszolgáló megjelenítéséhez nyissa meg **Recovery Services**-tárolót, **site Recovery-infrastruktúrát**, majd a **Hyper-v-gazdagépeket** (a Hyper-v-helyek szakaszban).

## <a name="known-issue"></a>Ismert probléma ##
Ha fürttel regisztrálja Azure Site Recoveryt, vagy ha valamelyik csomópont nem telepíti Azure Site Recovery vagy regisztrálja a Azure Site Recovery szolgáltatást, előfordulhat, hogy a virtuális gépek nem lesznek védve. Annak ellenőrzéséhez, hogy a fürt összes csomópontja regisztrálva van-e a Azure Portalban, nyissa meg a **Recovery Services**-tárolót, a **feladatokat**, majd **site Recovery a feladatokat**.

## <a name="next-steps"></a>További lépések
További információ a Azure Site Recoveryról:

- [Általános kérdések a Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
