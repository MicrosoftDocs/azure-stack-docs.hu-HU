---
title: Megbízható vállalati virtualizálás üzembe helyezése Azure Stack HCI-ben
description: Ez a témakör útmutatást nyújt arról, hogyan tervezhet, konfigurálhat és helyezhet üzembe egy olyan, magasan biztonságos infrastruktúrát, amely megbízható vállalati virtualizációt használ a Azure Stack HCI operációs rendszeren.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: 37a29bcfae28f1b1f01cf8e459df016a3b5c4daf
ms.sourcegitcommit: 330d04d39e0cf3e8965e2ccbc181c968cb71d9ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052649"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Megbízható vállalati virtualizálás üzembe helyezése Azure Stack HCI-ben

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör útmutatást nyújt arról, hogyan tervezhet, konfigurálhat és helyezhet üzembe egy olyan, magasan biztonságos infrastruktúrát, amely megbízható vállalati virtualizációt használ a Azure Stack HCI operációs rendszeren. Használja ki a Azure Stack HCI investmentt a biztonságos számítási feladatok futtatásához a [virtualizációs alapú biztonságot (vbs)](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs) és a hibrid felhőalapú szolgáltatásokat használó hardvereken a Windows felügyeleti központban és a Azure Portal.

## <a name="overview"></a>Áttekintés
A VBS az [Azure stack HCI-ben lévő biztonsági beruházások](/windows-server/get-started-19/whats-new-19#security) kulcsfontosságú összetevője, amely a gazdagépek és a virtuális gépek (VM-EK) biztonsági fenyegetésektől való védelmére szolgál. Például a [biztonsági technikai megvalósítási útmutató (Stig)](https://nvd.nist.gov/ncp/checklist/914), amely a védelmi minisztérium (DoD) információs rendszereinek biztonságának javítására szolgáló eszközként van közzétéve, a vbs és a [hypervisor által védett kód integritását (HVCI)](https://docs.microsoft.com/windows-hardware/drivers/bringup/device-guard-and-credential-guard) általános biztonsági követelményekként sorolja fel. Fontos, hogy a (z) VBS és a HVCI számára engedélyezett gazdagép-hardvert a virtuális gépeken lévő munkaterhelések védelmére használja, mivel a feltört gazdagép nem tudja garantálni a VM-védelmet.

A VBS hardveres virtualizációs funkciókat használ a memória biztonságos régiójának létrehozásához és elkülönítéséhez az operációs rendszerből. A Windowsban a [virtuális biztonságos mód (VSM)](https://docs.microsoft.com/virtualization/hyper-v-on-windows/tlfs/vsm) számos biztonsági megoldás futtatásával nagy mértékben növelheti az operációs rendszer biztonsági rései és a rosszindulatú támadások elleni védelmet.

A VBS a Windows Hypervisort használja a biztonsági határok létrehozásához és kezeléséhez az operációs rendszer szoftverében, korlátozásokat kényszerítve a létfontosságú rendszererőforrások védelmére, valamint a biztonsági eszközök, például a hitelesített felhasználói hitelesítő adatok védelmére. A VBS használatával, akkor is, ha a kártevők hozzáférnek az operációs rendszer rendszermaghoz, nagy mértékben korlátozhatja és tartalmazhatja a lehetséges biztonsági réseket, mivel a hypervisor megakadályozza a kártevők programkódok futtatását vagy a platform titkainak elérését.

A hypervisor, a legalkalmasabb rendszerszoftver, beállítja és érvényesíti az oldal engedélyeit az összes rendszermemóriában. A VSM-ben a lapok csak a kód sértetlenségének ellenőrzése után hajthatók végre. A kódlap nem módosítható, még akkor is, ha olyan biztonsági rést okoz, amely lehetővé teszi a kártevők számára a memória módosítását, és a módosított memória nem hajtható végre. A VBS és a HVCI jelentősen erősíti a kód-integritási szabályzatok betartatását. A rendszer az összes kernel módú illesztőprogramot és bináris fájlt ellenőrzi, és az aláíratlan illesztőprogramokat vagy rendszerfájlokat nem lehet betölteni a rendszermemóriába.

## <a name="deploy-trusted-enterprise-virtualization"></a>Megbízható vállalati virtualizálás üzembe helyezése
Ez a szakasz azt ismerteti, hogyan lehet hardvert beszerezni egy olyan, magas biztonságú infrastruktúra üzembe helyezéséhez, amely megbízható nagyvállalati virtualizációt használ a Azure Stack HCI és a Windows felügyeleti központban a felügyelethez.

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>1. lépés: hardver beszerzése a megbízható nagyvállalati virtualizálás Azure Stack HCI-ben
Először be kell szereznie a hardvert. Ennek a legegyszerűbb módja, ha megkeresi a kívánt Microsoft-hardveres partnert a [Azure stack HCI-katalógusban](https://hcicatalog.azurewebsites.net) , és megvásárol egy integrált rendszert a Azure stack HCI operációs rendszerrel előre telepítve. A katalógusban szűrheti az ilyen típusú számítási feladatokhoz optimalizált szállítói hardvert.

Ellenkező esetben telepítenie kell a Azure Stack HCI operációs rendszert a saját hardverén. Az Azure Stack HCI telepítési lehetőségeivel és a Windows felügyeleti központ telepítésével kapcsolatos részletekért lásd: [a Azure stack HCI operációs rendszer](./operating-system.md)telepítése.

Ezután a Windows felügyeleti központban [hozzon létre egy Azure stack HCI-fürtöt](./create-cluster.md).

Az Azure Stack HCI-hez készült összes partneri hardver minősítése a megbízhatósági kiegészítő minősítéssel történik. A minősítési folyamat teszteli az összes szükséges [vbs](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs) -funkciót. A VBS és a HVCI azonban nem lesz automatikusan engedélyezve Azure Stack HCI-ben. További információ a hardveres garancia további minősítéséről: a [Windows Server Catalog](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016) **rendszerek** területén a "hardveres megbízhatóság" című témakör.

   >[!WARNING]
   > Előfordulhat, hogy a HVCI nem kompatibilisek a Azure Stack HCI-katalógusban nem szereplő hardvereszközökkel. Javasoljuk, hogy az Azure Stack HCI által ellenőrzött hardvert a partnereinktől a megbízható vállalati virtualizálási infrastruktúrához használja.

### <a name="step-2-enable-hvci"></a>2. lépés: a HVCI engedélyezése
Engedélyezze a HVCI a kiszolgálói hardvereken és a virtuális gépeken. Részletekért lásd: a [kód integritásának virtualizálás-alapú védelmének engedélyezése](https://docs.microsoft.com/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity).

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>3. lépés: a Azure Security Center beállítása a Windows felügyeleti központban
A Windows felügyeleti központban hozzon létre [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction) a veszélyforrások elleni védelem hozzáadásához és a számítási feladatok biztonsági állapotának gyors felméréséhez.

További információ: [a Windows felügyeleti központ erőforrásainak Security Centersal](https://docs.microsoft.com/azure/security-center/windows-admin-center-integration)való ellátása.

A Security Center megkezdéséhez:
- Ehhez egy Microsoft Azure-előfizetésre van szüksége. Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free).
- A Security Center ingyenes díjszabási szintje minden aktuális Azure-előfizetésen engedélyezve van, ha a Azure Portal Azure Security Center irányítópultját látogatják, vagy programozott módon teszik lehetővé az API-n keresztül.
A fokozott biztonságú felügyelet és a fenyegetések észlelési képességeinek kihasználásához engedélyeznie kell az Azure Defendert. Az Azure Defender 30 napig ingyenesen használható. További információ: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center).
- Ha készen áll az Azure Defender engedélyezésére, tekintse meg a rövid útmutató [: Azure Security Center beállítása](https://docs.microsoft.com/azure/security-center/security-center-get-started) című témakört, amely végigvezeti a lépéseken.

A Windows felügyeleti központ használatával további Azure Hybrid Services-szolgáltatásokat is beállíthat, például biztonsági mentést, File Sync, Site Recovery, pont – hely VPN-t, Update Managementt és Azure Monitort.

## <a name="next-steps"></a>További lépések
A megbízható vállalati virtualizálás szolgáltatással kapcsolatos további információkért lásd:
- [Hyper-V Windows Serveren](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
