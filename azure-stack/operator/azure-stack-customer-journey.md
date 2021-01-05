---
title: Azure Stack hub Datacenter integrációs útmutató
description: Ismerje meg, mire számíthat az Azure Stack hub sikeres helyszíni üzembe helyezéséhez az adatközpontban, a tervezéstől a telepítést követően.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 307a24fdf42ce5e007f74a9cf0072b5124d247b2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871429"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Azure Stack hub Datacenter integrációs útmutató

Ez a cikk a Azure Stack hub Datacenter integrációjának teljes körű folyamatát ismerteti az üzembe helyezés utáni támogatás megvásárlása után. Az integráció egy együttműködési projekt az ügyfél, a megoldás szolgáltatója és a Microsoft között. A következő lapokon megtekintheti a projekt egyes tagjainak konkrét lépéseit, és a következő szakaszokban láthatja a projekt idővonalának különböző fázisainak összegzését. 

# <a name="customer"></a>[Ügyfél](#tab/customer)

1. Használati esetek és követelmények leírása
1. A számlázási modell meghatározása
1. Szerződések áttekintése és jóváhagyása
1. Az [üzembe helyezési munkalap](azure-stack-deployment-worksheet.md) befejezése
1. Győződjön meg arról, hogy teljesülnek az üzembe helyezés előfeltételei
1. Az adatközpont előkészítése 
1. Előfizetési adatok megadása az üzembe helyezés során
1. A megadott adattal kapcsolatos kérdések megoldása

# <a name="partner"></a>[Partner](#tab/partner)

1. Az ügyfélre vonatkozó követelmények alapján javasolt megoldási lehetőségek
1. Javaslat a koncepció igazolására (POC) 
1. A támogatási szint eldöntése
1. Szerződések előkészítése az ügyféllel
1. Ügyfél-beszerzési megrendelés létrehozása
1. Kézbesítési ütemterv kiválasztása
1. Ügyfél összekötése a Microsofttal 
1. Ügyfél betanítása üzembe helyezéskor 
1. Az összegyűjtött adatok érvényesítésének segítése az ügyfélen
1. Az alapkonfiguráció-Build és a Microsoft Deployment Toolkit telepítése és ellenőrzése
1. Hardver szállítása az ügyfél webhelyére
1. Helyszíni mérnök megadása
1. Állvány és verem
1. Hardveres életciklus-gazdagép (HLH) üzembe helyezése 
1. Azure Stack hub üzembe helyezése
1. Kikapcsolás az ügyfélnek

# <a name="microsoft"></a>[Microsoft](#tab/micro)

1. Partner használata az értékesítés előtti támogatáshoz
2. Szoftverlicencelési és szerződések előkészítése
3. Eszközöket biztosít az adatközpont-integráció követelményeinek gyűjtéséhez
4. Havi alapkonfiguráció-buildek és az eszközök láncának frissítéseinek megadása
5. A Microsoft támogatási szakemberei bármilyen üzembe helyezési problémával segítenek

---

## <a name="planning"></a>Tervezés
A Microsoft vagy egy Azure Stack hub Solution partner segít kiértékelni a célokat. Segítenek a következő kérdések eldöntésében:

-   Azure Stack hub a megfelelő megoldás a szervezet számára?
-   Milyen típusú számlázási és licencelési modellt fog működni a szervezete számára?
-   Milyen méretű megoldásra lesz szüksége?
-   Mik a szükséges energiagazdálkodási és hűtési követelmények?

Az [Azure stack Hub Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) segítségével vizsgálhatja és elemezheti az igényeinek legmegfelelőbb hardver-kapacitást és konfigurációt. 

## <a name="ordering"></a>Rendezés
A szervezet vállalja az Azure Stack hub megvásárlását, aláírja a szerződéseket és a vásárlási rendeléseket, és biztosítja az integrációs követelmények adatait a megoldás szolgáltatójának.

