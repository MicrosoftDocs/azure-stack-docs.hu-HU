---
title: Többfelhős méretezés (helyszíni adattípusok) az Azure Stack központban
description: Megtudhatja, hogyan hozhat létre olyan méretezhető, többfelhős alkalmazást, amely helyszíni információkat használ az Azure-ban és Azure Stack hub-ban.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: edbb608fbf8e5288f29572bfe4cca98ffb3cb8fc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812390"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Több felhőre kiterjedő méretezés (helyszíni adattípusok)

Ismerje meg, hogyan hozhat létre olyan hibrid alkalmazást, amely az Azure-t és Azure Stack hubot is felöleli. Ez a minta azt is bemutatja, hogyan használható egyetlen helyszíni adatforrás a megfelelőséghez.

## <a name="context-and-problem"></a>Kontextus és probléma

Számos szervezet nagy mennyiségű bizalmas vásárlói adatot gyűjt és tárol. Gyakran meggátolják, hogy bizalmas adatokat tároljanak a nyilvános felhőben a vállalati rendeletek vagy a közigazgatási szabályzatok miatt. Ezek a szervezetek is szeretnék kihasználni a nyilvános felhő méretezhetőségét. A nyilvános felhő képes kezelni az adatforgalom szezonális csúcsait, így az ügyfeleknek pontosan a szükséges hardverért kell fizetniük, amikor szükségük van rá.

## <a name="solution"></a>Megoldás

A megoldás kihasználja a privát felhő megfelelőségi előnyeit, és kombinálja őket a nyilvános felhő méretezhetőségével. Az Azure és a Azure Stack hub Hybrid Cloud egységes felhasználói élményt nyújt a fejlesztőknek. Ez a konzisztencia lehetővé teszi, hogy tudásukat a nyilvános felhőben és a helyszíni környezetekben is alkalmazhatók legyenek.

A megoldás üzembe helyezési útmutatója lehetővé teszi, hogy egy azonos webalkalmazást egy nyilvános és privát felhőbe helyezzen üzembe. A privát felhőben üzemeltetett, nem internetes útválasztású hálózatot is elérheti. A webalkalmazások figyelése betöltésre történik. A forgalom jelentős növekedésével a program a DNS-rekordokat úgy kezeli, hogy átirányítsa a forgalmat a nyilvános felhőbe. Ha a forgalom már nem jelentős, a DNS-rekordok frissülnek, hogy a rendszer visszairányítsa a forgalmat a privát felhőbe.

