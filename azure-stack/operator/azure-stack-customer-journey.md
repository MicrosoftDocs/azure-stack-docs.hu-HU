---
title: Azure Stack Datacenter integrációs útmutatója | Microsoft Docs
description: Megtudhatja, mire számíthat az adatközpontban Azure Stack sikeres helyszíni üzembe helyezéséhez, a tervezéstől a telepítést követően.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: fdb0f9a34fe40b3d3fef6abf96b9e1a6927b04e6
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909306"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Azure Stack Datacenter integrációs útmutató

Ez a cikk a teljes körű Azure Stack felhasználói élményt ismerteti, amely egy integrált rendszer megvásárlását mutatja be a megoldás-szolgáltató sikeres helyszíni telepítésére. Ezekkel az információkkal könnyedén elvégezheti az utazást, és segítheti az elvárások megadását Azure Stack ügyfélként.

Azure Stack ügyfélként a következő fázisokra kell számítani:

|     |Tervezési fázis|Megrendelés folyamata|Üzembe helyezés előtti|Gyári folyamat|Hardveres kézbesítés|Helyszíni üzembe helyezés|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Folytassa a partnerrel az értékesítés előtti támogatás biztosításához.|Szükség szerint készítse elő a szoftver licencelését és a szerződéseket.|Adja meg a szükséges eszközöket az adatközpont-integrációs követelmények és dokumentáció összegyűjtéséhez az ügyfél számára.|Adja meg a legújabb alapkonfigurációk és az eszközök láncának frissítéseit egy havi ritmusban.|–|A Microsoft támogatási szakemberei segítséget nyújtanak az üzembe helyezési problémák megoldásában.|
|**Partner**|Az ügyfélre vonatkozó követelmények alapján ajánljuk a megoldás beállításait.<br><br>Szükség esetén javasoljon megvalósíthatósági igazolást (POC).<br><br>Üzleti kapcsolat létrehozása.<br><br>Döntse el a támogatási szintet.|Készítse elő a szükséges szerződéseket az ügyféllel.<br><br>Hozzon létre egy ügyfél-beszerzési rendelést.<br><br>Döntse el a kézbesítési ütemtervet.<br><br>Ha szükséges, az ügyfél összekapcsolását a Microsofttal.|Adja meg az ügyfél számára a szükséges képzést az összes üzembe helyezési előfeltétel és adatközpont-integrációs lehetőség megismerése érdekében.<br><br>Segítheti az ügyfelet az összegyűjtött adatok érvényesítésében a teljesség és pontosság biztosítása érdekében.|Alkalmazza az utolsó érvényesített alapterv-buildet.<br><br>Alkalmazza a szükséges Microsoft központi telepítési eszközkészletet.|Hardver szállítása az ügyfél webhelyére.|Helyszíni mérnök által kezelt üzembe helyezés.<br><br>Állvány és verem.<br><br>Hardveres életciklus-gazdagép (HLH) telepítése.<br><br>Azure Stack üzemelő példány.<br><br>Kikapcsolás az ügyfélnek.|
|**Ügyfél**|Ismertesse a felhasználási példákat, és adja meg a követelményeket.|Határozza meg, hogy milyen számlázási modellt kíván használni, ellenőrizni és jóváhagyni a szerződéseket.|Fejezze be a [telepítési munkalapot](azure-stack-deployment-worksheet.md), és győződjön meg arról, hogy az összes központi telepítés előfeltétele teljesül, és készen áll a telepítésre|–|Készítse elő az adatközpontot az összes szükséges energiaellátási és hűtési, határ-és egyéb szükséges adatközpont-integrációs követelmény biztosításával.|Az üzembe helyezés során elérhetőnek kell lennie az előfizetés hitelesítő adatainak és támogatásának biztosításához, ha kérdése van a megadott adatokkal kapcsolatban.|
| | | | | | | |


## <a name="planning-phase"></a>Tervezési fázis
A tervezési fázis az, amikor a Microsoft vagy a Azure Stack-megoldás partnere együttműködik Önnel, hogy kiértékelje és megértse az igényeinek megfelelő megoldást, ha a Azure Stack a legmegfelelőbb megoldás:

A következő kérdések segítenek eldönteni:

-   Azure Stack a megfelelő megoldás a szervezet számára?

-   Milyen méretű megoldásra lesz szüksége?

-   Milyen típusú számlázási és licencelési modellt fog működni a szervezete számára?

-   Mik a szükséges energiagazdálkodási és hűtési követelmények?

Annak biztosítása érdekében, hogy a hardveres megoldás megfeleljen az igényeinek, a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) az elővételi tervezés elősegítése érdekében a Azure stack hardveres megoldás megfelelő kapacitásának és konfigurációjának meghatározására szolgál.

A táblázat *nem* helyettesíti a saját igényeihez legjobban illő hardveres megoldások kiértékelését és elemzését. Azure Stack központi telepítésének tervezésekor tekintse át a Azure Stack integrált rendszerek [általános adatközpont-integrációs szempontjait](azure-stack-datacenter-integration.md) is.

