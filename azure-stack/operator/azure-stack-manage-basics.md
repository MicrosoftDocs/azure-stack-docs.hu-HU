---
title: Azure Stack hub adminisztrációs alapjai
titleSuffix: Azure Stack Hub
description: Ismerkedjen meg Azure Stack hub felügyeletének alapjaival.
author: nicoalba
ms.topic: article
ms.date: 03/02/2020
ms.author: v-nialba
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8f56dafbcc27e3ff4de9adcfbf5de27dea115bb3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79293955"
---
# <a name="azure-stack-hub-administration-basics"></a>Azure Stack hub adminisztrációs alapjai

Ha most ismerkedik a Azure Stack hub felügyeletével, több dolgot kell tudnia. Ez a cikk áttekintést nyújt a szerepköréről Azure Stack hub-operátorként, illetve arról, hogy mit kell tudnia a felhasználóknak a hatékony munkához.

## <a name="understand-the-builds"></a>A buildek ismertetése

Ha Azure Stack hub integrált rendszerét használja, az Azure Stack hub frissített verzióit frissítési csomagokon keresztül terjeszti. Ezeket a csomagokat importálhatja, és a felügyeleti portál **frissítések** csempével is alkalmazhatja.

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése

Vegye figyelembe a felhasználók számára elérhetővé tehető szolgáltatásokat. Azure Stack hub az Azure-szolgáltatások egy részhalmazát támogatja. A támogatott szolgáltatások listája továbbra is fejlődik.

### <a name="foundational-services"></a>Alapszolgáltatások

Alapértelmezés szerint a Azure Stack hub a következő alapszolgáltatásokat tartalmazza az Azure Stack hub telepítésekor:

- Számítás
- Tárterület
- Hálózat
- Key Vault

Ezekkel az alapszolgáltatásokkal minimális konfigurációval biztosíthat infrastruktúra-szolgáltatást (IaaS) a felhasználók számára.

### <a name="additional-services"></a>További szolgáltatások

A következő kiegészítő szolgáltatásként nyújtott platform-szolgáltatásokat támogatjuk:

