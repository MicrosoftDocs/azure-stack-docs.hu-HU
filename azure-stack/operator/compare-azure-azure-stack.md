---
title: A Azure Stack és a globális Azure összehasonlítása | Microsoft Docs
description: Ismerje meg, hogy a Microsoft Hogyan biztosítja az Azure-t és a Azure Stack termékcsaládot egy Azure-ökoszisztémában
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/03/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a847e842dff8b8b2b3032ded2466402897a3ae4
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995603"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>A globális Azure, a Azure Stack és az Azure Stack HCI közötti különbségek

A Microsoft egy Azure-ökoszisztémában biztosítja az Azure-t és a Azure Stack termékcsaládot. Ugyanazzal az alkalmazási modellel, önkiszolgáló portálokkal és API-kkal használhatja a felhőalapú funkciókat, hogy az Ön vállalata globális Azure-vagy helyszíni erőforrásokat használ-e Azure Resource Manager.

Ez a cikk bemutatja a globális Azure-, Azure Stack-és Azure Stack HCI-képességeket, és általános forgatókönyvekkel kapcsolatos ajánlásokat tartalmaz, amelyekkel a szervezete számára biztosíthatja a Microsoft felhőalapú szolgáltatásainak megvalósítását.

![Az Azure ökoszisztéma áttekintése](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globális Azure

A Microsoft Azure felhőszolgáltatások állandóan bővülő gyűjteménye, amely segítséget nyújt cégének üzleti céljai elérésében. A kedvenc eszközeivel és keretrendszerével szabadon hozhat létre, kezelhet és helyezhet üzembe alkalmazásokat egy hatalmas, globális hálózaton.

A globális Azure több mint 100 szolgáltatást kínál a világszerte 54 régiókban. A globális Azure-szolgáltatások legfrissebb listáját a [*régiók által elérhető termékek*](https://azure.microsoft.com/regions/services)között tekintheti meg. Az Azure-ban elérhető szolgáltatások kategóriák szerint vannak felsorolva, valamint az is, hogy általánosan elérhetők vagy előzetes verzióban érhetők el.

A globális Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [Ismerkedés az Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)-ban.

## <a name="azure-stack"></a>Azure Stack

Azure Stack az Azure kiterjesztése, amely a felhő-számítástechnika rugalmasságát és innovációját teszi lehetővé a helyszíni környezetben. A helyszínen üzembe helyezett Azure Stack használatával biztosíthatja az Azure konzisztens szolgáltatásait az internethez (és az Azure-hoz), illetve az internetkapcsolat nélküli, leválasztott környezetekhez. Azure Stack ugyanazokat a mögöttes technológiákat használja, mint a globális Azure, amely magában foglalja az infrastruktúra-szolgáltatás (IaaS), a szolgáltatott szoftver (SaaS) és az opcionális platform-szolgáltatás (Pásti) funkcióinak alapvető összetevőit, beleértve a következőket:

- Azure-beli virtuális gépek Windows és Linux rendszerekhez
- Azure-Web Apps és-függvények
- Azure Key Vault
- Azure Resource Manager
- Azure Piactér
- Containers
- Felügyeleti eszközök (csomagok, ajánlatok, RBAC stb.)

A Azure Stack Péter képességei nem kötelezőek, mert Azure Stack nem a Microsoft üzemelteti, hanem az ügyfelek működtetik. Ez azt jelenti, hogy bármely olyan Pásti-szolgáltatást kínálhat, amelyet a végfelhasználók számára kíván használni, ha készen áll arra, hogy elkészítse az alapul szolgáló infrastruktúrát és folyamatokat a végfelhasználótól. Azure Stack azonban több opcionális, például a App Service, az SQL Database és a MySQL-adatbázisokat is tartalmazza. Ezek erőforrás-szolgáltatóként érkeznek, így a több-bérlős használatra kész, az idő múlásával frissülnek a standard Azure Stack frissítésekkel, amelyek láthatók a Azure Stack portálon, és jól integrálva vannak Azure Stack.

A fent ismertetett erőforrás-szolgáltatók mellett további, a IaaS-ben futó, [sablonon alapuló megoldások Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) , de Ön Azure stack operátora is elérhetővé teheti őket a felhasználók számára, például a következőket:

- Service Fabric
- Kubernetes-tároló szolgáltatás
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Példa a Azure Stack használati eseteire:

- Pénzügyi modellezés
- Klinikai és jogcímek
- IoT-eszköz elemzése
- Kiskereskedelmi szortiment optimalizálása
- Ellátási lánc optimalizálása
- Ipari IoT
- Prediktív karbantartás
- Smart City
- Állampolgári szerepvállalás

További információ a Azure Stackról: [mi Azure stack](azure-stack-overview.md).

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

[Azure Stack adminisztráció alapjai](azure-stack-manage-basics.md)

[Rövid útmutató: a Azure Stack felügyeleti portál használata](azure-stack-manage-portals.md)
