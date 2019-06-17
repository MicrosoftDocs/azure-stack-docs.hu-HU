---
title: Az Azure Stack adatközpont integrációs forgatókönyv |} A Microsoft Docs
description: Ismerje meg, egy helyszíni a sikeres üzembe helyezéséhez az Azure Stack a helyi adatközpontban várható.
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
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 9282747cf26598aee93fb6ae302a3d687eee2b17
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131548"
---
# <a name="azure-stack-datacenter-integration"></a>Az Azure Stack adatközpont integrációja

Ez a cikk ismerteti a teljes körű Azure Stack felhasználói élmény a vásárlási keresztül a helyszíni üzembe helyezés sikerességét megoldásszolgáltató által integrált rendszer. Ezt az információt használja megkönnyítése érdekében foglalkozás és közölheti az elvárásokat, az ügyfél egy Azure Stack segítségével.

Az Azure Stack ügyfélként kell várhatóan az alábbi lépéseket:

|     |Tervezési fázis|Rendelés feldolgozása|Központi telepítés előtti|Feldolgozó folyamat|Hardver kézbesítés|Helyszíni üzembe helyezés|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Partner bevonása a vásárlás előtti támogatás biztosításához.|Készítse elő a szoftverlicencelés és szerződések igény szerint.|Adja meg a szükséges eszközök, adatközpont integrációs követelményeinek és a dokumentáció gyűjtéséhez az ügyfél.|Adja meg a legújabb alapkonfiguráció buildek és eszközt lánc frissítések havi kiadása ütemben történik.|–|A Microsoft támogatási szakemberei segítséget nyújt az összes üzembe helyezési problémák.|
|**Partner**|Javasolt megoldási lehetőségeket az ügyfelek igényei alapján.<br><br>A koncepció igazolása (POC) javaslatot, ha szükséges.<br><br>Üzleti kapcsolatot létesíteni.<br><br>Döntse el, a támogatási szint.|Készítse elő a szükséges szerződéseket az ügyfélnél.<br><br>Vevői beszerzési rendelés létrehozása.<br><br>Döntse el, szállítási eseményeket egy idővonalon megjelenítve.<br><br>Csatlakozás a Microsoft-ügyfél, szükség esetén.|Adja meg az ügyfél a szükséges képzés biztosítása az összes üzembe helyezési Előfeltételek és a datacenter ismeretekkel integrálási lehetőségek.<br><br>Segítséget nyújt az ügyfél-érvényesítéssel tapasztalható a begyűjtött adatok teljességét és pontosságának biztosítása érdekében.|A alkalmazni a legutóbbi ellenőrzött alapkonfiguráció build.<br><br>A alkalmazni a szükséges a Microsoft központi telepítési eszközkészlet.|Adjon ki kiváló hardvert, hogy az ügyfél telephelyén.|A központi telepítés helyszíni mérnökként kezeli.<br><br>Állvány és a stack.<br><br>Hardver életciklus-gazdagép (HLH) telepítése.<br><br>Az Azure Stack üzemelő példányához.<br><br>Aktuális ügyfélnek ki.|
|**Ügyfél**|Importálni kívánt használati esetek ismertetik, és adja meg a követelményeknek.|A számlázási modellt használja, tekintse át és hagyja jóvá a szerződések meghatározására.|Végezze el a [üzembehelyezési munkalap](azure-stack-deployment-worksheet.md), és ellenőrizze, hogy az összes üzembe helyezési Előfeltételek a következők fennállnak és telepítésre készen áll.|–|Készítse elő a datacenter úgy, hogy minden szükséges power és hűtéssel szegély kapcsolat és egyéb szükséges adatközpont integrációja követelmény teljesülnek.|-Előfizetés hitelesítő adatait, és támogatja a megadott adatokkal kapcsolatos kérdések esetén üzembe helyezés során érhetők el.|
| | | | | | | |


## <a name="planning-phase"></a>Tervezési fázis
A tervezési fázist akkor, ha a Microsoft vagy az Azure Stack felhőszolgáltatói partner együttműködnek Önnel a kiértékelése, és ismerje meg, hogy az Azure Stack a megfelelő megoldás az, hogy az igényeinek:

Ezek segítségével eldöntheti, a következők:

-   Az Azure Stack a szervezet számára a megfelelő megoldás?

-   Milyen méretű megoldást kell?

-   Milyen típusú számlázási és licencelési modell működni fog a szervezet számára?

-   Mik a power és a hűtési követelmények?

Győződjön meg arról, hogy a hardveres megoldás lesz legjobban az igényeinek, hogy a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) is használható, amelyek segítik az előre történő megvásárlása határozza meg a megfelelő kapacitás és az Azure Stack hardverét konfigurációjának megtervezése megoldás.

A számolótáblát *nem* saját vizsgálati és elemzési megoldások hardver, hogy az igényeinek a leginkább helyett használhatók. Ha az Azure Stack üzembe helyezésének megtervezése, tekintse meg a [általános adatközpont integrációja szempontok](azure-stack-datacenter-integration.md) az Azure Stack integrált rendszerek.

