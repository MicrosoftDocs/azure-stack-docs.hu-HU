---
title: Virtuális asztali infrastruktúra (VDI) üzembe helyezése Azure Stack HCI-ben
description: Ez a témakör útmutatást nyújt a virtuális asztali infrastruktúra (VDI) megtervezéséhez, konfigurálásához és üzembe helyezéséhez a Azure Stack HCI operációs rendszeren.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571550"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Virtuális asztali infrastruktúra (VDI) üzembe helyezése Azure Stack HCI-ben

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör útmutatást nyújt a virtuális asztali infrastruktúra (VDI) megtervezéséhez, konfigurálásához és üzembe helyezéséhez a Azure Stack HCI operációs rendszeren. A szervezet felhasználói számára központosított, magasan elérhető, egyszerűsített és biztonságos felügyeletet biztosíthat a Azure Stack HCI-befektetések számára. Ezzel az útmutatóval olyan forgatókönyveket engedélyezhet, mint a saját eszközök használata (BYOD) a felhasználók számára, miközben konzisztens és megbízható felhasználói élményt biztosít az üzleti szempontból kritikus fontosságú alkalmazások számára a biztonság feláldozása nélkül.

## <a name="overview"></a>Áttekintés
A VDI kiszolgálói hardver használatával futtatja az asztali operációs rendszereket és a szoftvereket egy virtuális gépen (VM). A VDI így lehetővé teszi a hagyományos asztali számítási feladatok futtatását a központosított kiszolgálókon. Az üzleti beállításokban rejlő VDI-előnyök közé tartozik a bizalmas vállalati alkalmazások és adatok biztonságos adatközpontban való megőrzése, valamint a BYOD szabályzat betartása anélkül, hogy aggódni kellene a személyes adatoknak a vállalati eszközökkel való összekeverése nélkül. A VDI a távoli és a fiókirodában dolgozó szakemberek támogatására is lehetőséget nyújt, és hozzáférést biztosít a vállalkozók és a partnerek számára.

Azure Stack HCI a VDI optimális platformját kínálja. A Microsoft [Távoli asztali szolgáltatások (RDS)](/windows-server/remote/remote-desktop-services/welcome-to-rds) szolgáltatással együtt érvényesített Azure stack HCI megoldás lehetővé teszi, hogy a rendelkezésre álló és jól méretezhető architektúrát biztosítson.

Emellett Azure Stack HCI VDI a következő egyedi felhőalapú funkciókat biztosítja a VDI-munkaterhelések és-ügyfelek számára:
- Központilag felügyelt frissítések az Azure Update Management
- Egységes biztonsági felügyelet és komplex veszélyforrások elleni védelem VDI-ügyfelek számára

## <a name="deploy-vdi"></a>VDI üzembe helyezése
Ez a szakasz részletesen ismerteti, hogyan vásárolhat hardvert a VDI Azure Stack HCI-re való üzembe helyezéséhez, és hogyan használhatja a Windows felügyeleti központot a felügyelethez. Emellett magában foglalja az RDS telepítését is a VDI támogatásához.

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>1. lépés: hardver beszerzése a VDI-hez Azure Stack HCI-ben
Először be kell szereznie a hardvert. Ennek a legegyszerűbb módja, ha megkeresi a kívánt Microsoft-hardveres partnert a [Azure stack HCI-katalógusban](https://hcicatalog.azurewebsites.net) , és megvásárol egy integrált rendszert a Azure stack HCI operációs rendszerrel előre telepítve. A katalógusban szűrheti az ilyen típusú számítási feladatokhoz optimalizált szállítói hardvert. Ügyeljen arra, hogy a hardvereszközökkel kapcsolatban ellenőrizze, hogy a hardver képes-e támogatni a fürtön üzemeltetni kívánt virtuális asztalok számát.

Ellenkező esetben telepítenie kell a Azure Stack HCI operációs rendszert a saját hardverén. Az Azure Stack HCI telepítési lehetőségeivel és a Windows felügyeleti központ telepítésével kapcsolatos részletekért lásd: [a Azure stack HCI operációs rendszer](./operating-system.md)telepítése.

Ezután a Windows felügyeleti központban [hozzon létre egy Azure stack HCI-fürtöt](./create-cluster.md).

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>2. lépés: az Azure Update Management beállítása a Windows felügyeleti központban
A Windows felügyeleti központban állítsa be az [Azure Update Managementt](/windows-server/manage/windows-admin-center/azure/azure-update-management) az elérhető frissítések állapotának gyors felmérésére, a szükséges frissítések bevezetésére és a telepítési eredmények áttekintésére az alkalmazott frissítések ellenőrzéséhez.

Az Azure Update Management megkezdéséhez szüksége lesz egy előfizetésre a Microsoft Azurehoz. Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free).

A Windows felügyeleti központ használatával további Azure Hybrid Services-szolgáltatásokat is beállíthat, például biztonsági mentést, File Sync, Site Recovery, pont – hely VPN-t és Azure Security Centert.

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>3. lépés: Távoli asztali szolgáltatások (RDS) üzembe helyezése a VDI-támogatáshoz
Miután elvégezte a Azure Stack HCI üzembe helyezését és az Azure-ban való regisztrálást Update Management használatára, készen áll arra, hogy az ebben a szakaszban található útmutatással az RDS-t a VDI támogatásához felépítse és telepítse.

Az RDS létrehozása és üzembe helyezése:
1. [A távoli asztali környezet üzembe helyezése](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. [RDS-munkamenet-gyűjtemény létrehozása](/windows-server/remote/remote-desktop-services/rds-create-collection) alkalmazások és erőforrások megosztásához
1. [Az RDS-telepítés licence](/windows-server/remote/remote-desktop-services/rds-client-access-license)
1. Az alkalmazások és erőforrások eléréséhez [Távoli asztal-ügyfél](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) telepítésének elvégzése a felhasználóktól
1. A magas rendelkezésre állás érdekében vegyen fel kapcsolati közvetítőket és munkamenet-állomásokat:
    - [Meglévő RDS-gyűjtemény felskálázása egy távoli asztali munkamenetgazda-farmtal](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [Magas rendelkezésre állás hozzáadása a RD-kapcsolatszervező-infrastruktúrához](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [Magas rendelkezésre állás hozzáadása a távoli asztali webes és RD-átjáró webes oldalhoz](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [Két csomópontos Közvetlen tárolóhelyek fájlrendszer üzembe helyezése a UPD-tároláshoz](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>További lépések
A VDI-vel kapcsolatos további információkért lásd: [Távoli asztali szolgáltatások támogatott konfigurációi](/windows-server/remote/remote-desktop-services/rds-supported-config)