- App Service
- Azure Functions
- SQL-és MySQL-adatbázisok
- Kubernetes
- IoT Hub
- Eseményközpont

Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára. További információ: **oktatóanyagok** és **útmutatók** > a [Azure stack hub-kezelő dokumentációjában](https://docs.microsoft.com/azure-stack/operator/)találhat **szolgáltatásokat** .

### <a name="service-roadmap"></a>Szolgáltatási ütemterv

Azure Stack hub továbbra is támogatja az Azure-szolgáltatásokat. A tervezett ütemtervhez tekintse meg az [Azure stack hub: az Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) -tanulmány bővítményét. Az új hirdetmények [Azure stack hub-blogbejegyzéseit](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) is figyelheti.

## <a name="what-account-should-i-use"></a>Milyen fiókot használok?

Az Azure Stack hub kezelésekor figyelembe kell venni néhány fiókra vonatkozó szempontot. Ez különösen igaz a Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszerű, Azure Active Directory (Azure AD) helyett identitás-szolgáltatóként történő központi telepítések esetén.

| **Fiók** | **Azure** | **AD FS** |
|---|---|---|
| Helyi rendszergazda (.\Rendszergazda) |   |
| Az Azure AD globális rendszergazdája | A telepítés során használatban van. <br> Az alapértelmezett szolgáltató tulajdonosa | Nem alkalmazható. |
| Fiók a kiterjesztett tárterülethez|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok a felügyelethez?

Azure Stack hub kezeléséhez a [felügyeleti portál](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002) vagy a PowerShell használható. Az alapvető fogalmak megismerésének legegyszerűbb módja a portálon keresztül. Ha a PowerShellt szeretné használni, előkészítési lépések szükségesek. Mielőtt elkezdené, érdemes megismernie, hogyan használják a PowerShellt Azure Stack hub-on. További információ: Ismerkedés [a PowerShell-lel Azure stack hub-on](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-overview?view=azs-2002).

Azure Stack hub az alapul szolgáló üzembe helyezési, felügyeleti és szervezeti mechanizmusként Azure Resource Manager használ. Ha Azure Stack hub felügyeletét végzi, és segítségre van szüksége a felhasználók támogatásához, ismerkedjen meg a Resource Managerrel. Tekintse meg a [Első lépések Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmányt.

## <a name="your-typical-responsibilities"></a>Tipikus feladatai

A felhasználók a szolgáltatásokat szeretnék használni. A legfontosabb szerepe, hogy ezek a szolgáltatások elérhetők legyenek a saját szemszögéből. A csomagok, ajánlatok és kvóták létrehozásával eldöntheti, hogy mely szolgáltatásokat kívánja felkínálni, és elérhetővé tenni ezeket a szolgáltatásokat. További információ: [az Azure stack hub szolgáltatásainak áttekintése](https://review.docs.microsoft.com/en-us/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-2002).

Emellett elemeket is hozzá kell adnia [Azure stack hub Marketplace](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-marketplace?view=azs-2002)-hez. A legegyszerűbb módszer, ha az [Azure-ból Azure stack központba tölti le a Piactéri elemeket](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2002).

Ha tesztelni szeretné a csomagjait, ajánlatait és szolgáltatásait, használhatja a [felhasználói portált](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002); nem a felügyeleti portálon.

A szolgáltatások biztosítása mellett az operátorok rendszeres kötelességeit is meg kell tennie, hogy Azure Stack hubot. Ezek a feladatok a következő feladatokat foglalják magukban:

- Felhasználói fiókok hozzáadása az [Azure AD](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-add-new-user-aad?view=azs-2002) telepítéséhez.
- [A hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérléssel](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-permissions?view=azs-2002). (Ez a feladat nem korlátozódik a rendszergazdákra.)
- Az [infrastruktúra állapotának figyelése](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-monitor-health?view=azs-2002).
- A [hálózati](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-viewing-public-ip-address-consumption?view=azs-2002) és a [tárolási](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-storage-accounts?view=azs-2002) erőforrások kezelése.
- [Azure stack hub elindítása és leállítása](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-start-and-stop?view=azs-2002&branch=release-tzl).
- [A kiterjesztett tároló üzemeltetése](https://review.docs.microsoft.com/en-us/azure-stack/tdc/extended-storage-operator-guide?view=azs-2002&branch=release-tzl).
- [IoT hub kezelése](https://review.docs.microsoft.com/en-us/azure-stack/operator/iot-hub-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&.bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Az [Event hub kezelése](https://review.docs.microsoft.com/en-us/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [App Service kezelése](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Cserélje le a hibás hardvereket. Itt láthatók a [cserélhető részek](https://review.docs.microsoft.com/en-us/azure-stack/tdc/cru-replaceable-parts?view=azs-2002&branch=release-tzl)listája.
- [Támogatás kérése](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Operátori feladatok

Az alábbi lista az operátorok napi, heti és havi feladatait sorolja fel:

# <a name="daily"></a>[Napi](#tab/daily)

1. Riasztások keresése.
2. A biztonsági mentés állapotának megtekintése.
3. A Defender aláírásának (leválasztott rendszerek) frissítése.
4. A Isilon rendszerállapotának és eseményeinek keresése a OneFS-ben.
5. A Isilon kapacitásának megkeresése.

# <a name="weekly"></a>[Heti](#tab/weekly)

1. Kapacitásának ellenőrzését.
2. `isi status –verbose` futtatása a Avocent-kapcsolatban.

# <a name="monthly"></a>[Havi](#tab/monthly)

1. Havi frissítési csomagok (Microsoft & OEM) alkalmazása.
2. A biztonsági mentés ellenőrzése a ASDK használatával.
3. Azure Stack hub Marketplace (naprakész) kezelése.
4. Frissítési kapcsoló belső vezérlőprogram & Avocent.
5. Tárolási kapacitás visszaigénylése.

# <a name="ondemand"></a>[OnDemand](#tab/ondemand)

1. Titkos kód elforgatása.
2. Ajánlatok, csomagok és kvóták létrehozása és frissítése.
3. Gyorsjavítási csomagok alkalmazása.
4. Gyorsjavítási csomagok alkalmazása.
5. Bontsa ki a kapacitás (csomópontok & IPSpace) elemet.
6. `isi status –verbose` futtatása a Avocent-kapcsolatban.
7. Állítsa vissza a Storage-fiókokat.
8. A System leállítása.
9. Diagnosztikai naplók gyűjteménye.

---

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak

Lehetővé kell tennie, hogy a felhasználók tudják, hogyan dolgozhatnak Azure Stack hub szolgáltatásaival, hogyan csatlakozhat a környezethez, és hogyan fizethet elő az ajánlatokra. Minden olyan egyéni dokumentáció mellett, amelyet érdemes lehet biztosítani a felhasználók számára, [Azure stack hub felhasználói dokumentációra](https://review.docs.microsoft.com/en-us/azure-stack/user/)irányíthatja a felhasználókat.

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Ismerje meg, hogyan dolgozhat a szolgáltatásokkal Azure Stack központban

A felhasználóknak meg kell érteniük az adatokat, mielőtt a szolgáltatásokat használják, és alkalmazásokat építhetnek ki Azure Stack központban. A PowerShell-és API-verzióra vonatkozó követelmények például bizonyosak. Az Azure szolgáltatásban és a Azure Stack hub-ban ezzel egyenértékű szolgáltatásban is vannak szolgáltatások. Győződjön meg arról, hogy a felhasználók a következő cikkeket tekintik át:

- [Azure Stack hub és az Azure közötti különbségek szolgáltatások és alkalmazások létrehozásakor](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-considerations?view=azs-2002)
- [Azure Stack hub VM-funkciók](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-vm-considerations?view=azs-2002)
- [Azure Stack hub Storage: különbségek és megfontolások](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-acs-differences?view=azs-2002)

Az ezekben a cikkekben található információk összefoglalják az Azure-beli szolgáltatás és a Azure Stack hub közötti különbségeket. Kiegészíti az Azure-szolgáltatásokhoz elérhető információkat a globális Azure-dokumentációban.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Kapcsolódás Azure Stack hubhoz felhasználóként

A felhasználók tudni szeretnék, hogyan [érheti el a felhasználói portált](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-use-portal?view=azs-2002) vagy hogyan csatlakozhat a PowerShell-lel. Az integrált rendszerkörnyezetek esetében a felhasználói portál címe az üzemelő példányok számától függ. A megfelelő URL-címmel kell megadnia a felhasználóknak.

A PowerShell használata esetén előfordulhat, hogy a felhasználóknak regisztrálniuk kell az erőforrás-szolgáltatókat, mielőtt használniuk tudják a szolgáltatásokat. Egy erőforrás-szolgáltató kezeli a szolgáltatást. A hálózati erőforrás-szolgáltató például olyan erőforrásokat kezel, mint a virtuális hálózatok, a hálózati adapterek és a terheléselosztó.  [Telepíteniük](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-install?view=azs-2002) kell PowerShellt, [le kell tölteniük](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-download?view=azs-2002) további modulokat, és [konfigurálni](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-configure-user?view=azs-2002) PowerShellt (amely magában foglalja az erőforrás-szolgáltatói regisztrációt).

### <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

Ahhoz, hogy a felhasználók használhassanak szolgáltatásokat, [elő kell fizetniük egy olyan ajánlatra](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-2002) , amelyet operátorként hozott létre.

## <a name="where-to-get-support"></a>Honnan kaphat támogatást?

Azure Stack hub korábbi kiadásaival kapcsolatos támogatási információk megkereséséhez (pre-1905) lásd: [Azure stack hub karbantartási szabályzata](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-servicing-policy?view=azs-2002).

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Támogatási kérelem megnyitásához válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában. Ezután válassza a **Súgó + támogatás** , majd az **új támogatási kérelem** a **támogatási** szakaszban.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is), vagy bármilyen hardveres szoftvert – például a hardver életciklus-gazdagépén futó szoftvert –, először lépjen kapcsolatba az OEM hardver gyártójával.

Bármilyen más esetben forduljon a Microsoft CSS-hez.

## <a name="next-steps"></a>Következő lépések

- [Területi felügyelet Azure Stack központban](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-region-management?view=azs-2002)
