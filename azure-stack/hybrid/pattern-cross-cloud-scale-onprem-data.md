---
title: Az Azure-beli és Azure Stack hub-on keresztül a Felhőbeli adatskálát használó alkalmazások létrehozásának mintája.
description: Ismerje meg, hogyan használhatja az Azure-t és a Azure Stack hub-t egy méretezhető, többfelhős alkalmazás létrehozására, amely helyszíni információkat használ.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f492eba4cd2fae28162eb47b990737a8f5b21c53
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77689528"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Több felhőre kiterjedő méretezés (helyszíni adattípusok)

Ismerje meg, hogyan hozhat létre olyan hibrid alkalmazást, amely az Azure-t és Azure Stack hubot is felöleli. Ez a minta azt is bemutatja, hogyan használható egyetlen helyszíni adatforrás a megfelelőséghez.

## <a name="context-and-problem"></a>Kontextus és probléma

Számos szervezet nagy mennyiségű ügyfél-bizalmas adatot gyűjt és tárol. Gyakran meggátolják, hogy bizalmas adatokat tároljanak a nyilvános felhőben, a vállalati szabályozások vagy a kormányzati szabályzatok miatt. Ezek a szervezetek is szeretnék kihasználni a nyilvános felhő méretezhetőségét. A nyilvános felhő képes kezelni az adatforgalom szezonális csúcsait, így az ügyfeleknek pontosan a szükséges hardverért kell fizetniük, amikor szükségük van rá.

## <a name="solution"></a>Megoldás

A megoldás kihasználja a privát felhő megfelelőségi előnyeit, és kombinálja őket a nyilvános felhő méretezhetőségével. Az Azure és a Azure Stack hub Hybrid Cloud egységes felhasználói élményt nyújt a fejlesztőknek. Ez a konzisztencia lehetővé teszi, hogy tudásukat a nyilvános felhőben és a helyszíni környezetekben is alkalmazhatók legyenek.

A megoldás üzembe helyezési útmutatója lehetővé teszi, hogy egy azonos webalkalmazást egy nyilvános és privát felhőbe helyezzen üzembe. A privát felhőben üzemeltetett, nem internetes útválasztású hálózatot is elérheti. A webalkalmazások figyelése a betöltéshez. A forgalom jelentős növekedésével a program a DNS-rekordokat úgy kezeli, hogy átirányítsa a forgalmat a nyilvános felhőbe. Ha a forgalom már nem jelentős, a DNS-rekordok frissülnek, hogy a rendszer visszairányítsa a forgalmat a privát felhőbe.

