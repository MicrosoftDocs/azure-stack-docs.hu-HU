---
title: Azure Stack hub adminisztrációs alapjai
description: Megismerheti Azure Stack hub felügyeletének alapjait.
author: justinha
ms.topic: article
ms.date: 06/16/2020
ms.author: justinha
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: b277eb9dcb1ac66af870db68c919e711aadc1ac7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543510"
---
# <a name="azure-stack-hub-administration-basics"></a>Azure Stack hub adminisztrációs alapjai

Ha most ismerkedik a Azure Stack hub felügyeletével, több dolgot kell tudnia. Ez a cikk áttekintést nyújt a szerepkörről Azure Stack hub-kezelőként, és azt, hogy mit kell tudnia a felhasználóknak a hatékony munkához.

## <a name="understand-the-builds"></a>A buildek ismertetése

### <a name="integrated-systems"></a>Integrált rendszerek

Azure Stack hub integrált rendszer használata esetén a frissítési csomagok a Azure Stack hub frissített verzióit terjesztik. Ezeket a csomagokat importálhatja, és a felügyeleti portál **frissítések** csempéje használatával is alkalmazhatja.
 
### <a name="development-kit"></a>Fejlesztői csomag

Ha a Azure Stack Development Kit (ASDK) használja, tekintse át a [Mi az Azure stack hub?](../asdk/asdk-what-is.md) című témakört, amelyből MEGISMERHETI a ASDK célját és korlátozásait. A ASDK *homokozóban* is használható, ahol kiértékelheti Azure stack hubot, és nem éles környezetben fejlesztheti és tesztelheti alkalmazásait. A központi telepítéssel kapcsolatos információkért lásd: [Azure stack Development Kit központi telepítés](../asdk/asdk-install.md).

Az Azure-hoz hasonlóan a gyors innováció is. Rendszeresen kiadjuk az új buildeket. Ha a ASDK futtatja, és a legújabb buildre szeretne áttérni, újra kell [telepítenie Azure stack hubot](../asdk/asdk-redeploy.md). A frissítési csomagok nem alkalmazhatók. Ez a folyamat időt vesz igénybe, de az előnye, hogy kipróbálhatja a legújabb funkciókat. A webhelye ASDK dokumentációja a legújabb kiadás-összeállítást mutatja be.

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése

A felhasználók számára elérhetővé tenni kívánt szolgáltatások ismerete szükséges. Azure Stack hub az Azure-szolgáltatások egy részhalmazát támogatja. A támogatott szolgáltatások listája továbbra is fejlődik.

**Alapszolgáltatások**

Alapértelmezés szerint a Azure Stack hub a következő "alapszolgáltatásokat" tartalmazza a Azure Stack hub telepítésekor:

- Compute
- Storage
- Hálózatkezelés
- Key Vault

Ezekkel az alapszolgáltatásokkal minimális konfigurációval biztosíthat infrastruktúra-szolgáltatást (IaaS) a felhasználók számára.

**További szolgáltatások**

Jelenleg a következő, platform-szolgáltatásként nyújtott szolgáltatásokkal (Pásti) támogatjuk a szolgáltatást:

- App Service
- Azure Functions
- SQL-és MySQL-adatbázisok
- Kubernetes (előzetes verzió)

Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára. További információkért tekintse meg az Azure Stack hub-kezelő dokumentációjának "oktatóanyagok" és "útmutató guides\Offer szolgáltatások" részeit.

**Szolgáltatási ütemterv**

Azure Stack hub továbbra is támogatja az Azure-szolgáltatásokat. A tervezett ütemtervhez tekintse meg az [Azure stack hub: az Azure-tanulmány bővítményét](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) . Az új hirdetmények [Azure stack hub-blogbejegyzéseit](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) is figyelheti.

## <a name="what-account-should-i-use"></a>Milyen fiókot használok?

Az Azure Stack hub kezelésekor figyelembe kell venni néhány fiókra vonatkozó szempontot. Különösen a Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszerű központi telepítések identitás-szolgáltatóként való használata Azure Active Directory (Azure AD) helyett. A következő fiókokra vonatkozó megfontolások a Azure Stack hub integrált rendszereire és a ASDK üzemelő példányokra is érvényesek:

