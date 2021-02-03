---
title: Azure Stack hub adminisztrációs alapjai – MDC
titleSuffix: Azure Stack Hub
description: Ismerkedjen meg Azure Stack hub adminisztrációjának alapjaival. Tekintse meg a további támogatási információkat, és tekintse meg a következő lépéseket.
author: PatAltimore
ms.topic: article
ms.date: 10/26/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 2e88907094ea18e6fe3f8cbb49811fd1db307733
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99495455"
---
# <a name="azure-stack-hub-administration-basics---modular-data-center-mdc"></a>Azure Stack hub adminisztrációs alapjai – moduláris adatközpont (MDC)

Ha most ismerkedik a Azure Stack hub felügyeletével, több dolgot kell tudnia. Ez a cikk áttekintést nyújt a szerepköréről Azure Stack hub-operátorként, illetve arról, hogy mit kell tudnia a felhasználóknak a hatékony munkához.

## <a name="understand-the-builds"></a>A buildek ismertetése

Ha Azure Stack hub integrált rendszerét használja, az Azure Stack hub frissített verzióit frissítési csomagokon keresztül terjeszti. Ezeket a csomagokat importálhatja, és a felügyeleti portál **frissítések** csempéje használatával is alkalmazhatja.

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése

Vegye figyelembe a felhasználók számára elérhetővé tehető szolgáltatásokat. Azure Stack hub az Azure-szolgáltatások egy részhalmazát támogatja. A támogatott szolgáltatások listája továbbra is fejlődik.

### <a name="foundational-services"></a>Alapszolgáltatások

Alapértelmezés szerint a Azure Stack hub a következő alapszolgáltatásokat tartalmazza az Azure Stack hub telepítésekor:

- Compute
- Storage
- Hálózatkezelés
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

Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára. További információ: **oktatóanyagok** és útmutatók a   >   [Azure stack hub-kezelő dokumentációjában](../../operator/index.yml).

### <a name="service-roadmap"></a>Szolgáltatási ütemterv

