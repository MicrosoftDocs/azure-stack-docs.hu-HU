---
title: Az Azure-t és Azure Stack hub-t használó elemzési megoldáshoz használt többszintes adatelemzési minta.
description: Ismerje meg, hogyan használhatja az Azure-t és Azure Stack hub-szolgáltatásokat egy többszintű adatmegoldás megvalósításához a hibrid felhőben.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 91f23e7362ec0a1a733417dad1f48dc04b80d19f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875521"
---
# <a name="tiered-data-for-analytics-pattern"></a>Többszintes adatelemzési minta

Ez a minta azt szemlélteti, hogyan használható a Azure Stack hub és az Azure a különböző helyszíni és Felhőbeli helyekre vonatkozó adatgyűjtéshez, elemzéséhez, feldolgozásához, tisztításához és tárolásához.

## <a name="context-and-problem"></a>Kontextus és probléma

A modern technológiai környezet vállalati szervezeteinek egyik problémája a biztonságos adattárolás,-feldolgozás és-elemzés. A szempontok a következők:
- adattartalom
- location
- biztonsági és adatvédelmi követelmények
- hozzáférési engedélyek
- karbantartási
- Storage-tárház

Az Azure Azure Stack hub-val együtt kezeli az adatkezelési problémákat, és alacsony költséghatékonyságú megoldásokat kínál. Ez a megoldás egy elosztott gyártási vagy logisztikai vállalaton keresztül van kifejezve. 

A megoldás a következő forgatókönyvön alapul:
- Egy nagyméretű multi-Branch Manufacturing-szervezet.
- Szükség van a gyors és biztonságos adattárolásra,-feldolgozásra és-elosztásra a globális távoli helyek és a központi központ között. 
- Az alkalmazottak és a gépek tevékenységének, a létesítmény információinak és az üzleti jelentéskészítési adatoknak biztonságosnak kell maradniuk. Az adatforgalomnak megfelelően kell kiosztani, és meg kell felelnie a regionális megfelelőségi szabályzatnak és az iparági előírásoknak.

## <a name="solution"></a>Megoldás

A helyszíni és a nyilvános felhőalapú környezetek egyaránt kielégítik a több létesítményt használó vállalatok igényeit. A Azure Stack hub gyors, biztonságos és rugalmas megoldást kínál a helyi és a távoli adatok gyűjtésére, feldolgozására, tárolására és terjesztésére. Különösen akkor, ha a biztonság, a titkosság, a vállalati házirend és a szabályozási követelmények eltérőek lehetnek a helyszínek és a felhasználók között. 

![többplatformos adatelemzési megoldás architektúrája](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Component (Összetevő) | Leírás |
|----------|-----------|-------------|
| Azure | Adattárolás | Az [Azure Storage](/azure/storage/) -fiókok steril adatfelhasználási végpontot biztosítanak. Az Azure Storage a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az Azure Storage nagymértékben méretezhető objektum-tárolót biztosít az adatobjektumokhoz és a felhőhöz tartozó fájlrendszer-szolgáltatásokhoz. Emellett egy üzenetküldési tárolót is biztosít a megbízható üzenetkezeléshez, valamint egy NoSQL-tárolót. |
| Azure Stack Hub | Adattárolás | [Azure stack hub Storage](/azure-stack/user/azure-stack-storage-overview) -fiókot több szolgáltatáshoz is használunk:<br>**blob storage** - a nyers adattároláshoz. A blob Storage bármilyen típusú szöveget vagy bináris adattípust tartalmazhat, például dokumentumot, médiafájlt vagy Application Installert. Minden blob egy tárolóban van rendszerezve. A tárolók hasznos módszert biztosítanak a biztonsági szabályzatok objektumok csoportjaihoz való hozzárendeléséhez. A Storage-fiókok tetszőleges számú tárolót tartalmazhatnak, és a tárolók tetszőleges számú blobot tartalmazhatnak a Storage-fiók 500 TB-os kapacitási korlátja alapján.<br>az adatarchívumok **blob storage** - . A ritkán használt adatok archiválása alacsony díjszabású tárolást tesz elérhetővé. A ritkán használt adatok közé tartoznak például a biztonsági másolatok, a médiatartalom, a tudományos adatok, a megfelelőség és az archiválási adatok. Általánosságban elmondható, hogy a ritkán használt adatok ritka tárolásnak minősülnek. az adatok az olyan attribútumok alapján, mint a hozzáférés gyakorisága és a megőrzési időszak. A vásárlói adatok ritkán érhetők el, de hasonló késést és teljesítményt igényelnek a gyors adateléréshez.<br>- **üzenetsor-tároló** a feldolgozott adattároláshoz. A üzenetsor-tárolás Felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A várólista-tároló aszinkron üzenetküldést biztosít az alkalmazás-összetevők közötti kommunikációhoz.  Függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is. |
| | Azure Functions | A [Azure functions](/azure/azure-functions/) szolgáltatást a [Azure stack hub](/azure-stack/operator/azure-stack-app-service-overview) erőforrás-szolgáltatójának Azure app Service biztosítja. Azure Functions lehetővé teszi, hogy a kódot egy egyszerű, kiszolgáló nélküli környezetben hajtsa végre, hogy parancsfájlt vagy kódot futtasson a különböző eseményekre adott válaszként. Azure Functions a méretezést az igények kielégítése érdekében anélkül, hogy létre kellene hoznia egy virtuális gépet vagy közzé kellene tennie egy webalkalmazást az Ön által választott programozási nyelv használatával. A megoldás a következő funkciókat használja:<br>**adatbevitel** - <br>- **az adatsterilizálás.** A manuálisan aktivált függvények ütemezett adatfeldolgozást, karbantartást és archiválást végezhetnek. Ilyenek lehetnek például az esti ügyfelek listáját tartalmazó cserjések és a havi jelentések feldolgozása.|

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Skálázhatóság 

Az Azure functions és a Storage megoldások méretezhetők az adatmennyiség és a feldolgozási igények kielégítése érdekében. Az Azure méretezhetőségi információi és céljai: az [Azure Storage skálázhatósági dokumentációja](/azure/storage/common/storage-scalability-targets). 

### <a name="availability"></a>Elérhetőség

Ennek a mintának az elsődleges rendelkezésre állási szempont a tárterület. A nagy adatmennyiség-feldolgozáshoz és-terjesztéshez gyors kapcsolaton keresztüli kapcsolódás szükséges. 

### <a name="manageability"></a>Kezelhetőség

A megoldás kezelhetősége a használatban lévő és a verziókövetés bevezetését szolgáló eszközöktől függ. 

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- Tekintse meg az [Azure Storage](/azure/storage/) és a [Azure functions](/azure/azure-functions/) dokumentációját. Ez a minta az Azure Storage-fiókok és a Azure Functionsek nagy mennyiségű használatát teszi lehetővé az Azure-ban és az Azure Stack központban is.
- Az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásáért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa az [elemzési megoldás üzembe helyezési útmutatója](https://aka.ms/tiereddatadeploy)című témakörben foglalt szintű adatelemzéssel. A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.