|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazda (.\Rendszergazda)|ASDK-gazda rendszergazdája.|ASDK-gazda rendszergazdája.|
|AzureStack\AzureStackAdmin|ASDK-gazda rendszergazdája.<br><br>A Azure Stack hub felügyeleti portálra való bejelentkezéshez használható.<br><br>Hozzáférés a Service Fabric gyűrűk megtekintéséhez és felügyeletéhez.|ASDK-gazda rendszergazdája.<br><br>Nincs hozzáférése az Azure Stack hub felügyeleti portálján.<br><br>Hozzáférés a Service Fabric gyűrűk megtekintéséhez és felügyeletéhez.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) már nem tulajdonosa.|
|AzureStack\CloudAdmin|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat.|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat.<br><br>Nem lehet bejelentkezni a ASDK gazdagépre.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) tulajdonosa.|
|Az Azure AD globális rendszergazdája|A telepítés során használatban van.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) tulajdonosa.|Nem alkalmazható.|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok a felügyelethez?
 
Azure Stack hub felügyeletéhez használhatja a [felügyeleti portált](azure-stack-manage-portals.md) vagy a PowerShellt. Az alapvető fogalmak megismerésének legegyszerűbb módja a portálon keresztül. Ha a PowerShellt szeretné használni, előkészítési lépések szükségesek. Mielőtt elkezdené, érdemes megismernie, hogyan használják a PowerShellt Azure Stack hub-on. További információ: Ismerkedés [a PowerShell-lel Azure stack hub-on](../user/azure-stack-powershell-overview.md).

