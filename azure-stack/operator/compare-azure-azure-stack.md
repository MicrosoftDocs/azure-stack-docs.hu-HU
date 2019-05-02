---
title: Azure Stack és a globális Azure összehasonlítása |} A Microsoft Docs
description: Ismerje meg, hogyan nyújt a Microsoft, Azure és az Azure Stack operációsrendszer-szolgáltatások az Azure-ökoszisztéma hasznosítása
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: fb8b3e2cd973fc2e42540cd7ba45392829f605f8
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984440"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Globális Azure, Azure Stack és az Azure Stack HCl közötti különbségek

A Microsoft Azure és az Azure Stack operációsrendszer-szolgáltatások az Azure-ökoszisztéma hasznosítása biztosít. Használja az azonos alkalmazásfejlesztési modell, önkiszolgáló portálok és API-k az Azure Resource Managerrel, hogy a felhőalapú képességek az üzleti használja az Azure globális vagy helyi erőforrásokhoz.

Ez a cikk ismerteti a globális Azure, az Azure Stack, és az Azure Stack HCl képességeket, és nyújt a gyakori forgatókönyv segít meghozni a legjobb választás a Microsoft felhőalapú szolgáltatások a szervezet számára.

![Azure-ökoszisztéma – áttekintés](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globális Azure

A Microsoft Azure felhőszolgáltatások állandóan bővülő gyűjteménye, amely segítséget nyújt cégének üzleti céljai elérésében. Szabadon fejlesztéséhez, kezeléséhez, és helyezzen üzembe alkalmazásokat a kedvenc eszközeit és keretrendszerek használatával nagy, globális hálózaton.

Globális Azure több mint 100 54 régiókban világszerte elérhető szolgáltatásokat kínál. A globális Azure-szolgáltatás legfrissebb listájáért tekintse meg a [ *elérhető termékek régiók szerint*](https://azure.microsoft.com/regions/services). Az Azure-ban elérhető szolgáltatások a következők: a kategória, valamint felsorolt, hogy azok általánosan elérhető vagy előzetes verzióként érhető el.

Globális Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [első lépései az Azure-ral](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Az Azure Stack az Azure kiterjesztése, amely számos lehetőséget kínál a rugalmasságot és innovációjával emeli a felhő-számítástechnika a helyszíni környezetben. Üzembe helyezhető a helyszínen, az Azure Stack segítségével akár csatlakozik az internethez (és az Azure) vagy kapcsolat nélküli környezetben internetkapcsolat nélküli Azure konzisztens szolgáltatásokat. Az Azure Stack az alapul szolgáló technológiát használja, mint a globális Azure-ban, amely tartalmazza a fő összetevőinek infrastruktúra--szolgáltatásként (IaaS), szoftver--szolgáltatásként (SaaS), és nem kötelező Platform--szolgáltatásként (PaaS) funkcióit, többek között:

- A Windows és Linux rendszerű Azure virtuális gépek
- Azure Web Apps és Functions
- Azure Key Vault
- Azure Resource Manager
- Azure Piactér
- Containers
- Az Azure IoT Hub és az Event Hubs
- Felügyeleti eszközökkel (csomagok, ajánlatok, RBAC, stb.)

Az Azure Stack PaaS képességeit nem kötelező, mivel az Azure Stack nem Microsoft által üzemeltetett – ügyfeleink üzemelteti. Ez azt jelenti, hogy a végfelhasználók számára azt szeretné, ha az alapul szolgáló infrastruktúrát és folyamatokat a végfelhasználó távolabbi absztrakt felkészült bármilyen PaaS-szolgáltatás biztosítani képes. Azure Stack azonban számos választható PaaS szolgáltatót többek között az App Service-ben, az SQL Database-adatbázisok és a MySQL-adatbázisok tartalmazza. Ezek a kézbesített, erőforrás-szolgáltatók, így azok a több-bérlős kész, a frissített a standard Azure Stack frissíti az idő függvényében, az Azure Stack portálon látható és jól integrálva van az Azure Stack.

Az erőforrás-szolgáltatók a fent leírt, kívül további PaaS-szolgáltatások rendelkezésre álló és tesztelt, [Azure Resource Manager-sablon-alapú megoldások](https://github.com/Azure/AzureStack-QuickStart-Templates) futtató, IaaS, de, mint az Azure Stack operátori is elérhetővé teheti őket, mint A felhasználók számára, beleértve a PaaS-szolgáltatások:

- Service Fabric
- Kubernetes Container Service
- Az IoT Hub és az Eseményközpont
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Használati példák az Azure Stack:

- Pénzügyi modellezési
- Adatok klinikai és jogcímek
- IoT-eszköz analytics
- Kiskereskedelmi növekedett optimalizálása
- Ellátásilánc-optimalizálás
- Ipari IoT
- Prediktív karbantartás
- Az intelligens az városa
- A polgárok elkötelezettségének

További információ az Azure Stack, [az Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Az Azure Stack HCl-megoldások lehetővé teszik a helyszíni virtuális gépek futtatásához, és könnyedén csatlakozhat az Azure egy hiperkonvergens infrastruktúrával (HCL) megoldással. Egységes Azure-szolgáltatásokkal hozhat létre és futtathat felhőalapú alkalmazásokat a helyszínen a jogszabályi és műszaki előírásoknak való megfelelőség mellett. Mellett a virtualizált alkalmazások a helyszínen fut az Azure Stack HCl lehetővé teszi, hogy cserélje le, és konszolidálhatja az elévülés kiszolgálói infrastruktúrát, és csatlakozni az Azure cloud Services használatával a Windows Admin Center.

Az Azure Stack HCl működteti, a Hyper-V és a közvetlen tárolóhelyek a Windows Server 2019 szoftveralapú adatközpont (SDDC) ellenőrzött HCl megoldásokat nyújt. A Windows Admin Center felügyeletére használható, és az Azure-szolgáltatásokhoz való hozzáférés például integrálva:

- Azure Backup
- Azure Site Recovery
- Az Azure Monitor és frissítés

Az Azure frissített listáját, szolgáltatási kapcsolódhatnak-e, tekintse meg az Azure Stack HCl [hibrid Azure-szolgáltatásokhoz való csatlakozás Windows Server](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Az Azure Stack HCl használati példák
- Távoli vagy a fiókiroda office rendszer
- Adatközpont-konszolidáció
- Virtuális asztali infrastruktúra
- Üzleti szempontból kritikus fontosságú infrastruktúra
- Költséghatékony tárolás
- Magas rendelkezésre állás és vészhelyreállítás helyreállítási a felhőben
- Vállalati alkalmazások, például az SQL Server

Látogasson el a [Azure Stack HCl webhely](https://azure.microsoft.com/overview/azure-stack/hci/) 70-nél több Azure Stack HCl jelenleg elérhető megoldásokat a Microsoft-partnerektől megtekintéséhez.

## <a name="next-steps"></a>További lépések

[Az Azure Stack az Adminisztráció alapjai](azure-stack-manage-basics.md)

[Gyors útmutató: az Azure Stack felügyeleti portál használata](azure-stack-manage-portals.md)