## <a name="pre-deployment"></a>Központi telepítés előtti
Ön dönti el, hogyan integrálhatja Azure Stack hub-t az adatközpontba. A Microsoft a megoldás-szolgáltatókkal együttműködve közzétesz egy [üzembe helyezési munkalapot](azure-stack-deployment-worksheet.md) a szükséges információk összegyűjtésének elősegítése érdekében.
Az [általános adatközpont-integrációs megfontolások](azure-stack-datacenter-integration.md) című cikk olyan információt tartalmaz, amely segítséget nyújt a sablon végrehajtásához, amelyet a központi telepítési munkalapnak nevezünk.

> [!IMPORTANT]
> A rendszer a megoldás megrendelése előtt minden előfeltételt megvizsgál, hogy elkerülje az üzembe helyezés késését. Az Előfeltételek ellenőrzése időt vehet igénybe, és megkövetelheti a szervezeten belüli különböző részlegek koordinálását és az adatok gyűjtését. 

A következő elemek közül választhat:

- **Azure stack hub-kapcsolatok modelljének és identitás-szolgáltatójának**. Dönthet úgy, hogy Azure Stack hubot [az internethez (és az Azure-hoz) csatlakozik, vagy le van választva](azure-stack-connection-models.md). A Azure Stack hub előnyeinek kihasználása érdekében, beleértve a hibrid forgatókönyveket is, az Azure-hoz való csatlakozást szeretné telepíteni. Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory (Azure AD) kiválasztása egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. **Az identitás-szolgáltatót később nem módosíthatja a teljes rendszer újbóli üzembe helyezése nélkül**.

- **Licencelési modell**. A licencelési modell lehetőségei közül választhat attól függően, hogy milyen típusú központi telepítéssel rendelkezik. Az identitás-szolgáltató választása nem a bérlői virtuális gépekre, illetve az általuk használt identitásrendszer és fiókok használatára van hatással.
    - A [leválasztott központi telepítésben](azure-stack-disconnected-deployment.md) lévő ügyfeleknek csak egy lehetősége van: a kapacitás alapú számlázás.

    - A csatlakoztatott üzemelő [példányban](azure-stack-connected-deployment.md) lévő ügyfelek a kapacitás alapú számlázás és a használaton kívüli fizetési lehetőség közül választhatnak. A Capacity-alapú számlázáshoz egy Nagyvállalati Szerződés (EA) Azure-előfizetés szükséges a regisztrációhoz. Ez a regisztrációhoz szükséges, amely az Azure Marketplace-en az Azure-előfizetésen keresztül elérhető elemek rendelkezésre állását biztosítja.

- **Hálózati integráció**. A [hálózati integráció](azure-stack-network.md) elengedhetetlen a Azure stack hub rendszerek üzembe helyezéséhez, működtetéséhez és felügyeletéhez. Számos szempontot figyelembe kell venni, hogy az Azure Stack hub-megoldás rugalmas, és a működésének támogatásához egy magasan elérhető fizikai infrastruktúrát biztosít.

- **Tűzfal-integráció**. Javasoljuk, hogy a Azure Stack hub biztonságossá tétele érdekében [Használjon tűzfalat](azure-stack-firewall.md) . A tűzfalak segítenek megakadályozni a DDOS-támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Azt azonban érdemes megjegyezni, hogy az Azure Storage-szolgáltatások számára az átviteli sebesség szűk keresztmetszete lehet.

- A **tanúsítványokra vonatkozó követelmények**. Fontos, hogy az összes [szükséges tanúsítvány](azure-stack-pki-certs.md) elérhető legyen, *mielőtt* egy helyszíni mérnök megérkezik az adatközpontba üzembe helyezéshez.

Miután az összes előfeltételként szükséges információt összegyűjtötte az üzembe helyezési munkalapon, a megoldás szolgáltatója a begyűjtött adatok alapján elindítja a gyári folyamatot, hogy az Azure Stack hub sikeres integrációja legyen az adatközpontba.

## <a name="hardware-delivery"></a>Hardveres kézbesítés 
A megoldás szolgáltatója Önnel együtt fog működni az ütemezéskor, amikor a megoldás megérkezik a létesítménybe. A beérkezés és a bevezetésük után időt kell ütemeznie a megoldás szolgáltatójának, hogy az Azure Stack hub üzembe helyezéséhez egy mérnök kerüljön a helyszínen.