[többfelhős méretezés ![helyszíni adatmintázattal](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Összetevő | Leírás |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure app Service](/azure/app-service/) lehetővé teszi a webalkalmazások, a REST API-alkalmazások és a Azure functions kiépítését és üzemeltetését. Minden az Ön által választott programozási nyelven, az infrastruktúra kezelése nélkül. |
| | Azure Virtual Network| Az [azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) az Azure-beli magánhálózatok alapvető építőeleme. A VNet lehetővé teszi több Azure-erőforrástípus, például Virtual Machines (VM) használatát az egymással, az internettel és a helyszíni hálózatokkal való biztonságos kommunikációhoz. A megoldás a további hálózatkezelési összetevők használatát is bemutatja:<br>-alkalmazás-és átjáró-alhálózatok<br>-helyszíni helyi hálózati átjáró<br>-egy virtuális hálózati átjáró, amely helyek közötti VPN Gateway-kapcsolatként működik<br>– nyilvános IP-cím<br>-pont – hely típusú VPN-kapcsolat<br>– Azure DNS a DNS-tartományok üzemeltetéséhez és névfeloldás biztosításához |
| | Azure Traffic Manager | Az [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) egy DNS-alapú forgalom terheléselosztó. Lehetővé teszi a különböző adatközpontokban lévő szolgáltatási végpontok felhasználói forgalmának szabályozását. |
| | Azure Application Insights | A [Application Insights](/azure/azure-monitor/app/app-insights-overview) egy bővíthető Application Performance Management szolgáltatás, amellyel a fejlesztők több platformon hozhatnak létre és kezelhetnek alkalmazásokat.|
| | Azure Functions | [Azure functions](/azure/azure-functions/) lehetővé teszi a kód kiszolgáló nélküli környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. |
| | Azure-beli autoskálázás | Az [autoscale](/azure/azure-monitor/platform/autoscale-overview) a Cloud Services, a Virtual Machines és a Web Apps beépített funkciója. A funkció lehetővé teszi, hogy az alkalmazások a lehető leghatékonyabban hajtsák végre a változtatásokat. Az alkalmazások a forgalmi csúcsokra változnak, és a mérőszámok változásakor értesítik, és igény szerint skálázást tesznek lehetővé. |
| Azure Stack hub | IaaS számítás | Az Azure Stack hub lehetővé teszi, hogy ugyanazt az alkalmazás-modellt, önkiszolgáló portált és az Azure által engedélyezett API-kat használja. Az Azure Stack hub IaaS a nyílt forráskódú technológiák széles választékát teszi lehetővé a hibrid felhőalapú környezetek egységes környezetében. A megoldás példája egy Windows Server rendszerű virtuális gépet használ SQL Server, például:.|
| | Azure App Service | Akárcsak az Azure-webalkalmazáshoz, a megoldás a [Azure app Service on Azure stack hub](/azure-stack/operator/azure-stack-app-service-overview) használatával futtatja a webalkalmazást. |
| | Hálózat | Az Azure Stack hub Virtual Network, ugyanúgy működik, mint az Azure Virtual Network. Számos azonos hálózati összetevőt használ, beleértve az egyéni állomásnévket is. 
| Azure DevOps Services | Regisztráció | Gyorsan buildelési, tesztelési és üzembe helyezés a folyamatos integráció beállítása. További információ: [regisztráció, bejelentkezés az Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | [Azure-folyamatokat](/azure/devops/pipelines/agents/agents?view=azure-devops) használhat folyamatos integrációhoz és folyamatos teljesítéshez. Az Azure-folyamatok lehetővé teszik az üzemeltetett Build és Release ügynökök és definíciók kezelését. |
| | Kódtár | Több kódrészletet is kihasználhat a fejlesztési folyamat leegyszerűsítése érdekében. Meglévő kódrészletek használata a GitHub, a bitbucket, a Dropbox, a OneDrive és az Azure Reposban. |

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Méretezhetőség 

Az Azure és a Azure Stack hub egyedi módon alkalmas a mai globálisan elosztott üzleti igények kielégítésére.

**Hibrid felhő a szóváltás nélkül**

A Microsoft a helyszíni eszközök páratlan integrációját kínálja Azure Stack hubhoz és az Azure-hoz egyetlen egységes megoldásban. Ez az integráció kiküszöböli a több ponttal rendelkező megoldások és a felhőalapú szolgáltatók együttes kezelését. A többfelhős méretezéssel az Azure ereje csupán néhány kattintással elérhető. Egyszerűen csatlakoztathatja az Azure Stack hub-t az Azure-hoz a Cloud burst használatával, és az adatai és alkalmazásai az Azure-ban lesznek elérhetők, ha szükséges.

- Nem kell másodlagos DR-helyet felépíteni és karbantartani.
- Időt és pénzt takaríthat meg, ha megszünteti a szalagos biztonsági mentést, és akár 99 éves biztonsági mentési adatokkal is rendelkezik az Azure-ban.
- A felhő gazdaságosságának és rugalmasságának kihasználásához könnyedén áttelepítheti a Hyper-V, a fizikai (előzetes verzió) és a VMware (előzetes verzió) munkaterhelést az Azure-ba.
- Nagy számítási igényű jelentéseket vagy elemzéseket futtathat az Azure-ban lévő helyszíni eszköz többszörözött példányán anélkül, hogy ez hatással lenne az éles munkaterhelésekre.
- A felhőbe, és helyszíni számítási feladatokat futtathat az Azure-ban, és szükség esetén nagyobb számítási sablonokkal. A hibrid lehetőséget biztosít a szükséges teljesítményre, ha szüksége van rá.
- Hozzon létre többrétegű fejlesztési környezeteket az Azure-ban néhány kattintással – akár az élő éles üzemi adatait is replikálhatja a fejlesztési és tesztelési környezetbe, hogy azok a közel valós idejű szinkronizálásban maradjanak.

**Felhőbeli skálázások gazdasága Azure Stack hubhoz**

A felhő kitörésének legfőbb előnye a gazdaságos megtakarítások. A további erőforrásokért csak akkor kell fizetnie, ha igény van ezekre az erőforrásokra. Nincs több ráfordítás a szükségtelen extra kapacitásért, vagy a keresleti csúcsok és ingadozások előrejelzésére van szükség.

**A felhőbe való magas kereslet csökkentése**

A több felhőre kiterjedő skálázás felhasználható a terhelések feldolgozására. A Load elosztása az alapszintű alkalmazások nyilvános felhőbe való áthelyezésével történik, és a helyi erőforrások felszabadításával az üzleti szempontból kritikus fontosságú alkalmazások számára. Egy alkalmazás alkalmazható a privát felhőre, majd a nyilvános felhőre csak akkor lehet szükség, ha az igények kielégítése szükséges.


### <a name="availability"></a>Rendelkezésre állás

A globális üzembe helyezés saját kihívásokkal rendelkezik, mint például a változó kapcsolat és régiónként eltérő kormányzati rendeletek. A fejlesztők csak egy alkalmazást fejleszthet, majd különböző okok miatt üzembe helyezhetik őket.  Telepítse az alkalmazást az Azure nyilvános felhőbe, majd helyileg helyezzen üzembe további példányokat vagy összetevőket. Az Azure használatával kezelheti az összes példány közötti forgalmat.

### <a name="manageability"></a>Kezelhetőség

**Egyetlen, egységes fejlesztési megközelítés**

Az Azure és a Azure Stack hub lehetővé teszi, hogy egységes fejlesztési eszközöket használjon a szervezeten belül. Ez a konzisztencia megkönnyíti a folyamatos integráció és a folyamatos fejlesztés (CI/CD) gyakorlatának megvalósítását. Az Azure-ban vagy Azure Stack hub-ban üzembe helyezett alkalmazások és szolgáltatások felcserélhetők, és zökkenőmentesen futhatnak mindkét helyen.

A hibrid CI/CD-folyamat A következőket nyújtja:
- Hozzon létre egy új Build kód alapján véglegesíti a kódot a tárházba.
- Az újonnan létrehozott kód automatikus üzembe helyezése az Azure-ban felhasználói elfogadási tesztelés céljából.
- A kód sikeres tesztelése után a rendszer automatikusan üzembe helyezi az Azure Stack hub-t.

**Egyetlen, egységes Identitáskezelés megoldás**

Azure Stack hub Azure Active Directory és Active Directory összevonási szolgáltatások (AD FS) (ADFS) is működik. A Azure Stack hub összekapcsolt helyzetekben Azure Active Directory működik. Olyan környezetek esetén, amelyek nem rendelkeznek kapcsolattal, az ADFS-t leválasztott megoldásként használhatja. Az egyszerű szolgáltatások hozzáférést biztosítanak az alkalmazásokhoz, így az erőforrások üzembe helyezése vagy konfigurálása Azure Resource Manager használatával történik. 

### <a name="security"></a>Biztonság

**A megfelelőség és az adatszuverenitás biztosítása**

Azure Stack hub lehetővé teszi, hogy ugyanazt a szolgáltatást több országon is futtassa, mint ha nyilvános felhőt használ. Ugyanazon alkalmazás üzembe helyezése az adatközpontokban minden országban, lehetővé teszi az adatszuverenitási követelmények teljesítését. Ez a funkció biztosítja, hogy a személyes adat az egyes országok határain belül maradjon.

**Azure Stack hub – biztonsági testhelyzet**

A folyamatos és folyamatos karbantartási folyamat nélkül nincs biztonsági helyzet. Emiatt a Microsoft egy olyan előkészítési motorba fektetett be, amely a javítások és a frissítések zökkenőmentesen alkalmazható a teljes infrastruktúrán keresztül.

A Azure Stack hub OEM-partnerekkel való együttműködésnek köszönhetően a Microsoft ugyanazokat a biztonsági helyzeteket terjeszti ki az OEM-specifikus összetevőkre. Például a hardver életciklus-gazdagépe és a rajta futó szoftver. Ez a partnerség biztosítja, hogy Azure Stack hub egységes, stabil biztonsági állapottal rendelkezik a teljes infrastruktúrán belül. Az ügyfelek az alkalmazás számítási feladatait felépíthetik és biztonságossá tehetik.

**Egyszerű szolgáltatásnév használata a PowerShell, a CLI és a Azure Portal segítségével**

Ha erőforrás-hozzáférést szeretne adni egy parancsfájlhoz vagy alkalmazáshoz, állítson be egy identitást az alkalmazáshoz, és hitelesítse az alkalmazást a saját hitelesítő adataival. Ez az identitás ismert szolgáltatásnév, és lehetővé teszi a következőket:

- Rendeljen engedélyeket az alkalmazás identitásához, amely eltér a saját engedélyeitől, és az alkalmazás igényeinek megfelelően korlátozódik.
- Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor. 

Az egyszerű szolgáltatás létrehozásával és a hitelesítő adatok tanúsítványának használatával kapcsolatos további információkért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni? 

- A szervezetem DevOps megközelítést használ, vagy a közeljövőben megtervezhető egy terv.
- CI/CD-gyakorlatot szeretnék megvalósítani az Azure Stack hub implementációjában és a nyilvános felhőben.
- Konszolidálni szeretném a CI/CD-folyamatot a felhőben és a helyszíni környezetekben.
- Szeretnék zökkenőmentesen fejleszteni az alkalmazásokat a felhő vagy a helyszíni szolgáltatások használatával.
- Konzisztens fejlesztői ismereteket szeretnék használni a felhőben és a helyszíni alkalmazásokban.
- Az Azure-t használom, de vannak olyan fejlesztők, akik helyszíni Azure Stack hub-felhőben dolgoznak.
- A helyszíni alkalmazások a szezonális, ciklikus vagy kiszámíthatatlan ingadozások során igénybe veszi a felmerülő igényeket.
- Helyszíni összetevőkkel rendelkezem, és zökkenőmentesen szeretném méretezni a felhőt.
- A felhő méretezhetőségét szeretném, de azt szeretném, hogy az alkalmazásom a lehető legnagyobb mértékben fusson a helyszínen.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- Tekintse meg az [alkalmazások dinamikus méretezését az adatközpontok és a nyilvános felhő között](https://www.youtube.com/watch?v=2lw8zOpJTn0) a minta használatának áttekintéséhez.
- Az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásáért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- Ez a minta a Azure Stack termékcsaládot használja, beleértve az Azure Stack hub-t is. A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa a [többfelhős méretezési (helyszíni adatkezelési) megoldás telepítési útmutatóját](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.