## <a name="order-process-phase"></a>Rendelés folyamat fázis
Ezen a ponton a kérdésekre, megvalósíthatósági tartományállapot számos lenne érkezett válasz. Most, hogy az Azure Stack megvásárlása, valamint a szükséges szerződések a bejelentkezést követően véglegesítse és beszerzési rendelés készen áll, a rendszer kéri, a megoldásszolgáltató biztosíthat az integrációs követelményeinek adatokat.

## <a name="pre-deployment-phase"></a>Központi telepítés előtti fázist
Ebben a fázisban kell annak eldöntése, hogyan szeretné az adatközponttal integrálja az Azure Stack. Ez a folyamat megkönnyítése a Microsoft, a szolgáltatást nyújtó vállalkozásoknak együttműködve összeállítani követelmények sablon gyűjthet a környezetében az integrált rendszer központi telepítésének tervezése a szükséges információkat.

A [általános adatközpont integrációja szempontok](azure-stack-datacenter-integration.md) cikk, amely segítséget nyújt a kitöltheti a sablont, más néven az üzembehelyezési munkalap információkat tartalmaz. 

> [!IMPORTANT]
> Ezen szakasz során fontos, hogy az összes előfeltételként információk vizsgálni és kiválasztotta az a megoldás Rendezés előtt. Vegye figyelembe, hogy ez a lépés időigényes koordinálása és a szervezeten belül több oktatnak történő adatok gyűjtési van szükség. Helytelen vagy hiányos adatok hosszabb központi telepítés eredményezhet. 

A központi telepítés előtti fázist dönthet arról, hogy a következő lesz szüksége:

- **Az Azure Stack kapcsolat modell és az identity provider**. Dönthet úgy, hogy az Azure Stack üzembe helyezése vagy [kapcsolódik az internethez (és az Azure-bA)](azure-stack-connection-models.md). Hozhatja ki a legnagyobb előnnyel az Azure Stack, hibrid környezetekben, beleértve a szeretne üzembe helyezése az Azure-hoz csatlakoztatva. Az Active Directory összevonási szolgáltatások (AD FS) vagy az Azure Active Directory (Azure AD)-e egy egyszeri döntés üzembe helyezéskor kell végrehajtania. **Nem ez később megváltoztatható a teljes rendszer újbóli üzembe helyezése nélkül**.

- **Licencelési modell**. A licencelési modell lehetőségei közül választhat a központi telepítés kap függ. Identitás-szolgáltató tetszőleges nem befolyásolta a bérlői virtuális gépeket vagy a identitásrendszer és fiókokat használnak.
    - Az ügyfél egy [üzembe helyezési leválasztott](azure-stack-disconnected-deployment.md) csak az egyik lehetőség van: kapacitás-alapú számlázás.

    - Az ügyfél egy [kapcsolódó telepítési](azure-stack-connected-deployment.md) kapacitásalapú számlázási és fizetési mint-akkor-használható közül választhat. Kapacitás-alapú számlázás regisztráció egy nagyvállalati szerződés (EA) Azure-előfizetésre van szükség. Erre azért van szükség a regisztráció, amely előírja a Marketplace-en keresztül az Azure-előfizetés elemek rendelkezésre állását.

- **Hálózati integráció**. [Hálózati integráció](azure-stack-network.md) kulcsfontosságú a központi telepítés, üzemeltetése és kezelése az Azure Stack-rendszereket. Több szempontot, amelyek az Azure Stack megoldás képes legyen ellenállni, és a műveletek támogatásához egy magas rendelkezésre állású fizikai infrastruktúrával rendelkezik.

- **Tűzfal-integráció**. Javasoljuk, hogy Ön [használjon tűzfalat](azure-stack-firewall.md) biztonságos Azure Stack segítségével. Tűzfalak megakadályozhatja a DDoS elleni támadások, behatolásérzékelési és tartalmát is vizsgálhassa. Megjegyzendő azonban, hogy az Azure storage-szolgáltatásokban teljesítmény szűk keresztmetszetté válhat.


- **Tanúsítvány-követelmények**. Alapvető fontosságú, amely minden [szükséges tanúsítványok](azure-stack-pki-certs.md) érhetők el *előzetes* egy helyszíni szakemberének érkező az Adatközpont üzembe helyezéshez.

Miután az üzembehelyezési munkalap keresztül gyűjt az összes előfeltételként információkat, a megoldásszolgáltató elindít a feldolgozó folyamat adatközpontban helyezhető Azure stacket sikeres integráció érdekében a gyűjtött adatok alapján.

## <a name="hardware-delivery-phase"></a>Hardver kézbesítési fázis
A megoldásszolgáltató fog működni Önnel ütemezés, amikor a megoldás a létesítmény érkezik. Kapott, és a helyre kell idő ütemezése, a megoldás szolgáltatóval rendelkezik egy mérnököt a helyszínen az Azure Stack központi telepítésének végrehajtása származnak.