**Rendkívül fontos** , hogy az előfeltételként szükséges összes érték zárolva legyen és elérhető legyen, *mielőtt a helyszíni mérnök megérkezik a megoldás üzembe helyezéséhez*.

-   Minden tanúsítványt meg kell vásárolni és üzemkész állapotban kell lennie.

-   A régió nevét meg kell határozni.

-   Minden hálózati integrációs paraméter véglegesítve van, és egyezik azzal, amit a megoldás-szolgáltatójánál megosztott.

> [!TIP]
> Ha bármelyik információ módosult, ügyeljen arra, hogy a tényleges telepítés megkezdése előtt tájékoztassa a változást a megoldás szolgáltatójával.

## <a name="onsite-deployment"></a>Helyszíni üzembe helyezés 
Azure Stack hub üzembe helyezéséhez a hardveres megoldás szolgáltatójától származó helyszíni mérnöknek jelen kell lennie az üzembe helyezés elindításához. A sikeres telepítés érdekében győződjön meg arról, hogy a központi telepítés munkalapon megadott összes információ nem módosult.

A következő ellenőrzéseket kell elvárnia a helyszíni mérnöktől az üzembe helyezési élményben:

- Ellenőrizze az összes kábelezést és a szegélyek kapcsolatát, és győződjön meg arról, hogy a megoldás megfelelően össze van szerelve, és megfelel az igényeinek.
- Konfigurálja a megoldás HLH (hardver életciklus-gazdagép), ha van ilyen.
- Győződjön meg arról, hogy az összes BMC, BIOS és hálózati beállítás helyes.
- Győződjön meg arról, hogy az összes összetevő belső vezérlőprogramja a megoldás legújabb jóváhagyott verziója.
- Indítsa el a telepítést.

> [!NOTE]
> A helyszíni mérnök üzembe helyezési eljárása egy munkahetet is igénybe vehet.

## <a name="post-deployment"></a>Üzembe helyezés után 
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

- Ha a központi telepítés [AD FS](azure-stack-integrate-identity.md)használatával fejeződött be, akkor az Azure stack hub-bélyeget integrálni kell az ügyfél saját ad FSával.

  > [!NOTE]
  > Ez a lépés az ügyfél feladata, bár a partner dönthet úgy, hogy ezt a szolgáltatást kínálja.

- Integráció egy meglévő figyelési rendszerrel az adott partnertől.

  -   [System Center Operations Manager integráció](azure-stack-integrate-monitor.md) a flotta-felügyeleti képességeket is támogatja.

  -   [Nagios-integráció](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="schedule"></a>Ütemezés

![Azure Stack hub helyszíni telepítésének teljes idővonala](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Támogatás
Az Azure Stack hub lehetővé teszi, hogy az Azure-konzisztens, integrált támogatási élmény a teljes rendszer életciklusát tartalmazza. Az Azure Stack hub-alapú integrált rendszerek teljes körű támogatásához az ügyfeleknek két támogatási szerződésre van szükségük: egyet a Microsofttal (vagy a felhőalapú megoldás szolgáltatójával) az Azure-szolgáltatások támogatásához, egyet pedig a hardver szolgáltatója a rendszertámogatáshoz. Az integrált támogatási felület összehangolt eszkalációt és megoldást biztosít, így az ügyfelek egy egységes támogatási élményt kapnak, függetlenül attól, hogy először hívják meg őket. Azon ügyfelek számára, akik már rendelkeznek Premier, Azure-standard/közvetlen vagy partneri támogatással a Microsofttal, Azure Stack hub szoftveres támogatást is tartalmaz.

Az integrált támogatási élmény egy Case Exchange-mechanizmust használ a támogatási esetek és a Microsoft és a hardverprofil közötti, kétirányú átvitelhez. Az Microsoft Azure Stack hub a [modern életciklus-szabályzattal](https://support.microsoft.com/help/30881)fog járni.

## <a name="next-steps"></a>További lépések
További információ az [általános adatközpont integrációs szempontjairól](azure-stack-datacenter-integration.md).