## <a name="order-process-phase"></a>Rendelési folyamat fázisa
Ebben a szakaszban a megvalósíthatósági kérdésekkel kapcsolatos számos kérdést megválaszoltak. Most, hogy készen áll a Azure Stack megvásárlására, és az összes szükséges szerződés és beszerzési megrendelés aláírása után meg kell adnia az integrációs követelmények adatait a megoldás szolgáltatójának.

## <a name="pre-deployment-phase"></a>Üzembe helyezés előtti fázis
Ebben a fázisban el kell döntenie, hogyan szeretné integrálni Azure Stack az adatközpontba. Ennek a folyamatnak a megkönnyítése érdekében a Microsoft összefoglalja a követelményekkel kapcsolatos sablont, amely segítséget nyújt a szükséges információk összegyűjtéséhez, hogy a környezetén belül integrált rendszer-telepítést tervezzen. Ez a követelmény-sablon a megoldás-szolgáltatókkal együttműködve készült.

Az [általános adatközpont-integrációs megfontolások](azure-stack-datacenter-integration.md) című cikk olyan információt tartalmaz, amely segítséget nyújt a sablon végrehajtásához, amelyet a központi telepítési munkalapnak nevezünk.

> [!IMPORTANT]
> Ebben a szakaszban fontos, hogy a megoldás megrendelése előtt minden előfeltételi információt megvizsgáljon, és döntést hozzon. Vegye figyelembe, hogy ez a lépés időigényes, és a szervezeten belül több tudományágból történő koordinációt és adatgyűjtést igényel. A helytelen vagy hiányos információk már nem üzemelő telepítést eredményezhetnek. 

Az üzembe helyezés előtti fázisban a következő elemeket kell eldöntenie:

- **Azure stack a kapcsolatok modelljét és az identitás-szolgáltatót**. Dönthet úgy, hogy Azure Stack [az internethez (és az Azure-hoz) csatlakozik, vagy le van választva](azure-stack-connection-models.md). Ahhoz, hogy a lehető leghatékonyabban használhassa a Azure Stackt, beleértve a hibrid forgatókönyveket is, az Azure-hoz való csatlakozást szeretné telepíteni. Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory (Azure AD) kiválasztása egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. **Az identitás-szolgáltatót később nem módosíthatja a teljes rendszer újbóli üzembe helyezése nélkül**.

- **Licencelési modell**. A licencelési modell lehetőségei közül választhat attól függően, hogy milyen típusú központi telepítéssel rendelkezik. Az identitás-szolgáltató választása nem a bérlői virtuális gépekre, illetve az általuk használt identitásrendszer és fiókok használatára van hatással.
    - A [leválasztott központi telepítésben](azure-stack-disconnected-deployment.md) lévő ügyfeleknek csak egy lehetősége van: a kapacitás alapú számlázás.

    - A csatlakoztatott üzemelő [példányban](azure-stack-connected-deployment.md) lévő ügyfelek a kapacitás alapú számlázás és a használaton kívüli fizetési lehetőség közül választhatnak. A Capacity-alapú számlázáshoz egy Nagyvállalati Szerződés (EA) Azure-előfizetés szükséges a regisztrációhoz. Ez a regisztrációhoz szükséges, amely az Azure Marketplace-en az Azure-előfizetésen keresztül elérhető elemek rendelkezésre állását biztosítja.

- **Hálózati integráció**. A [hálózati integráció](azure-stack-network.md) elengedhetetlen a Azure stack rendszerek üzembe helyezéséhez, működtetéséhez és felügyeletéhez. Számos szempontot figyelembe kell venni, hogy a Azure Stack megoldás rugalmas, és a működésének támogatásához egy magasan elérhető fizikai infrastruktúrát biztosít.

- **Tűzfal-integráció**. Ajánlott [tűzfalat használni](azure-stack-firewall.md) a Azure stack biztonságossá tételéhez. A tűzfalak segítenek megakadályozni a DDOS-támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Azt azonban érdemes megjegyezni, hogy az Azure Storage-szolgáltatások számára az átviteli sebesség szűk keresztmetszete lehet.


- A **tanúsítványokra vonatkozó követelmények**. Fontos, hogy az összes [szükséges tanúsítvány](azure-stack-pki-certs.md) elérhető legyen, *mielőtt* egy helyszíni mérnök megérkezik az adatközpontba üzembe helyezéshez.

Miután az összes előfeltételként szükséges információt összegyűjtötte az üzembe helyezési munkalapon, a megoldás szolgáltatója a begyűjtött adatok alapján elindítja a gyári folyamatot, hogy a Azure Stack sikeres integrációja legyen az adatközpontba.

## <a name="hardware-delivery-phase"></a>Hardveres kézbesítési fázis
A megoldás szolgáltatója Önnel együtt fog működni az ütemezéskor, amikor a megoldás megérkezik a létesítménybe. A beérkezés és a bevezetésük után időt kell ütemeznie a megoldás szolgáltatójának, hogy a Azure Stack központi telepítésének elvégzéséhez egy mérnök kerüljön a helyszínen.