Azure Stack hub az alapul szolgáló üzembe helyezési, felügyeleti és szervezeti mechanizmusként Azure Resource Manager használ. Ha Azure Stack hub felügyeletét végzi, és segítségre van szüksége a felhasználók támogatásához, megismerheti a Resource Managert. Tekintse meg a [Első lépések Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmányt.

## <a name="your-typical-responsibilities"></a>Tipikus feladatai

A felhasználók a szolgáltatásokat szeretnék használni. A legfontosabb szerepe, hogy ezek a szolgáltatások elérhetők legyenek a saját szemszögéből. Döntse el, hogy mely szolgáltatásokat szeretné felkínálni, és tegye elérhetővé ezeket a szolgáltatásokat csomagok, ajánlatok és kvóták létrehozásával. További információ: [az Azure stack hub szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md). 

Emellett elemeket is hozzá kell adnia [Azure stack hub Marketplace](azure-stack-marketplace.md)-hez. A legegyszerűbb módszer, ha az [Azure-ból Azure stack központba tölti le a Piactéri elemeket](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Ha tesztelni szeretné a csomagjait, ajánlatait és szolgáltatásait, használhatja a [felhasználói portált](azure-stack-manage-portals.md); nem a felügyeleti portálon.

A szolgáltatások biztosítása mellett az operátorok rendszeres kötelességeit is meg kell tennie, hogy Azure Stack hubot. Ezek a feladatok a következő feladatokat foglalják magukban:

- Felhasználói fiókok hozzáadása ( [Azure ad](azure-stack-add-new-user-aad.md) -telepítéshez vagy [AD FS](azure-stack-add-users-adfs.md) központi telepítéshez).
- [Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendeléséhez](azure-stack-manage-permissions.md) (ez a feladat nem korlátozódik a rendszergazdákra.)
- Az [infrastruktúra állapotának figyelése](azure-stack-monitor-health.md).
- A [hálózati](azure-stack-viewing-public-ip-address-consumption.md) és [tárolási](azure-stack-manage-storage-accounts.md) erőforrások kezelése.
- Cserélje le a hibás hardvereket. Például [cserélje le a meghibásodott lemezt](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak

Lehetővé kell tennie, hogy a felhasználók tudják, hogyan dolgozhatnak Azure Stack hub szolgáltatásaival, hogyan csatlakozhat a környezethez, és hogyan fizethet elő az ajánlatokra. Minden olyan egyéni dokumentáció mellett, amelyet érdemes lehet biztosítani a felhasználók számára, [Azure stack hub felhasználói dokumentációra](../user/index.yml)irányíthatja a felhasználókat.

**Ismerje meg, hogyan dolgozhat a szolgáltatásokkal Azure Stack központban**

A felhasználóknak meg kell érteniük az adatokat, mielőtt a szolgáltatásokat használják, és alkalmazásokat építhetnek ki Azure Stack központban. A PowerShell-és API-verzióra vonatkozó követelmények például bizonyosak. Az Azure szolgáltatásban és a Azure Stack hub-ban ezzel egyenértékű szolgáltatásban is vannak szolgáltatások. Győződjön meg arról, hogy a felhasználók a következő cikkeket tekintik át:

- [Főbb szempontok: szolgáltatások használata vagy alkalmazások készítése Azure Stack hubhoz](../user/azure-stack-considerations.md)
- [Az Azure Stack hub Virtual Machines szempontjai](../user/azure-stack-vm-considerations.md)
- [Tárolás: különbségek és megfontolások](../user/azure-stack-acs-differences.md)

Az ezekben a cikkekben található információk összefoglalják az Azure-beli szolgáltatás és a Azure Stack hub közötti különbségeket. Kiegészíti az Azure-szolgáltatásokhoz elérhető információkat a globális Azure-dokumentációban.

**Kapcsolódás Azure Stack hubhoz felhasználóként**

ASDK-környezetben, ha a felhasználó nem használja Távoli asztal a ASDK-gazdagéphez való csatlakozáshoz, konfigurálhat virtuális magánhálózati (VPN) kapcsolatot Azure Stack hubhoz való csatlakozáshoz. Lásd: [kapcsolódás Azure stack hubhoz](../asdk/asdk-connect.md).

A felhasználók tudni szeretnék, hogyan [érheti el a felhasználói portált](../user/azure-stack-use-portal.md) , vagy hogyan csatlakozhat a PowerShell-lel. Az integrált rendszerkörnyezetek esetében a felhasználói portál címe az üzemelő példányok számától függ. A megfelelő URL-címmel kell megadnia a felhasználóknak.

A PowerShell használata esetén előfordulhat, hogy a felhasználóknak regisztrálniuk kell az erőforrás-szolgáltatókat, mielőtt használniuk tudják a szolgáltatásokat. Egy erőforrás-szolgáltató kezeli a szolgáltatást. A hálózati erőforrás-szolgáltató például olyan erőforrásokat kezel, mint a virtuális hálózatok, a hálózati adapterek és a terheléselosztó. [Telepíteniük](powershell-install-az-module.md) kell a PowerShellt, [le kell tölteniük](azure-stack-powershell-download.md) a további modulokat, és [be kell állítania](../user/azure-stack-powershell-configure-user.md) a PowerShellt (amely magában foglalja az erőforrás-szolgáltató

**Előfizetés egy ajánlatra**

Ahhoz, hogy a felhasználók használhassanak szolgáltatásokat, [elő kell fizetniük](azure-stack-subscribe-plan-provision-vm.md) az operátorként létrehozott ajánlatra.

## <a name="where-to-get-support"></a>Honnan kaphat támogatást?

> [!Note]  
> Az Azure Stack 1905 hub korábbi kiadásaival kapcsolatos támogatási információk megkereséséhez lásd: [Súgó és támogatás a korábbi kiadásokhoz Azure stack hub (pre-1905)](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Integrált rendszerek

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás Microsoft ügyfélszolgálataon keresztül érhető el. Támogatási kérelem megnyitásához válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában. Ezután válassza a **Súgó + támogatás** lehetőséget, majd az **új támogatási kérést** a **támogatás** szakaszban.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) vagy bármilyen hardveres szoftverrel, például a hardver életciklus-állomásán futó szoftverrel, akkor először lépjen kapcsolatba az OEM hardver gyártójával.

Minden más esetben lépjen kapcsolatba Microsoft ügyfélszolgálata.

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack fejlesztői készlet (ASDK)

A ASDK a támogatással kapcsolatos kérdéseket is megteheti a [Microsoft fórumokon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). A fórumok beszerzéséhez válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában, majd válassza a **Súgó + támogatás** lehetőséget, majd válassza az MSDN- **fórumok** lehetőséget a **támogatás** szakaszban. Ezeket a fórumokat rendszeresen figyelik. Mivel a ASDK egy kiértékelési környezet, Microsoft ügyfélszolgálataon keresztül nem érhető el hivatalos támogatás.

## <a name="next-steps"></a>Következő lépések

[Területi felügyelet Azure Stack központban](azure-stack-region-management.md)