[![Többfelhős méretezés helyszíni adatmintázattal](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Összetevő | Leírás |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure app Service](/azure/app-service/) lehetővé teszi a webalkalmazások, a REST API-alkalmazások és a Azure functions kiépítését és üzemeltetését. Minden az Ön által választott programozási nyelven, az infrastruktúra kezelése nélkül. |
| | Azure Virtual Network| Az [azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) az Azure-beli magánhálózatok alapvető építőeleme. A VNet lehetővé teszi több Azure-erőforrástípus, például a virtuális gépek (VM) számára, hogy biztonságosan kommunikáljanak egymással, az internettel és a helyszíni hálózatokkal. A megoldás a további hálózatkezelési összetevők használatát is bemutatja:<br>-alkalmazás-és átjáró-alhálózatok.<br>– helyi hálózati átjáró.<br>-egy virtuális hálózati átjáró, amely helyek közötti VPN Gateway-kapcsolatként működik.<br>– nyilvános IP-cím.<br>-pont – hely típusú VPN-kapcsolat.<br>– Azure DNS a DNS-tartományok üzemeltetéséhez és a névfeloldás biztosításához. |
| | Azure Traffic Manager | Az [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) egy DNS-alapú forgalom terheléselosztó. Lehetővé teszi a különböző adatközpontokban lévő szolgáltatási végpontok felhasználói forgalmának szabályozását. |
| | Azure Application Insights | A [Application Insights](/azure/azure-monitor/app/app-insights-overview) egy bővíthető Application Performance Management szolgáltatás a webfejlesztőknek több platformon futó alkalmazások létrehozásához és kezeléséhez.|
| | Azure Functions | [Azure functions](/azure/azure-functions/) lehetővé teszi a kód kiszolgáló nélküli környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. |
| | Automatikus méretezés az Azure-ban | Az [autoscale](/azure/azure-monitor/platform/autoscale-overview) Cloud Services, virtuális gépek és webalkalmazások beépített funkciója. A funkció lehetővé teszi, hogy az alkalmazások a lehető leghatékonyabban hajtsák végre a változtatásokat. Az alkalmazások a forgalmi csúcsokra változnak, és szükség esetén értesítik a metrikák változásáról és méretezéséről. |
| Azure Stack hub | IaaS számítás | Azure Stack hub lehetővé teszi, hogy ugyanazt az alkalmazás-modellt, önkiszolgáló portált és az Azure által engedélyezett API-kat használja. Azure Stack hub IaaS széles körű, nyílt forráskódú technológiákat tesz lehetővé a hibrid felhőalapú környezetekben. A megoldás példája egy Windows Server rendszerű virtuális gépet használ SQL Server, például:.|
| | Azure App Service | Akárcsak az Azure-webalkalmazáshoz, a megoldás a [Azure app Service on Azure stack hub](/azure-stack/operator/azure-stack-app-service-overview) használatával futtatja a webalkalmazást. |
| | Hálózat | Az Azure Stack hub Virtual Network ugyanúgy működik, mint az Azure Virtual Network. Számos azonos hálózati összetevőt használ, beleértve az egyéni állomásnévket is.
| Azure DevOps Services | Regisztráció | Gyorsan állíthatja be a létrehozás, a tesztelés és az üzembe helyezés folyamatos integrációját. További információ: [regisztráció, bejelentkezés az Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | [Azure-folyamatokat](/azure/devops/pipelines/agents/agents?view=azure-devops) használhat folyamatos integrációhoz és folyamatos teljesítéshez. Az Azure-folyamatok lehetővé teszik az üzemeltetett Build és Release ügynökök és definíciók kezelését. |
| | Kódtár | Több kódrészletet is kihasználhat a fejlesztési folyamat leegyszerűsítése érdekében. Meglévő kódrészletek használata a GitHub, a bitbucket, a Dropbox, a OneDrive és az Azure Reposban. |

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Méretezhetőség

Az Azure és a Azure Stack hub egyedi módon alkalmas a mai globálisan elosztott üzleti igények kielégítésére.

#### <a name="hybrid-cloud-without-the-hassle"></a>Hibrid felhő a szóváltás nélkül

A Microsoft a helyszíni eszközök páratlan integrációját kínálja Azure Stack hubhoz és az Azure-hoz egyetlen egységes megoldásban. Ez az integráció kiküszöböli a több ponttal rendelkező megoldások és a felhőalapú szolgáltatók együttes kezelését. A többfelhős méretezéssel az Azure ereje csupán néhány kattintással elérhető. Egyszerűen csatlakoztassa a Azure Stack hub-t az Azure-hoz a Cloud burst használatával, az adatai és alkalmazásai pedig az Azure-ban lesznek elérhetők, ha szükséges.

- Nem kell másodlagos DR-helyet felépíteni és karbantartani.
- Időt és pénzt takaríthat meg azáltal, hogy megszünteti a szalagos biztonsági mentést, és akár 99 éves biztonsági mentési adatokkal is rendelkezik az Azure-ban.
- A felhő gazdaságosságának és rugalmasságának kihasználásához könnyedén áttelepítheti a Hyper-V, a fizikai (előzetes verzió) és a VMware (előzetes verzió) munkaterhelést az Azure-ba.
- Nagy számítási igényű jelentéseket vagy elemzéseket futtathat az Azure-beli helyszíni eszköz replikált példányán anélkül, hogy ez hatással lenne az éles munkaterhelésekre.
- A felhőbe, és helyszíni számítási feladatokat futtathat az Azure-ban, és szükség esetén nagyobb számítási sablonokkal. A hibrid lehetőséget biztosít a szükséges teljesítményre, ha szüksége van rá.
- Hozzon létre többrétegű fejlesztési környezeteket az Azure-ban néhány kattintással – akár az élő éles üzemi adatait is replikálhatja a fejlesztési és tesztelési környezetbe, hogy azok a közel valós idejű szinkronizálásban maradjanak.

#### <a name="economy-of-cross-cloud-scaling-with-azure-stack-hub"></a>Felhőbeli skálázások gazdasága Azure Stack hubhoz

A felhő kitörésének legfőbb előnye a gazdaságos megtakarítások. A további erőforrásokért csak akkor kell fizetnie, ha igény van ezekre az erőforrásokra. Nincs több ráfordítás a szükségtelen extra kapacitáshoz, vagy a keresleti csúcsok és ingadozások előrejelzésére van szükség.

#### <a name="reduce-high-demand-loads-into-the-cloud"></a>A felhőbe való magas kereslet csökkentése

A több felhőre kiterjedő skálázás felhasználható a terhelések feldolgozására. A Load elosztása az alapszintű alkalmazások nyilvános felhőbe való áthelyezésével történik, és a helyi erőforrásokat felszabadítja az üzleti szempontból kritikus fontosságú alkalmazások számára. Az alkalmazás alkalmazható a privát felhőre, majd a nyilvános felhőre csak akkor lehet szükség, ha az igények kielégítéséhez szükséges.

### <a name="availability"></a>Rendelkezésre állás

A globális üzembe helyezés saját kihívásokkal, például változó kapcsolattal és régiónként eltérő kormányzati szabályozásokkal rendelkezik. A fejlesztők csak egy alkalmazást fejleszthet, majd különböző okok miatt üzembe helyezhetik őket. Telepítse az alkalmazást az Azure nyilvános felhőbe, majd helyileg helyezzen üzembe további példányokat vagy összetevőket. Az Azure használatával kezelheti az összes példány közötti forgalmat.

### <a name="manageability"></a>Kezelhetőség

#### <a name="a-single-consistent-development-approach"></a>Egyetlen, egységes fejlesztési megközelítés

Az Azure és Azure Stack hub lehetővé teszi, hogy a szervezeten belül konzisztens fejlesztési eszközöket használjon. Ez a konzisztencia megkönnyíti a folyamatos integráció és a folyamatos fejlesztés (CI/CD) gyakorlatának megvalósítását. Az Azure-ban vagy Azure Stack hub-ban üzembe helyezett számos alkalmazás és szolgáltatás felcserélhető, és zökkenőmentesen futhat mindkét helyen.

A hibrid CI/CD-folyamat A következőket nyújtja:

- Hozzon létre egy új Build kód alapján véglegesíti a kódot a tárházba.
- Az újonnan létrehozott kód automatikus üzembe helyezése az Azure-ban felhasználói elfogadási tesztelés céljából.
- A kód sikeres tesztelése után a rendszer automatikusan üzembe helyezi az Azure Stack hub-t.

### <a name="a-single-consistent-identity-management-solution"></a>Egyetlen, egységes Identitáskezelés megoldás

Azure Stack hub a Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (ADFS) szolgáltatással is működik. Az Azure Stack hub az Azure AD-vel összekapcsolt helyzetekben működik. Olyan környezetek esetén, amelyek nem rendelkeznek kapcsolattal, az ADFS-t leválasztott megoldásként használhatja. Az egyszerű szolgáltatások hozzáférést biztosítanak az alkalmazásokhoz, így az erőforrások üzembe helyezése vagy konfigurálása Azure Resource Manager használatával történik.

### <a name="security"></a>Biztonság

#### <a name="ensure-compliance-and-data-sovereignty"></a>A megfelelőség és az adatszuverenitás biztosítása

Azure Stack hub lehetővé teszi, hogy ugyanazt a szolgáltatást több országban is futtatja, mint ha nyilvános felhőt használ. Ugyanazon alkalmazás üzembe helyezése az adatközpontokban minden országban lehetővé teszi az adatszuverenitási követelmények teljesítését. Ez a funkció biztosítja, hogy a személyes adat az egyes országok határain belül maradjon.

#### <a name="azure-stack-hub---security-posture"></a>Azure Stack hub – biztonsági testhelyzet

A folyamatos és folyamatos karbantartási folyamat nélkül nincs biztonsági helyzet. Emiatt a Microsoft egy olyan előkészítési motorba fektetett be, amely a javítások és a frissítések zökkenőmentesen alkalmazható a teljes infrastruktúrán keresztül.

A Azure Stack hub OEM-partnerekkel való partneri együttműködésnek köszönhetően a Microsoft ugyanazokat a biztonsági helyzeteket terjeszti ki az OEM-specifikus összetevőkre, például a hardveres életciklus-gazdagépre és a rajta futó szoftverekre. Ez a partnerség biztosítja, hogy Azure Stack hub egységes, stabil biztonsági állapottal rendelkezik a teljes infrastruktúrán belül. Az ügyfelek az alkalmazás számítási feladatait felépíthetik és biztonságossá tehetik.

#### <a name="use-of-service-principals-via-powershell-cli-and-azure-portal"></a>Egyszerű szolgáltatásnév használata a PowerShell, a CLI és a Azure Portal segítségével

Ha erőforrás-hozzáférést szeretne adni egy parancsfájlhoz vagy alkalmazáshoz, állítson be egy identitást az alkalmazáshoz, és hitelesítse az alkalmazást a saját hitelesítő adataival. Ez az identitás egyszerű szolgáltatásnév, és a következőket teszi lehetővé:

- Rendeljen engedélyeket az alkalmazás identitásához, amely eltér a saját engedélyeitől, és az alkalmazás igényeinek megfelelően korlátozódik.
- Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor.

További információ az egyszerű szolgáltatás létrehozásáról és a hitelesítő adatok tanúsítványának használatáról: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

- A szervezetem DevOps megközelítést használ, vagy a közeljövőben megtervezhető egy terv.
- CI/CD-gyakorlatot szeretnék megvalósítani az Azure Stack hub implementációjában és a nyilvános felhőben.
- Konszolidálni szeretném a CI/CD-folyamatot a felhőben és a helyszíni környezetekben.
- Szeretnék zökkenőmentesen fejleszteni az alkalmazásokat a felhő vagy a helyszíni szolgáltatások használatával.
- Konzisztens fejlesztői ismereteket szeretnék használni a felhőben és a helyszíni alkalmazásokban.
- Azure-t használok, de vannak olyan fejlesztők, akik helyszíni Azure Stack hub-felhőben dolgoznak.
- A helyszíni alkalmazások a szezonális, ciklikus vagy kiszámíthatatlan ingadozások során igénybe veszi a felmerülő igényeket.
- Helyszíni összetevőkkel rendelkezem, és zökkenőmentesen szeretném méretezni a felhőt.
- A felhő méretezhetőségét szeretném, de azt szeretném, hogy az alkalmazás a lehető legnagyobb mértékben fusson a helyszínen.

## <a name="next-steps"></a>További lépések

További információ a cikkben bemutatott témakörökről:

- Tekintse meg az [alkalmazások dinamikus méretezését az adatközpontok és a nyilvános felhő között](https://www.youtube.com/watch?v=2lw8zOpJTn0) a minta használatának áttekintéséhez.
- További információ az ajánlott eljárásokról és az esetlegesen felmerülő további kérdések megválaszolásáról: a [hibrid alkalmazások kialakításával kapcsolatos szempontok](overview-app-design-considerations.md) .
- Ez a minta a Azure Stack termékcsaládot használja, beleértve az Azure Stack hub-t is. A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack) .

Ha készen áll a megoldás tesztelésére, folytassa a [többfelhős méretezési (helyszíni adatkezelési) megoldás telepítési útmutatóját](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.
