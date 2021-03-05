---
title: SQL Server üzembe helyezése Azure Stack HCI-ben (ak-HCI)
description: Ez a témakör útmutatást nyújt a SQL Server Azure Stack HCI-ben való üzembe helyezéséhez.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 433a9b8b7bf6f57cac936b24d7654b94b8e8c050
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186806"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>SQL Server üzembe helyezése Azure Stack HCI-ben

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; SQL Server (az összes támogatott verzió)

Ez a témakör útmutatást nyújt a SQL Servernak a Azure Stack HCI operációs rendszeren történő megtervezéséhez, konfigurálásához és üzembe helyezéséhez. Az operációs rendszer egy hiperkonvergens infrastruktúra-(HCI-) fürt megoldás, amely virtualizált Windows-és Linux-munkaterheléseket, valamint a hibrid helyszíni környezetben tárolt tárolókat üzemeltet.

## <a name="solution-overview"></a>Megoldás áttekintése
A Azure Stack HCI kiválóan elérhető, költséghatékony, rugalmas platformot biztosít SQL Server és Közvetlen tárolóhelyek futtatásához. A Azure Stack HCI online tranzakció-feldolgozási (OLTP) számítási feladatokat, adattárházat és BI-t, valamint mesterséges intelligenciát és elemzéseket is futtathat big dataeken keresztül.

A platform rugalmassága különösen fontos a kritikus fontosságú adatbázisok esetében. A Windows Servert vagy Linuxot használó virtuális gépeken SQL Server futtatható, amely lehetővé teszi több adatbázis számítási feladatának összevonását, és szükség esetén további virtuális gépek hozzáadását a Azure Stack HCI-környezethez. A Azure Stack HCI lehetővé teszi, hogy integrálja a SQL Servert a Azure Site Recoveryekkel, hogy felhőalapú áttelepítési, helyreállítási és védelmi megoldást biztosítson a szervezete megbízható és biztonságos adataihoz.

