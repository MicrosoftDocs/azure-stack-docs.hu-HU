---
title: A Azure Stack hub és a globális Azure összehasonlítása
description: Ismerje meg, hogyan biztosítja a Microsoft az Azure-t és a Azure Stack hub szolgáltatásokat egy Azure-ökoszisztémában
author: justinha
ms.topic: overview
ms.date: 05/03/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 3ddcedc92265147cd4e87ebabf2ad48986b9ad8d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880026"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>A globális Azure, a Azure Stack hub és a Azure Stack HCI közötti különbségek

A Microsoft egy Azure-ökoszisztémában biztosítja az Azure-t és a Azure Stack hub szolgáltatásokat. Ugyanazzal az alkalmazási modellel, önkiszolgáló portálokkal és API-kkal használhatja a felhőalapú funkciókat, hogy az Ön vállalata globális Azure-vagy helyszíni erőforrásokat használ-e Azure Resource Manager.

Ez a cikk a globális Azure-, Azure Stack hub-és Azure Stack HCI-képességeket ismerteti, és általános forgatókönyvekre vonatkozó ajánlásokat nyújt, amelyekkel a szervezete számára biztosítható a legjobb választás a Microsoft felhőalapú szolgáltatásainak megvalósításához.

![Az Azure ökoszisztéma áttekintése](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globális Azure

A Microsoft Azure felhőszolgáltatások állandóan bővülő gyűjteménye, amely segítséget nyújt cégének üzleti céljai elérésében. A kedvenc eszközeivel és keretrendszerével szabadon hozhat létre, kezelhet és helyezhet üzembe alkalmazásokat egy hatalmas, globális hálózaton.

A globális Azure több mint 100 szolgáltatást kínál a világszerte 54 régiókban. A globális Azure-szolgáltatások legfrissebb listáját a [*régiók által elérhető termékek*](https://azure.microsoft.com/regions/services)között tekintheti meg. Az Azure-ban elérhető szolgáltatások kategóriák szerint vannak felsorolva, valamint az is, hogy általánosan elérhetők vagy előzetes verzióban érhetők el.

A globális Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [Ismerkedés az Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)-ban.

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack hub az Azure kiterjesztése, amely a felhő-számítástechnika rugalmasságát és innovációját teszi lehetővé a helyszíni környezetben. A helyszínen üzembe helyezett Azure Stack hub lehetővé teszi az Azure konzisztens szolgáltatások biztosítását az internethez (és az Azure-hoz), vagy az internetkapcsolat nélküli, leválasztott környezetekben. Azure Stack hub ugyanazokat az alapul szolgáló technológiákat használja, mint a globális Azure, amely magában foglalja az infrastruktúra-szolgáltatás (IaaS), a szolgáltatott szoftver (SaaS) és az opcionális platform-szolgáltatás (

- Azure-beli virtuális gépek Windows és Linux rendszerekhez
- Azure-Web Apps és-függvények
- Azure Key Vault
- Azure Resource Manager
- Azure Piactér
- Tárolók
- Felügyeleti eszközök (csomagok, ajánlatok, RBAC stb.)

Az Azure Stack hub Péter képességei nem kötelezőek, mivel Azure Stack hub nem a Microsoft által üzemeltetett, hanem ügyfeleink által működtetett. Ez azt jelenti, hogy bármely olyan Pásti-szolgáltatást kínálhat, amelyet a végfelhasználók számára kíván használni, ha készen áll arra, hogy elkészítse az alapul szolgáló infrastruktúrát és folyamatokat a végfelhasználótól. Az Azure Stack hub azonban több opcionális, például App Service, SQL-adatbázist és MySQL-adatbázist is tartalmaz. Ezek erőforrás-szolgáltatóként érkeznek, így a több-bérlős használatra kész, a standard Azure Stack hub-frissítésekkel együtt frissülnek, és láthatók a Azure Stack hub portálon, és jól integráltak Azure Stack hub-ban.

A fent ismertetett erőforrás-szolgáltatók mellett további, a IaaS-ben futó, [sablonon alapuló megoldások Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) , de Ön Azure stack hub-kezelőként is elérhetővé válik a felhasználók számára, beleértve a következőket:

- Service Fabric
- Kubernetes-tároló szolgáltatás
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Példa Azure Stack hub használati eseteire:

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

[Azure stack HCI](azure-stack-hci-overview.md) -megoldások lehetővé teszik a helyszíni virtuális gépek futtatását, és az Azure-hoz való egyszerű hiperkonvergens-infrastruktúra (HCI) megoldással való kapcsolódást. Egységes Azure-szolgáltatásokkal hozhat létre és futtathat felhőalapú alkalmazásokat a helyszínen a jogszabályi és műszaki előírásoknak való megfelelőség mellett. A helyszíni virtualizált alkalmazások futtatása mellett Azure Stack HCI lehetővé teszi az öregedő kiszolgáló-infrastruktúra cseréjét és összevonását, valamint az Azure for Cloud Serviceshez való kapcsolódást a Windows felügyeleti központtal.

Azure Stack HCI a Hyper-V és a Közvetlen tárolóhelyek által vezérelt, ellenőrzött HCI-megoldásokat biztosít a Windows Server 2019 szoftveresen definiált adatközpont (SDDC) használatával. A Windows felügyeleti központ a következő Azure-szolgáltatásokhoz való felügyelethez és integrált hozzáférésekhez használható:

- Azure Backup
- Azure Site Recovery
- Azure Monitor és frissítés

Az Azure-szolgáltatások frissített listájához, amelyhez Azure Stack HCI-t csatlakoztathatja, lásd: a [Windows Server csatlakoztatása az Azure Hybrid Serviceshez](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Példa a Azure Stack HCI használati eseteire
- Távoli vagy fiókirodai rendszerek
- Adatközpont-konszolidáció
- Virtuális asztali infrastruktúra
- Üzleti szempontból kritikus fontosságú infrastruktúra
- Alacsonyabb árú tárolás
- Magas rendelkezésre állás és vész-helyreállítás a felhőben
- Vállalati alkalmazások, például SQL Server

Látogasson el a [Azure stack HCI webhelyére](https://azure.microsoft.com/overview/azure-stack/hci/) , ahol megtekintheti a Microsoft partnerei által jelenleg elérhető 70 és Azure stack HCI-megoldásokat.

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub adminisztrációs alapjai](azure-stack-manage-basics.md)

[Gyors útmutató: az Azure Stack hub felügyeleti portáljának használata](azure-stack-manage-portals.md)
