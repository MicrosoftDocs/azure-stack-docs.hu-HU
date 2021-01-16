---
title: Segédprogram-alkalmazások Azure Stack HCI-hez
description: Ez a témakör a Microsoft partnereitől származó, a Azure Stack HCI operációs rendszert támogató alkalmazásokat ismerteti.
author: burcuerdemir
ms.author: burcue
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: ec7623243c09ffc93036ada1927cbe62acad0b9d
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254959"
---
# <a name="utility-applications-for-azure-stack-hci"></a>Segédprogram-alkalmazások Azure Stack HCI-hez

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Azure Stack HCI integrálja a hiperkonvergens-infrastruktúra (HCI) technológiáját a Azure Stack családba, így ismerős módon futtathat segédprogram-alkalmazásokat, és könnyen hozzáférhet az Azure-szolgáltatásokhoz. A Azure Stack a Microsoft egyedi hibrid felhőalapú ajánlata, amely a Felhőbeli funkciókat a helyszíni adatközpontba integrálja. További információ: [Azure stack HCI megoldás áttekintése](../overview.md).

A Azure Stack HCI független szoftvergyártói programja olyan segédprogramok számára készült, amelyek közvetlenül a Azure Stack HCI operációs rendszeren futnak, amely az érvényes licenccel rendelkező virtuális gépeket (VM-ket) üzemelteti, felügyeli és szolgáltatásként futtatja. Ez a témakör az operációs rendszert támogató Microsoft-partnerektől származó, a szolgáltatással foglalkozó eszköz-alkalmazásokat ismerteti.

Javasoljuk, hogy a Microsoft Defender víruskeresőt az operációs rendszerrel együtt használja.A víruskereső és a kártevő szoftverek észlelésével kapcsolatos egyéb szoftverek és szolgáltatások hatással lehetnek az operációs rendszer frissítéseinek fogadására. Ez az útmutató az operációs rendszer későbbi kiadásaiban is változhat.

## <a name="partner-spotlight"></a>Partner Spotlight
Az operációs rendszert támogató segédprogram-alkalmazások az alábbi Microsoft-partnerektől szerezhetők be.

:::image type="content" source="./media/utility-applications/altaro-logo.png" alt-text="Altáró-cég emblémája" border="false":::

A Altáró VM Backup egy díjnyertes VM biztonsági mentési és replikációs megoldás a Hyper-V és a VMware környezetekhez. Az informatikai részlegek, az informatikai viszonteladók és a tanácsadók, valamint a felügyelt szolgáltatók (MSP-EK) számára készült megoldás robusztus, gördülékeny, nagyvállalati szintű funkciókat biztosít.

- Alkalmazás listázása: [virtuális gépek biztonsági mentése és replikációja a Hyper-V és a VMware rendszerhez](https://www.altaro.com/vm-backup/)
- Alkalmazás-támogatás: [Altáró-támogatás Azure stack HCI-hez](https://www.altaro.com/news/single/News-Altaro-applies-its-expertise-in-Hyper-V-backup-to-support-Microsoft.php)

:::image type="content" source="./media/utility-applications/veeam-logo.png" alt-text="Veeam-cég emblémája" border="false":::

A Veeam Backup & replikációja az egyetlen megoldás a Felhőbeli, virtuális és fizikai számítási feladatok egyszerű, megbízható és rugalmas védelméhez. Vegye figyelembe az adatvédelmet, a ransomware megelőzését és a megfelelőségi kihívásokat.

- Alkalmazás listázása: [Veeam biztonsági mentési & replikációja](https://www.veeam.com/vm-backup-recovery-replication-software.html)
- Alkalmazás-támogatás: [Veeam-támogatás Azure stack HCI-hez](https://www.veeam.com/kb4047)

:::image type="content" source="./media/utility-applications/commvault-logo.png" alt-text="CommVault-cég emblémája" border="false":::

A CommVault egy teljes körű adatkezelési platform, amely a helyszíni és a Felhőbeli helyeken tárolt adattárolást,-védelemmel,-felügyeletet és-használatot végzi.

- Alkalmazás listázása: [Microsoft Azure stack](https://www.commvault.com/supported-technologies/microsoft/azurestack)
- Alkalmazás-támogatás: [Azure stack HCI](https://documentation.commvault.com/11.21/essential/132799_microsoft_azure_stack_hci.html)

:::image type="content" source="./media/utility-applications/veritas-logo.png" alt-text="A Veritas vállalati emblémája" border="false":::

A Veritas Backup Exec egy könnyen használható biztonsági mentési és helyreállítási megoldás, amely segít a környezete virtuális, fizikai és Felhőbeli erőforrásainak biztonságában.

- Alkalmazás listázása: [Backup Exec](https://www.veritas.com/protection/backup-exec)
- Alkalmazás-támogatás: [Azure stack HCI Protect exec biztonsági mentése](https://www.veritas.com/support/en_US/article.100048860)

:::image type="content" source="./media/utility-applications/datadog-logo.png" alt-text="Datadag-cég emblémája" border="false":::

A datadoggal egy teljes körűen egységesített platform, amely magában foglalja az infrastruktúra figyelését, az alkalmazások teljesítményének monitorozását, a naplózás kezelését, a felhasználói élmény figyelését és egyebeket.

- Alkalmazás listázása: [modern monitorozási & biztonság](https://www.datadoghq.com/)
- Alkalmazás-támogatás: [Microsoft Azure stack HCI monitorozása a datadoggal](https://www.datadoghq.com/blog/monitor-azure-stack-hci-datadog)

## <a name="next-steps"></a>További lépések
További információért lásd még:
- [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/)
- [Microsoft Defender víruskereső](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-in-windows-10)