---
title: Az Azure Stack segítségével az infrastruktúra biztonsági mentési szolgáltatás súlyos adatvesztés utáni helyreállítás |} A Microsoft Docs
description: Ha a végzetes hiba miatt sikertelenek lesznek, Ön telepítheti az Azure Stack az infrastruktúra adatok visszaállításához, amikor az Azure Stack üzemelő példány visszaállítása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f459804d33871ec97bf50e55ed01685001dcbfc7
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617914"
---
# <a name="recover-from-catastrophic-data-loss"></a>Végzetes adatvesztés utáni helyreállítás

*Vonatkozik: Az Azure Stackkel integrált rendszerek.*

Az Azure Stack Azure-szolgáltatások fut a helyi adatközpontban, és négy csomópont az egyetlen állványon telepítve legyen a környezetben futtatható. Ezzel szemben az Azure több mint 40 régióban több adatközpont és az egyes régiókban több zónában futtatja. Felhasználói erőforrásokat is kiterjedhetnek több kiszolgálók, állványokon, adatközpontok és régiók. Az Azure Stack jelenleg csak választhat, hogy a teljes felhőalapú üzembe egyetlen állványon. Ez elérhetővé teszi a felhőben, az adatközpontban vagy a hiba oka, hogy jelentős termékhibákat katasztrofális esemény kockázatát. Ha egy katasztrófa utáni feladatokat, az Azure Stack-példány offline állapotba kerül. Összes adat potenciálisan helyreállíthatatlan.

Az adatvesztés kiváltó okának függően szükség lehet egy egyetlen infrastruktúra-szolgáltatás javítása, vagy állítsa vissza a teljes Azure Stack-példány. Akkor is szükség lehet ugyanazon a helyen, vagy egy másik helyen lévő másik hardverre visszaállítása.

A forgatókönyv címek helyreállítása a teljes telepítési hiba esetén a berendezés és az újbóli üzembe helyezés a magánfelhő.

| Forgatókönyv                                                           | Adatvesztés                            | Megfontolandó szempontok                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Vészhelyreállítási vagy a termékverzió hiba miatt végzetes adatvesztés utáni helyreállítás | Az összes infrastruktúrát és a felhasználó és az alkalmazás adatokat | Felhasználó alkalmazás- és védett külön-külön infrastruktúra-adatokból |

## <a name="workflows"></a>A munkafolyamatok

Külön-külön biztonsági mentése az infrastruktúra és az alkalmazás vagy a bérlőjéhez adatok védelme az Azure indítsa el az utazás kezdődik. Ez a dokumentum ismerteti, hogyan védheti meg az infrastruktúra. 

![Kezdeti üzembe helyezhető Azure Stacket](media/azure-stack-backup/azure-stack-backup-workflow1.png)

A legrosszabb, használati esetek, ahol minden adat elveszik helyreállítása az Azure Stack az a folyamat egyedi az infrastruktúra-adatok visszaállítása az Azure Stack és az összes felhasználói adatot a telepítés. 

![Az Azure Stack ismételt üzembe helyezése](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Visszaállítás

Ha nincs végzetes adatvesztés, de továbbra is használható a hardvert, az újbóli üzembe helyezés az Azure Stack szükség. Újbóli üzembe helyezés, során megadhatja a tárolási helyét és a biztonsági másolatok eléréséhez szükséges hitelesítő adatokat. Ebben a módban van, nem szükséges, adja meg, hogy vissza kell állítani a szolgáltatásokat. Infrastruktúra biztonsági mentést vezérlő kódtárba vezérlő adatsík állapotának a telepítési munkafolyamat részeként.

Ha olyan használhatatlan rendereli a hardvert, újbóli üzembe helyezés csak akkor lehetséges, új hardverre. Újbóli üzembe helyezés hetet is igénybe vehet néhány helyettesítő hardver van rendezve, és az adatközponton belül érkezik. Vezérlési sík adatok visszaállítása bármikor lehetőség. Visszaállítási azonban nem támogatott, ha az újratelepített-példány verziója újabb, mint a legutóbbi biztonsági mentés a használt verzió egynél több verzió. 

| Üzembe helyezési mód | Kezdőpont | Végpont                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tiszta telepítés   | Alapkonfiguráció-build | OEM helyez üzembe az Azure Stack, és a legújabb támogatott verziót frissíti.                                                                                                                                          |
| A helyreállítási mód   | Alapkonfiguráció-build | OEM helyez üzembe az Azure Stack helyreállítási módban, és kezeli a alapján a rendelkezésre álló legfrissebb biztonsági mentés követelményeinek megfelelő verziója. Az OEM által frissítése a legújabb támogatott verziót az üzembe helyezés befejeződik. |

## <a name="data-in-backups"></a>Biztonsági adatok

Az Azure Stack a felhőalapú helyreállítási mód nevű központi telepítés típusát támogatja. Ebben az üzemmódban csak akkor, ha úgy dönt, hogy az Azure Stack helyreállítása vészhelyzet után, vagy a termék hiba jelenik meg a megoldás helyreállíthatatlan szolgál. Ebben a telepítési módban nem tudja a megoldásban tárolt felhasználói adatok. Ebben a telepítési módban hatóköre korlátozva visszaállítása a következő adatokat:

 - Üzembe helyezés bemenetek
 - Belső identitáskezelési rendszerek
 - Összevont (kapcsolat nélküli üzembe helyezés) konfiguráció azonosítására
 - Belső hitelesítésszolgáltató által használt legfelső szintű tanúsítványok
 - Az Azure Resource Manager konfigurációs felhasználói adatok, például az előfizetések, csomagok, ajánlatok és kvóták tároláshoz, hálózati és számítási erőforrások
 - A KeyVault titkos és tárolók
 - Az RBAC-szabályzat-hozzárendelések és a szerepkör-hozzárendelések 

A felhasználó infrastruktúra-szolgáltatás (IaaS) vagy a Platform, a Platformszolgáltatás (PaaS) erőforrások egyike állíthatók helyre, üzembe helyezés során. Vagyis IaaS virtuális gépek, tárfiókok, blobok, táblák, hálózati konfigurációt, és így tovább, elvesznek. A felhőalapú helyreállítás célja annak biztosítása érdekében az operátorok, a felhasználók vissza a portálra való üzembe helyezés után. Bejelentkezés felhasználók nem fogják látni a saját erőforrások. Felhasználók a visszaállított előfizetéssel rendelkezik, és együtt, amely az eredeti terveket, és a rendszergazda által meghatározott szabályzatokat kínál. A felhasználó bejelentkezik a rendszer az eredeti megoldásban a vészhelyzet előtt az azonos korlátozásai alapján működik. Felhőbeli helyreállítási befejezése után az operátor manuálisan állíthatja vissza hozzáadott értéket és a külső RPs, és a kapcsolódó adatokat.

## <a name="next-steps"></a>További lépések

További információ az ajánlott eljárások [az infrastruktúra biztonsági másolat szolgáltatással](azure-stack-backup-best-practices.md).