## <a name="deploy-sql-server"></a>SQL Server üzembe helyezése
Ez a szakasz részletesen ismerteti, hogyan sajátíthatja el a hardvert a SQL Server Azure Stack a HCI-ben, és a Windows felügyeleti központban kezelheti a kiszolgálók operációs rendszerét. A SQL Server, a monitorozás és a teljesítmény finomhangolására, valamint a magas rendelkezésre állás (HA) és az Azure Hybrid Services használatára vonatkozó információk is rendelkezésre állnak.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>1. lépés: hardver beszerzése az Azure Stack HCI-katalógusból
Először be kell szereznie a hardvert. Ennek a legegyszerűbb módja, ha megkeresi a kívánt Microsoft-hardveres partnert a [Azure stack HCI-katalógusban](https://hcicatalog.azurewebsites.net) , és megvásárol egy integrált rendszert a Azure stack HCI operációs rendszerrel előre telepítve. A katalógusban szűrheti az ilyen típusú számítási feladatokhoz optimalizált szállítói hardvert.

Ellenkező esetben telepítenie kell a Azure Stack HCI operációs rendszert a saját hardverén. Az Azure Stack HCI telepítési lehetőségeivel és a Windows felügyeleti központ telepítésével kapcsolatos részletekért lásd: [a Azure stack HCI operációs rendszer](./operating-system.md)telepítése.

Ezután a Windows felügyeleti központban [hozzon létre egy Azure stack HCI-fürtöt](./create-cluster.md).

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>2. lépés: a SQL Server telepítése Azure Stack HCI-re
A követelményektől függően a Windows Servert vagy a Linuxot futtató virtuális gépeken SQL Server is telepíthet.

A SQL Server telepítésére vonatkozó utasításokért lásd:
- [SQL Server telepítési útmutató a Windowshoz](/sql/database-engine/install-windows/install-sql-server?preserve-view=true&view=sql-server-ver15).
- [SQL Server on Linux telepítési útmutatója](/sql/linux/sql-server-linux-setup?preserve-view=true&view=sql-server-ver15).

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>3. lépés: a monitorozás és a teljesítmény finomhangolása SQL Server
A Microsoft átfogó eszközkészletet biztosít az események figyeléséhez SQL Server és a fizikai adatbázis kialakításának finomhangolásához. Az eszköz választása a végrehajtani kívánt figyelési vagy hangolási típustól függ.

A SQL Server-példányok teljesítményének és állapotának a Azure Stack HCI-ben való ellátásához tekintse meg a [Teljesítményfigyelés és-hangolás eszközeivel](/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?preserve-view=true&view=sql-server-ver15)foglalkozó témakört.

A 2017-es és a SQL Server 2016-es SQL Server hangoláshoz lásd: [SQL Server 2017 és 2016 ajánlott frissítései és konfigurációs lehetőségei nagy teljesítményű munkaterhelésekkel](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server).

### <a name="step-4-use-sql-server-high-availability-features"></a>4. lépés: a SQL Server magas rendelkezésre állású funkcióinak használata
Azure Stack HCI a [Windows Server feladatátvételi fürtszolgáltatást a SQL Server](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC) szolgáltatással használja a virtuális gépeken futó SQL Server hardveres meghibásodás esetén történő támogatásához. A SQL Server [Always On rendelkezésre állási csoportokat](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (AG-ket) is biztosít, amelyek az alkalmazás-és szoftver-meghibásodások segítésére szolgáló adatbázis-szintű magas rendelkezésre állást biztosítanak. A WSFC és az AG mellett Azure Stack HCI az [Always On feladatátvételi fürtszolgáltatást](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server) is használhatja, amely [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) technológián alapul a megosztott tároláshoz.

Ezek a lehetőségek mind a Microsoft Azure [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness) a kvórum vezérlésére használhatók. Az Always On rendelkezésre állási csoportok konfigurálásakor azt javasoljuk, hogy a különböző fizikai csomópontokra helyezett virtuális gépek esetében a fürt WSFC [-szabályait](/windows-server/failover-clustering/cluster-affinity) a fürtök közötti kapcsolati szabályok fenntartásával SQL Server.

### <a name="step-5-set-up-azure-hybrid-services"></a>5. lépés: az Azure Hybrid Services beállítása
Több Azure Hybrid-szolgáltatás is használható, amelyek segítségével megőrizheti SQL Server adatait és alkalmazásait. [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) a vész-helyreállítási szolgáltatás (DRaaS). Ha további információt szeretne arról, hogy a szolgáltatás hogyan gondoskodik az alkalmazások SQL Serverának védelméről a munkaterhelések online megőrzéséhez, olvassa el a vész- [helyreállítás beállítása SQL Serverhoz](/azure/site-recovery/site-recovery-sql)című témakört.

[Azure Backup](https://azure.microsoft.com/services/backup/) lehetővé teszi biztonsági mentési szabályzatok megadását a vállalati munkaterhelések megóvása érdekében, és támogatja a SQL Server konzisztencia biztonsági mentését és visszaállítását. További információ a helyszíni SQL-adatok biztonsági mentéséről: [Install Azure Backup Server](/azure/backup/backup-azure-microsoft-azure-backup).

Azt is megteheti, hogy a SQL Server [SQL Server felügyelt biztonsági mentési](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?preserve-view=true&view=sql-server-ver15) funkciójával felügyeli az Azure Blob Storage biztonsági másolatait.

A helyszíni archiválásra alkalmas lehetőség használatáról további információt a következő témakörben talál: 

- [Oktatóanyag: Azure Blob Storage-szolgáltatás használata az SQL Server 2016-tal](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?preserve-view=true&view=sql-server-ver15)
- [Gyors útmutató: az SQL biztonsági mentése és visszaállítása az Azure Blob Storage szolgáltatásba](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?preserve-view=true&tabs=SSMS&view=sql-server-ver15)

Ezen biztonsági mentési forgatókönyvek mellett más adatbázis-szolgáltatásokat is beállíthat, amelyek SQL Server ajánlatokat, beleértve [az integrációs szolgáltatások (SSIS) Azure Data Factory és az Azure Feature Pack csomagot](/sql/integration-services/azure-feature-pack-for-integration-services-ssis?preserve-view=true&view=sql-server-ver15). [](/azure/machine-learning/team-data-science-process/move-sql-azure-adf)

## <a name="next-steps"></a>Következő lépések
A SQL Server használatáról további információt a következő témakörben talál:
- [Oktatóanyag: Első lépések a adatbázismotor](/sql/relational-databases/tutorial-getting-started-with-the-database-engine?preserve-view=true&view=sql-server-ver15)