Ez **kulcsfontosságú** , hogy az összes előfeltételként adatok zárolva, és elérhető *előtt a helyszíni mérnök érkezik, a megoldás üzembe helyezéséhez*.

-   Minden tanúsítvány kell vásárolnia, és készen áll.

-   Régió neve meg kell határoznia.

-   Minden hálózati integráció paraméterek véglegesítése, és mi osztottak meg a megoldásszolgáltató felel meg.

> [!TIP]
> Ha ezt az információt bármelyikét megváltozott, ügyeljen arra, hogy a módosítás kommunikálni a megoldásszolgáltató, a tényleges telepítés ütemezése előtt.

## <a name="onsite-deployment-phase"></a>Helyszíni üzembe helyezési fázisnak
Azure Stack üzembe helyezéséhez a hardveres megoldás szolgáltatói helyszíni mérnökként kell szükség van a központi telepítés indíthat. Ahhoz, hogy az üzembe helyezés sikerességét, győződjön meg arról, hogy az üzembehelyezési munkalap keresztül elérhető összes információt nem változott. 

A következő egy mire számíthat a helyszínen mérnök, az üzembe helyezés során:

- Ellenőrizze az összes kábelek és szegély kapcsolatot annak biztosítására, a megoldás megfelelő felépítését, és megfelel a követelményeknek.
- A megoldás HLH (hardver életciklus gazdagép), konfigurálása, ha van ilyen.
- Ellenőrizze, hogy minden BMC, a BIOS-ban és a hálózati beállítások megfelelőek.
- Ellenőrizze, hogy az összes összetevő belső vezérlőprogramját a legújabb jóváhagyott verziójú a megoldás által.
- Indítsa el a központi telepítést.

> [!NOTE]
> A telepítési eljárás által a helyszínen mérnök eltarthat egy üzleti hét végrehajtásához.

## <a name="post-deployment-phase"></a>Üzembe helyezés utáni fázist
Számos lépést kell végrehajtani a partner által előtt a megoldás lesz továbbítva az ügyfél a utáni integrációs fázisban. Ebben a fázisban érvényesítési, fontos, hogy a rendszer központi telepítésének és teljesítményű megfelelően. 

Az OEM-Partner által végrehajtandó műveletek a következők:

- [Run test-azurestack](azure-stack-diagnostic-test.md)

- [Regisztráció az Azure-ral](azure-stack-registration.md)

- [Marketplace-en szindikálás](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- Biztonsági mentési Kapcsolókonfiguráció és HLH konfigurációs fájlok

- Remove DVM

- Összefoglaló ügyfél telepítésének előkészítése

- [Győződjön meg arról, hogy a megoldás szoftverek a legújabb verzióra frissül, a frissítések keresése](./azure-stack-updates.md)

Vannak kötelező vagy nem kötelező a telepítés típusától függően számos lépést.

- Ha a telepítés befejeződött, használatával [az AD FS](azure-stack-integrate-identity.md), majd az Azure Stack stamp kell integrálni lehet az ügyfél által a saját Active Directory összevonási szolgáltatások.

  > [!NOTE]
  > Ez a lépés nem az ügyfél felelőssége, bár partnernek igény szerint választhat, hogy ehhez szolgáltatásokat.

- Integráció a meglévő felügyeleti rendszer a megfelelő partnertől.

  -   [A System Center Operations Manager integrációjának](azure-stack-integrate-monitor.md) flottakezelés képességeket is támogatja.

  -   [Nagios-integráció](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Teljes ütemterv

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Támogatás
Az Azure Stack lehetővé teszi, hogy egy Azure-konzisztens, integrált támogatási környezet, amely lefedi a teljes életciklusa. Azure Stack integrált rendszerek teljes körű támogatásához, az ügyfeleknek kell két támogatási szerződés; egy, az Azure-szolgáltatások támogatásához, egy, a szolgáltatót a rendszer támogatja a Microsoft (vagy a Cloud Solution Provider). Az integrált támogatási környezet koordinált eszkalációs és problémamegoldási, biztosítja, hogy ügyfeleinknek minél több függetlenül attól, hogy kihez hívják meg először támogatásban részesül. Ügyfelek esetében, akik már rendelkezik Premier Azure - Standard / a Microsoft kiszolgálószoftveres támogatás az Azure Stack-Partner vagy a prodirect szintű támogatás is biztosított.

Az integrált támogatási környezet felhasznál egy esetet Exchange mechanizmus és a kétirányú átvitele támogatási esetek és megkülönbözteti a kis frissítéseket a Microsoft és a hardver partner között. A Microsoft Azure Stack követi a [Modern életciklus-szabályzat](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>További lépések
Tudjon meg többet [általános adatközpont integrációja szempontok](azure-stack-datacenter-integration.md).