**Rendkívül fontos** , hogy az előfeltételként szükséges összes érték zárolva legyen és elérhető legyen, *mielőtt a helyszíni mérnök megérkezik a megoldás üzembe helyezéséhez*.

-   Minden tanúsítványt meg kell vásárolni és üzemkész állapotban kell lennie.

-   A régió nevét meg kell határozni.

-   Minden hálózati integrációs paraméter véglegesítve van, és egyezik azzal, amit a megoldás-szolgáltatójánál megosztott.

> [!TIP]
> Ha bármelyik információ módosult, ügyeljen arra, hogy a tényleges telepítés megkezdése előtt tájékoztassa a változást a megoldás szolgáltatójával.

## <a name="onsite-deployment-phase"></a>Helyszíni telepítési fázis
Azure Stack üzembe helyezéséhez a hardveres megoldás szolgáltatójától származó helyszíni mérnöknek jelen kell lennie az üzembe helyezés elindításához. A sikeres telepítés érdekében győződjön meg arról, hogy a központi telepítés munkalapon megadott összes információ nem módosult.

A következő ellenőrzéseket kell elvárnia a helyszíni mérnöktől az üzembe helyezési élményben:

- Ellenőrizze az összes kábelezést és a szegélyek kapcsolatát, és győződjön meg arról, hogy a megoldás megfelelően össze van szerelve, és megfelel az igényeinek.
- Konfigurálja a megoldás HLH (hardver életciklus-gazdagép), ha van ilyen.
- Győződjön meg arról, hogy az összes BMC, BIOS és hálózati beállítás helyes.
- Győződjön meg arról, hogy az összes összetevő belső vezérlőprogramja a megoldás legújabb jóváhagyott verziója.
- Indítsa el a telepítést.

> [!NOTE]
> A helyszíni mérnök üzembe helyezési eljárása egy munkahetet is igénybe vehet.

## <a name="post-deployment-phase"></a>Üzembe helyezés utáni fázis
A partnernek az integráció utáni fázisban több lépést kell végrehajtania, mielőtt a megoldás bekerül az ügyfélbe. Ebben a fázisban az érvényesítés fontos annak biztosítása érdekében, hogy a rendszer üzembe helyezése és megfelelő végrehajtása sikeres legyen. 

Az OEM-partner által végrehajtandó műveletek a következők:

- [Futtassa a test-azurestack](azure-stack-diagnostic-test.md).

- [Regisztráció az Azure](azure-stack-registration.md)-ban.

- [Piactéri hírszolgáltatás](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items).

- A Backup kapcsoló konfigurációs és HLH konfigurációs fájljai.

- Távolítsa el a DVM.

- Készítse elő az ügyfél összegzését az üzembe helyezéshez.

- A [frissítések ellenőrzésével győződjön meg arról, hogy a megoldás szoftvere frissítve lett a legújabb verzióra](./azure-stack-updates.md).

A telepítés típusától függően több lépés szükséges vagy választható.

- Ha a központi telepítés [AD FS](azure-stack-integrate-identity.md)használatával fejeződött be, akkor az Azure stack stampnek integrálva kell lennie az ügyfél saját ad FSával.

  > [!NOTE]
  > Ez a lépés az ügyfél feladata, bár a partner dönthet úgy, hogy ezt a szolgáltatást kínálja.

- Integráció egy meglévő figyelési rendszerrel az adott partnertől.

  -   [System Center Operations Manager integráció](azure-stack-integrate-monitor.md) a flotta-felügyeleti képességeket is támogatja.

  -   [Nagios-integráció](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="overall-timeline"></a>Teljes idősor

![Azure Stack helyszíni üzembe helyezésének teljes idővonala](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Támogatás
A Azure Stack lehetővé teszi az Azure-konzisztens, integrált támogatási élményt, amely magában foglalja a teljes rendszer életciklusát. Az Azure Stack integrált rendszerek teljes körű támogatásához az ügyfeleknek két támogatási szerződésre van szükségük: egyet a Microsofttal (vagy a felhőalapú megoldás szolgáltatójával) az Azure-szolgáltatások támogatásához, egyet pedig a hardver szolgáltatója a rendszertámogatáshoz. Az integrált támogatási felület összehangolt eszkalációt és megoldást biztosít, így az ügyfelek egy egységes támogatási élményt kapnak, függetlenül attól, hogy először hívják meg őket. Az olyan ügyfelek számára, akik már rendelkeznek Premier, Azure-standard/közvetlen vagy partneri támogatással a Microsofttal, Azure Stack szoftveres támogatást is tartalmaz.

Az integrált támogatási élmény egy Case Exchange-mechanizmust használ a támogatási esetek és a Microsoft és a hardverprofil közötti, kétirányú átvitelhez. Microsoft Azure Stack követni fogja a [modern életciklus-szabályzatot](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>További lépések
További információ az [általános adatközpont integrációs szempontjairól](azure-stack-datacenter-integration.md).
