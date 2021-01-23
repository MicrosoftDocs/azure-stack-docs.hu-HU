---
title: Azure Stack HCI összehasonlítása a Windows Serverrel
description: Ez a témakör segít megállapítani, hogy Azure Stack HCI vagy Windows Server megfelelő-e a szervezete számára.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705195"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>Azure Stack HCI összehasonlítása a Windows Server 2019-rel

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör ismerteti a Azure Stack HCI és a Windows Server 2019 közötti fő különbségeket, és útmutatást nyújt a használatáról. A Microsoft aktívan támogatja és karbantartja mindkét terméket. Számos szervezet dönthet úgy, hogy mindkettőt telepíti, mivel azok különböző és kiegészítő célokra szolgálnak.

## <a name="when-to-use-azure-stack-hci"></a>Mikor kell használni a Azure Stack HCI-t

A Azure Stack HCI a Microsoft Premier hiperkonvergens infrastrukturális platformja, amely a helyszíni virtuális gépeket vagy virtuális asztalokat futtatja az Azure Hybrid Serviceshez való csatlakozással. Ez egy egyszerű módszer az adatközpontok és fiókirodák korszerűsítésére és biztonságossá tételére, valamint az iparág legjobb teljesítményének elérésére alacsony késéssel és az adatszuverenitással.


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="Mikor kell használni a Azure Stack HCI-t a Windows Server 2019-nél" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


Azure Stack HCI használata a következőhöz:

- A legjobb virtualizációs gazdagép az infrastruktúra korszerűsítéséhez, vagy az alapszintű adatközpontban meglévő számítási feladatokhoz, vagy a fiókirodák és a peremhálózati telephelyek új követelményeihez
- Könnyen bővíthető a felhőbe, és az Azure-előfizetésből származó innovatív innovációk, valamint az eszközök és a tapasztalatok konzisztens készlete
- A hiperkonvergens-infrastruktúra előnyei: egyszerűbb, konszolidált adatközpont-architektúra nagy sebességű tárolással és hálózatkezeléssel

  >[!NOTE]
  >Mivel Azure Stack a HCI-t Hyper-V virtualizációs gazdagépként kívánja használni egy modern, hiperkonvergens architektúra számára, nem tartalmazza a vendég jogosultságokat. Ezért Azure Stack a HCI csak kis számú kiszolgálói szerepkör futtatására jogosult. minden más szerepkörnek a virtuális gépeken belül kell futnia.

## <a name="when-to-use-windows-server-2019"></a>Mikor kell használni a Windows Server 2019-et

A Windows Server 2019 egy sokoldalú, többcélú operációs rendszer, több tucat szerepkörrel és több száz funkcióval, beleértve a vendég jogosultságokat is. A Windows Server rendszerű gépek lehetnek a felhőben vagy a helyszínen, beleértve a Azure Stack HCI-re épülő virtualizált is.


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="Mikor kell használni a Windows Server 2019-et Azure Stack HCI-en keresztül" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


A Windows Server 2019 használata a következőhöz:

- A virtuális gépeken (VM) vagy tárolókban található vendég operációs rendszer
- Windows-alkalmazás futtatókörnyezete
- Egy vagy több beépített kiszolgálói szerepkör (például Active Directory, Fájlszolgáltatások, DNS, DHCP vagy Internet Information Services (IIS) használata
- Hagyományos kiszolgálóként, például operációs rendszer nélküli tartományvezérlő vagy SQL Server telepítés
- Hagyományos infrastruktúrához, például Fibre Channel SAN Storage-hoz csatlakoztatott virtuális gépekhez

## <a name="compare-product-positioning"></a>A termék pozicionálásának összehasonlítása

Az alábbi táblázat a Azure Stack HCI és a Windows Server 2019 magas szintű termék-csomagolását mutatja be.

| **Attribútum**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| Terméktípus     | Operációs rendszert és egyéb szolgáltatásokat is tartalmazó Cloud Service | Operációs rendszer |
| Jogi tudnivalók            | A Microsoft-szerződés vagy az online előfizetési szerződés hatálya alá tartozik | Saját végfelhasználói licencszerződéssel rendelkezik |
| Licencelés        | Az Azure-előfizetéshez tartozó számlázás | Saját fizetős licenccel rendelkezik |
| Támogatás          | Az Azure-támogatás alá tartozó | Különböző támogatási szerződések, például a Microsoft Premier szintű támogatás |
| Honnan szerezhető be?  | Töltse le a [Azure.com/HCI](https://azure.com/hci)-ból, vagy telepítse előre az integrált rendszereken | Microsoft mennyiségi licencelés Service Center vagy próbaverziós központ |
| Virtuális gépeken fut      | Csak értékelés esetén; gazda operációs rendszernek szánt | Igen, a felhőben vagy a helyszínen |
| Hardver         | Több mint 200 előre érvényesített megoldáson fut a [Azure stack HCI katalógusból](https://hcicatalog.azurewebsites.net) | A "Certified for Windows Server 2019" emblémával rendelkező hardveren fut |
| Életciklus-szabályzat | Mindig naprakész a legújabb funkciókkal | Válasszon a [Windows Server karbantartási csatornák](/windows-server/get-started-19/servicing-channels-19)közül: Long-Term karbantartási csatorna (LTSC) és Semi-Annual Channel (SAC) |

## <a name="compare-technical-features"></a>Technikai funkciók összehasonlítása

A következő táblázat összehasonlítja Azure Stack HCI és a Windows Server 2019 műszaki funkcióit.

| **Attribútum** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Hyper-V mag | Igen | Igen |
| Core Közvetlen tárolóhelyek | Igen | Igen |
| Core SDN | Igen | Igen |
| Stretch fürtözés a vész-helyreállításhoz | Igen | - |
| 4 – 5x-ös gyorsabb tárolóhelyek javítása | Igen | - |
| Integrált illesztőprogram-és belső vezérlőprogram-frissítések | Igen (csak integrált rendszerek esetén) | - |
| Irányított üzembe helyezés | Igen | - |

## <a name="compare-management-options"></a>Felügyeleti lehetőségek összehasonlítása

A következő táblázat összehasonlítja Azure Stack HCI és a Windows Server 2019 felügyeleti lehetőségeit. Mindkét termék távoli felügyeletre lett tervezve, és több azonos eszközzel is kezelhető.

| **Attribútum** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Asztali élmény | - | Igen |
| Windows felügyeleti központ | Igen | Igen |
| Microsoft System Center | Igen (külön megvásárolható) | Igen (külön megvásárolható) |
| Azure Portal | Igen (natív módon) | Ív-ügynök szükséges |
| Harmadik féltől származó eszközök | Igen | Igen |

## <a name="compare-product-pricing"></a>A termékek díjszabásának összehasonlítása

Az alábbi táblázat a Azure Stack HCI és a Windows Server 2019 termék díjszabását hasonlítja össze. Részletekért lásd: [Azure stack HCI díjszabása](https://azure.microsoft.com/pricing/details/azure-stack/hci/).

| **Attribútum** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Ár típusa | Előfizetési szolgáltatás | Változó: leggyakrabban egyszeri licenc |
| Ár szerkezete | /Mag, havonta | Változó: általában/mag |
| Ár | $10 USD/mag, havonta | Tekintse [meg a Windows Server 2019 díjszabását és licencelését](https://www.microsoft.com/windows-server/pricing) |
| Értékelés/próbaidőszak | 30 napos ingyenes próbaverzió regisztráció után | 180 napos próbaverziós másolat |
| Csatornák | Nagyvállalati szerződés, felhőalapú szolgáltató vagy közvetlen | Nagyvállalati szerződés/mennyiségi licencelés, OEM, szolgáltatási szolgáltatói licencszerződés (SPLA) |

## <a name="next-steps"></a>További lépések

- [Azure Stack HCI összehasonlítása Azure Stack hubhoz](compare-azure-stack-hub.md)
