---
title: A globális Azure, Azure Stack hub, Azure Stack HCI közötti különbségek
titleSuffix: Azure Stack Hub
description: Megismerheti a globális Azure, a Azure Stack hub és a Azure Stack HCI közötti különbségeket.
author: PatAltimore
ms.topic: overview
ms.date: 07/10/2020
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 4fc890225801fe35ec4ab72e84777aa56d597035
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868709"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>A globális Azure, az Azure Stack hub és az Azure Stack HCI közötti különbségek

A Microsoft egy Azure-ökoszisztémában biztosítja az Azure-t és a Azure Stack hub szolgáltatásokat. Ugyanazzal az alkalmazási modellel, önkiszolgáló portálokkal és API-kkal használhatja a felhőalapú funkciókat, hogy az Ön vállalata globális Azure-vagy helyszíni erőforrásokat használ-e Azure Resource Manager.

Ez a cikk a globális Azure, Azure Stack hub és a Azure Stack HCI képességek közötti különbségeket ismerteti. Általános forgatókönyvekre vonatkozó ajánlásokat tartalmaz, amelyek segítségével a legjobb választást nyújthatja a szervezete számára a Microsoft felhőalapú szolgáltatásainak biztosításához.

![Az Azure ökoszisztéma áttekintése](./media/compare-azure-azure-stack/azure-family-updated.png)

## <a name="global-azure"></a>Globális Azure

Microsoft Azure a Cloud Services egyre bővülő készlete, amely segít a szervezetnek az üzleti kihívásoknak való megfelelésben. A kedvenc eszközeivel és keretrendszerével szabadon hozhat létre, kezelhet és helyezhet üzembe alkalmazásokat egy hatalmas, globális hálózaton.

A globális Azure több mint 100 szolgáltatást kínál a világszerte 54 régiókban. A globális Azure-szolgáltatások legfrissebb listáját a [*régiók által elérhető termékek*](https://azure.microsoft.com/regions/services)között tekintheti meg. Az Azure-ban elérhető szolgáltatások kategória szerint vannak felsorolva, és azt is, hogy általánosan elérhetők vagy előzetes verzióban érhetők el.

A globális Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [Ismerkedés az Azure](/azure/?panel=get-started1&pivot=get-started)-ban.

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack hub az Azure kiterjesztése, amely a felhő-számítástechnika rugalmasságát és innovációját teszi lehetővé a helyszíni környezetben. A helyszínen üzembe helyezett Azure Stack hub lehetővé teszi az Azure konzisztens szolgáltatások biztosítását az internethez (és az Azure-hoz), vagy az internetkapcsolat nélküli, leválasztott környezetekben. A Azure Stack hub ugyanazokat az alapul szolgáló technológiákat használja, mint a globális Azure, amely magában foglalja az infrastruktúra-szolgáltatás (IaaS), a szolgáltatott szoftver (SaaS) és az opcionális platform-szolgáltatás (a szolgáltatás) funkcióinak alapvető összetevőit. Ilyen képességek:

- Azure-beli virtuális gépek Windows és Linux rendszerekhez
- Azure-Web Apps és-függvények
- Azure Key Vault
- Azure Resource Manager
- Azure Piactér
- Tárolók
- Felügyeleti eszközök (csomagok, ajánlatok, RBAC stb.)

Az Azure Stack hub Péter képességei nem kötelezőek, mert Azure Stack hub nem a Microsoft által üzemeltetett, hanem ügyfeleink által működtetett. Ez azt jelenti, hogy bármely olyan Pásti-szolgáltatást kínálhat, amelyet a végfelhasználók számára kíván elkészíteni, ha készen áll arra, hogy elvonta az alapul szolgáló infrastruktúrát és folyamatokat. Az Azure Stack hub azonban több opcionális, például App Service, SQL-adatbázist és MySQL-adatbázist is tartalmaz. Ezek erőforrás-szolgáltatóként érkeznek, így több-bérlős használatra készek, a standard szintű Azure Stack hub-frissítésekkel együtt, amelyek láthatók a Azure Stack hub portálon, és jól integrálva van az Azure Stack hub-val.

A fent ismertetett erőforrás-szolgáltatókon kívül további, a IaaS-ben futó, [sablonon alapuló megoldásokként](https://github.com/Azure/AzureStack-QuickStart-Templates) elérhető és tesztelt, Azure Resource Manager-alapú megoldásokkal is rendelkeznek. Azure Stack hub-kezelőként a következőket teheti a felhasználók számára, például:

- Service Fabric
- Kubernetes-tároló szolgáltatás
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Példa Azure Stack hub használati eseteire

- Pénzügyi modellezés
- Klinikai és jogcímek
- IoT-eszköz elemzése
- Kiskereskedelmi szortiment optimalizálása
- Ellátási lánc optimalizálása
- Ipari IoT
- Prediktív karbantartás
- Smart City
- Állampolgári szerepvállalás

További információ az Azure Stack hub-ról: [Mi az Azure stack hub](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI

A [Azure stack HCI](../hci/overview.md) olyan hiperkonvergens-fürt, amely érvényesített hardverrel futtatja a virtualizált Windows-és Linux-munkaterheléseket a helyszínen, és egyszerűen kapcsolódhat az Azure-hoz a felhőalapú biztonsági mentés, helyreállítás és monitorozás érdekében. Kezdetben a Windows Server 2019-alapú, Azure Stack HCI-t mostantól Azure-szolgáltatásként, előfizetéses licencelési modellel és beépített hibrid funkciókkal küldi el. Habár Azure Stack HCI a Windows Server rendszerrel megegyező központi operációsrendszer-összetevőkön alapul, ez egy teljesen új termékcsalád, amely a legjobb virtualizációs gazdagépre összpontosít.

Azure Stack HCI az optimális teljesítmény és megbízhatóság érdekében a Microsoft által érvényesített hardvert használ egy OEM-partnertől. A megoldások támogatják az olyan technológiák támogatását, mint például a NVMe-meghajtók, az állandó memória és a távoli közvetlen memória-hozzáférés (RDMA).

### <a name="example-use-cases-for-azure-stack-hci"></a>Példa a Azure Stack HCI használati eseteire

- Távoli vagy fiókirodai rendszerek
- Adatközpont-konszolidáció
- Virtuális asztali infrastruktúra
- Üzleti szempontból kritikus fontosságú infrastruktúra
- Alacsonyabb árú tárolás
- Magas rendelkezésre állás és vész-helyreállítás a felhőben
- Vállalati alkalmazások virtualizálása, például SQL Server

Látogasson el a [Azure stack HCI webhelyére](https://azure.microsoft.com/overview/azure-stack/hci/) , ahol megtekintheti a Microsoft partnerei által jelenleg elérhető 70 és Azure stack HCI-megoldásokat.

## <a name="next-steps"></a>További lépések

[Azure Stack hub adminisztrációs alapjai](azure-stack-manage-basics.md)

[Gyors útmutató: az Azure Stack hub felügyeleti portáljának használata](azure-stack-manage-portals.md)