Azure Stack hub továbbra is támogatja az Azure-szolgáltatásokat. A tervezett ütemtervhez tekintse meg az [Azure stack hub: az Azure-tanulmány bővítményét](https://azure.microsoft.com/resources/videos/azure-friday-azure-stack-an-extension-of-azure/) . Az új hirdetmények [Azure stack hub-blogbejegyzéseit](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) is figyelheti.

## <a name="what-account-should-i-use"></a>Milyen fiókot használok?

Az Azure Stack hub kezelésekor figyelembe kell venni néhány fiókra vonatkozó szempontot. Ez különösen igaz a Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszerű, Azure Active Directory (Azure AD) helyett identitás-szolgáltatóként történő központi telepítések esetén.

| **Fiók** | **Azure** | **AD FS** |
|---|---|---|
| Helyi rendszergazda (.\Rendszergazda) |   |
| Azure AD-beli globális rendszergazda | A telepítés során használatban van. <br> Az alapértelmezett szolgáltató tulajdonosa | Nem alkalmazható. |
| Fiók a kiterjesztett tárterülethez|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok a felügyelethez?

Azure Stack hub felügyeletéhez használhatja a [felügyeleti portált](../../operator/azure-stack-manage-portals.md) vagy a PowerShellt. Az alapvető fogalmak megismerésének legegyszerűbb módja a portálon keresztül. Ha a PowerShellt szeretné használni, előkészítési lépések szükségesek. Mielőtt elkezdené, érdemes megismernie, hogyan használják a PowerShellt Azure Stack hub-on. További információ: Ismerkedés [a PowerShell-lel Azure stack hub-on](../../user/azure-stack-powershell-overview.md).

Azure Stack hub az alapul szolgáló üzembe helyezési, felügyeleti és szervezeti mechanizmusként Azure Resource Manager használ. Ha Azure Stack hub felügyeletét végzi, és segítségre van szüksége a felhasználók támogatásához, ismerkedjen meg a Resource Managerrel. Tekintse meg a [Első lépések Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmányt.

## <a name="your-typical-responsibilities"></a>Tipikus feladatai

A felhasználók a szolgáltatásokat szeretnék használni. A legfontosabb szerepe, hogy ezek a szolgáltatások elérhetők legyenek a saját szemszögéből. A csomagok, ajánlatok és kvóták létrehozásával eldöntheti, hogy mely szolgáltatásokat kívánja felkínálni, és elérhetővé tenni ezeket a szolgáltatásokat. További információ: [az Azure stack hub szolgáltatásainak áttekintése](../../operator/service-plan-offer-subscription-overview.md).

Emellett elemeket is hozzá kell adnia [Azure stack hub Marketplace](../../operator/azure-stack-marketplace.md)-hez. A legegyszerűbb módszer, ha az [Azure-ból Azure stack központba tölti le a Piactéri elemeket](../../operator/azure-stack-download-azure-marketplace-item.md).

Ha tesztelni szeretné a csomagjait, ajánlatait és szolgáltatásait, használhatja a [felhasználói portált](../../operator/azure-stack-manage-portals.md); nem a felügyeleti portálon.

A szolgáltatások biztosítása mellett az operátorok rendszeres kötelességeit is meg kell tennie, hogy Azure Stack hubot. Ezek a feladatok a következő feladatokat foglalják magukban:

- Felhasználói fiókok hozzáadása az [Azure ad](../../operator/azure-stack-add-new-user-aad.md) üzembe helyezéséhez.
- [A hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérléssel](../../operator/azure-stack-manage-permissions.md). (Ez a feladat nem korlátozódik a rendszergazdákra.)
- Az [infrastruktúra állapotának figyelése](../../operator/azure-stack-monitor-health.md).
- A [hálózati](../../operator/azure-stack-viewing-public-ip-address-consumption.md) és [tárolási](../../operator/azure-stack-manage-storage-accounts.md) erőforrások kezelése.
- [Azure stack hub elindítása és leállítása](../../operator/azure-stack-start-and-stop.md).
- [A kiterjesztett tároló üzemeltetése](../../user/azure-stack-network-howto-extend-datacenter.md).
- [IoT hub kezelése](/azure/iot-hub/about-iot-hub).
- Az [Event hub kezelése](../../operator/event-hubs-rp-overview.md?bc=%2fazure-stack%2fbreadcrumb%2ftoc.json&branch=release-tzl&toc=%2fazure-stack%2ftdc%2ftoc.json&view=azs-2002).
- [App Service kezelése](../../operator/azure-stack-app-service-overview.md?bc=%2fazure-stack%2fbreadcrumb%2ftoc.json&branch=release-tzl&toc=%2fazure-stack%2ftdc%2ftoc.json&view=azs-2002).
- Cserélje le a hibás hardvereket. Itt láthatók a [cserélhető részek](../../operator/azure-stack-replace-component.md)listája.
- [Támogatás kérése](../../operator/azure-stack-help-and-support-overview.md?bc=%2fazure-stack%2fbreadcrumb%2ftoc.json&branch=release-tzl&toc=%2fazure-stack%2ftdc%2ftoc.json&view=azs-2002).

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
2. Futtassa `isi status –verbose` a parancsot a Avocent-kapcsolatban.

# <a name="monthly"></a>[Havonta](#tab/monthly)

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
6. Futtassa `isi status –verbose` a parancsot a Avocent-kapcsolatban.
7. Állítsa vissza a Storage-fiókokat.
8. A System leállítása.
9. Diagnosztikai naplók gyűjteménye.

---

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak

Lehetővé kell tennie, hogy a felhasználók tudják, hogyan dolgozhatnak Azure Stack hub szolgáltatásaival, hogyan csatlakozhat a környezethez, és hogyan fizethet elő az ajánlatokra. Minden olyan egyéni dokumentáció mellett, amelyet érdemes lehet biztosítani a felhasználók számára, [Azure stack hub felhasználói dokumentációra](../../user/index.yml)irányíthatja a felhasználókat.

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Ismerje meg, hogyan dolgozhat a szolgáltatásokkal Azure Stack központban

A felhasználóknak meg kell érteniük az adatokat, mielőtt a szolgáltatásokat használják, és alkalmazásokat építhetnek ki Azure Stack központban. A PowerShell-és API-verzióra vonatkozó követelmények például bizonyosak. Az Azure szolgáltatásban és a Azure Stack hub-ban ezzel egyenértékű szolgáltatásban is vannak szolgáltatások. Győződjön meg arról, hogy a felhasználók a következő cikkeket tekintik át:

- [Azure Stack hub és az Azure közötti különbségek szolgáltatások és alkalmazások létrehozásakor](../../user/azure-stack-considerations.md)
- [Azure Stack hub VM-funkciók](../../user/azure-stack-vm-considerations.md)
- [Azure Stack hub Storage: különbségek és megfontolások](../../user/azure-stack-acs-differences.md)

Az ezekben a cikkekben található információk összefoglalják az Azure-beli szolgáltatás és a Azure Stack hub közötti különbségeket. Kiegészíti az Azure-szolgáltatásokhoz elérhető információkat a globális Azure-dokumentációban.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Kapcsolódás Azure Stack hubhoz felhasználóként

A felhasználók tudni szeretnék, hogyan [érheti el a felhasználói portált](../../user/azure-stack-use-portal.md) , vagy hogyan csatlakozhat a PowerShell-lel. Az integrált rendszerkörnyezetek esetében a felhasználói portál címe az üzemelő példányok számától függ. A megfelelő URL-címmel kell megadnia a felhasználóknak.

A PowerShell használata esetén előfordulhat, hogy a felhasználóknak regisztrálniuk kell az erőforrás-szolgáltatókat, mielőtt használniuk tudják a szolgáltatásokat. Egy erőforrás-szolgáltató kezeli a szolgáltatást. A hálózati erőforrás-szolgáltató például olyan erőforrásokat kezel, mint a virtuális hálózatok, a hálózati adapterek és a terheléselosztó. [Telepíteniük](../../operator/powershell-install-az-module.md) kell a PowerShellt, [le kell tölteniük](../../operator/azure-stack-powershell-download.md) a további modulokat, és [be kell állítania](../../user/azure-stack-powershell-configure-user.md) a PowerShellt (amely magában foglalja az erőforrás-szolgáltató

### <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

Ahhoz, hogy a felhasználók használhassanak szolgáltatásokat, [elő kell fizetniük](../../operator/azure-stack-subscribe-plan-provision-vm.md) az operátorként létrehozott ajánlatra.

## <a name="where-to-get-support"></a>Honnan kaphat támogatást?

Azure Stack hub korábbi kiadásaival kapcsolatos támogatási információk megkereséséhez (pre-1905) lásd: [Azure stack hub karbantartási szabályzata](../../operator/azure-stack-servicing-policy.md).

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Támogatási kérelem megnyitásához válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában. Ezután válassza a **Súgó + támogatás** lehetőséget, majd az **új támogatási kérést** a **támogatás** szakaszban.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is), vagy bármilyen hardveres szoftvert – például a hardver életciklus-gazdagépén futó szoftvert –, először lépjen kapcsolatba az OEM hardver gyártójával.

Bármilyen más esetben forduljon a Microsoft CSS-hez.

## <a name="next-steps"></a>Következő lépések

- [Területi felügyelet Azure Stack központban](../../operator/azure-stack-region-management.md)