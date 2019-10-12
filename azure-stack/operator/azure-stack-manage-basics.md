---
title: Azure Stack adminisztráció alapjai | Microsoft Docs
description: Ismerkedjen meg Azure Stack adminisztráció alapjaival.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.lastreviewed: 05/29/2019
ms.openlocfilehash: f9b9d6d1474c22c8e31d24ae08faf1aac6d5e9c1
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283489"
---
# <a name="azure-stack-administration-basics"></a>Azure Stack adminisztráció alapjai

Ha most ismerkedik a Azure Stack adminisztrációval, több dolgot kell tudnia. Ez a cikk áttekintést nyújt a szerepköréről Azure Stack operátorként, valamint arról, hogy mit kell tudnia a felhasználóknak a hatékony munkához.

## <a name="understand-the-builds"></a>A buildek ismertetése

### <a name="integrated-systems"></a>Integrált rendszerek

Azure Stack integrált rendszer használata esetén a frissítési csomagok a Azure Stack frissített verzióit terjesztik. Ezeket a csomagokat importálhatja, és a felügyeleti portál **frissítések** csempéje használatával is alkalmazhatja.
 
### <a name="development-kit"></a>Fejlesztői csomag

Ha a Azure Stack Development Kit (ASDK) használja, tekintse át a [Mi az Azure stack?](../asdk/asdk-what-is.md) a ASDK céljának és korlátozásának megismeréséhez. Használhatja a ASDK *homokozóban*, ahol kiértékelheti az alkalmazásokat, és nem éles környezetben fejlesztheti és tesztelheti Azure Stackeit. A központi telepítéssel kapcsolatos információkért lásd: [Azure stack Development Kit központi telepítés](../asdk/asdk-install.md).

Az Azure-hoz hasonlóan a gyors innováció is. Rendszeresen kiadjuk az új buildeket. Ha a ASDK futtatja, és a legújabb buildre szeretne áttérni, újra kell [telepítenie Azure stack](../asdk/asdk-redeploy.md). A frissítési csomagok nem alkalmazhatók. Ez a folyamat időt vesz igénybe, de az előnye, hogy kipróbálhatja a legújabb funkciókat. A webhelye ASDK dokumentációja a legújabb kiadás-összeállítást mutatja be.

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése

A felhasználók számára elérhetővé tenni kívánt szolgáltatások ismerete szükséges. Azure Stack támogatja az Azure-szolgáltatások egy részhalmazát. A támogatott szolgáltatások listája továbbra is fejlődik.

**Alapszolgáltatások**

Alapértelmezés szerint a Azure Stack a következő "alapszolgáltatásokat" tartalmazza Azure Stack telepítésekor:

- Számítási szolgáltatások
- Adattárolás
- Hálózatkezelés
- Key Vault

Ezekkel az alapszolgáltatásokkal minimális konfigurációval biztosíthat infrastruktúra-szolgáltatást (IaaS) a felhasználók számára.

**További szolgáltatások**

Jelenleg a következő, platform-szolgáltatásként nyújtott szolgáltatásokkal (Pásti) támogatjuk a szolgáltatást:

- App Service
- Azure Functions
- SQL-és MySQL-adatbázisok
- Kubernetes (előzetes verzió)

Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára. További információkért tekintse meg a Azure Stack-kezelő dokumentációjának "oktatóanyagok" és "útmutató guides\Offer Services" részeit.

**Szolgáltatási ütemterv**

Azure Stack továbbra is támogatja az Azure-szolgáltatásokat. A tervezett ütemtervhez tekintse meg a [Azure stack: az Azure-tanulmány kiterjesztését](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) . Az új hirdetmények [Azure stack blogbejegyzéseit](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) is nyomon követheti.

## <a name="what-account-should-i-use"></a>Milyen fiókot használok?
Az Azure Stack kezelésekor figyelembe kell vennie néhány fiókra vonatkozó szempontot. Különösen a Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszerű központi telepítések identitás-szolgáltatóként való használata Azure Active Directory (Azure AD) helyett. A következő fiókokra vonatkozó megfontolások a Azure Stack integrált rendszerekre és a ASDK üzemelő példányokra egyaránt érvényesek:


|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazda (.\Rendszergazda)|ASDK-gazda rendszergazdája.|ASDK-gazda rendszergazdája.|
|AzureStack\AzureStackAdmin|ASDK-gazda rendszergazdája.<br><br>A Azure Stack felügyeleti portálra való bejelentkezéshez használható.<br><br>Hozzáférés a Service Fabric gyűrűk megtekintéséhez és felügyeletéhez.|ASDK-gazda rendszergazdája.<br><br>Nincs hozzáférése a Azure Stack felügyeleti portálhoz.<br><br>Hozzáférés a Service Fabric gyűrűk megtekintéséhez és felügyeletéhez.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) már nem tulajdonosa.|
|AzureStack\CloudAdmin|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat.|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat.<br><br>Nem lehet bejelentkezni a ASDK gazdagépre.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) tulajdonosa.|
|Az Azure AD globális rendszergazdája|A telepítés során használatban van.<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) tulajdonosa.|Nem alkalmazható.|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok a felügyelethez?
 
A Azure Stack kezeléséhez használhatja a [felügyeleti portált](azure-stack-manage-portals.md) vagy a PowerShellt. Az alapvető fogalmak megismerésének legegyszerűbb módja a portálon keresztül. Ha a PowerShellt szeretné használni, előkészítési lépések szükségesek. Az első lépések előtt érdemes megismerni, hogyan használják a PowerShellt a Azure Stack. További információ: Ismerkedés [a PowerShell-lel a Azure stackon](../user/azure-stack-powershell-overview.md).

A Azure Stack a Azure Resource Manager használja a mögöttes üzembe helyezési, felügyeleti és szervezeti mechanizmusként. Ha Azure Stack fogja kezelni a felhasználókat, és segítségre van szüksége a felhasználók támogatásához, megismerheti a Resource Managert. Tekintse meg a [Első lépések Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmányt.

## <a name="your-typical-responsibilities"></a>Tipikus feladatai

A felhasználók a szolgáltatásokat szeretnék használni. A legfontosabb szerepe, hogy ezek a szolgáltatások elérhetők legyenek a saját szemszögéből. Döntse el, hogy mely szolgáltatásokat szeretné felkínálni, és tegye elérhetővé ezeket a szolgáltatásokat csomagok, ajánlatok és kvóták létrehozásával. További információkért lásd: [az Azure stack ajánlati szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md). 

Emellett elemeket is fel kell vennie [Azure stack Marketplace](azure-stack-marketplace.md)-re. A legegyszerűbb módszer, ha a [Piactéri elemeket az Azure-ból Azure Stackba tölti le](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Ha tesztelni szeretné a csomagjait, ajánlatait és szolgáltatásait, használhatja a [felhasználói portált](azure-stack-manage-portals.md); nem a felügyeleti portálon.

A szolgáltatások biztosítása mellett az operátorok rendszeres kötelességeit is meg kell tennie, hogy Azure Stack a működés fenntartásához. Ezek a feladatok a következő feladatokat foglalják magukban:

- Felhasználói fiókok hozzáadása ( [Azure ad](azure-stack-add-new-user-aad.md) -telepítéshez vagy [AD FS](azure-stack-add-users-adfs.md) központi telepítéshez).
- [Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendeléséhez](azure-stack-manage-permissions.md) (ez a feladat nem korlátozódik a rendszergazdákra.)
- Az [infrastruktúra állapotának figyelése](azure-stack-monitor-health.md).
- A [hálózati](azure-stack-viewing-public-ip-address-consumption.md) és [tárolási](azure-stack-manage-storage-accounts.md) erőforrások kezelése.
- Cserélje le a hibás hardvereket. Például [cserélje le a meghibásodott lemezt](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak

Lehetővé kell tennie, hogy a felhasználók tudják, hogyan dolgozhatnak a Azure Stack szolgáltatásaival, hogyan csatlakozhat a környezethez, és hogyan fizethet elő az ajánlatokra. Minden olyan egyéni dokumentáció mellett, amelyet érdemes megadnia a felhasználók számára, a felhasználókat [Azure stack felhasználói dokumentációhoz](https://docs.microsoft.com/en-us/azure-stack/user/)irányíthatja.

**Ismerje meg, hogyan dolgozhat a szolgáltatásokkal Azure Stack**

A felhasználóknak meg kell érteniük az adatokat, mielőtt a szolgáltatásokat használják, és alkalmazásokat építhetnek ki Azure Stackban. A PowerShell-és API-verzióra vonatkozó követelmények például bizonyosak. Az Azure szolgáltatásban és az azzal egyenértékű szolgáltatásban található szolgáltatások közötti eltérések is léteznek Azure Stackban. Győződjön meg arról, hogy a felhasználók a következő cikkeket tekintik át:

- [Főbb szempontok: szolgáltatások használata vagy alkalmazások készítése Azure Stackhoz](../user/azure-stack-considerations.md)
- [A Azure Stack Virtual Machines szempontjai](../user/azure-stack-vm-considerations.md)
- [Tárolás: különbségek és megfontolások](../user/azure-stack-acs-differences.md)

A cikkben szereplő információk összefoglalják az Azure-beli szolgáltatás és a Azure Stack közötti különbségeket. Kiegészíti az Azure-szolgáltatásokhoz elérhető információkat a globális Azure-dokumentációban.

**Kapcsolódás Azure Stack felhasználóként**

ASDK-környezetben, ha a felhasználó nem használ Távoli asztalt a ASDK-gazdagéphez való csatlakozáshoz, konfigurálhat virtuális magánhálózati (VPN) kapcsolatot a Azure Stackhoz való csatlakozáshoz. Lásd: [kapcsolódás Azure Stackhoz](../asdk/asdk-connect.md).

A felhasználók tudni szeretnék, hogyan [érheti el a felhasználói portált](../user/azure-stack-use-portal.md) , vagy hogyan csatlakozhat a PowerShell-lel. Az integrált rendszerkörnyezetek esetében a felhasználói portál címe az üzemelő példányok számától függ. A megfelelő URL-címmel kell megadnia a felhasználóknak.

A PowerShell használata esetén előfordulhat, hogy a felhasználóknak regisztrálniuk kell az erőforrás-szolgáltatókat, mielőtt használniuk tudják a szolgáltatásokat. Egy erőforrás-szolgáltató kezeli a szolgáltatást. A hálózati erőforrás-szolgáltató például olyan erőforrásokat kezel, mint a virtuális hálózatok, a hálózati adapterek és a terheléselosztó. [Telepíteniük](azure-stack-powershell-install.md) kell a PowerShellt, [le kell tölteniük](azure-stack-powershell-download.md) a további modulokat, és [be kell állítania](../user/azure-stack-powershell-configure-user.md) a PowerShellt (amely magában foglalja az erőforrás-szolgáltató

**Előfizetés egy ajánlatra**

Ahhoz, hogy a felhasználók használhassanak szolgáltatásokat, [elő kell fizetniük](azure-stack-subscribe-plan-provision-vm.md) az operátorként létrehozott ajánlatra.

## <a name="where-to-get-support"></a>Honnan kaphat támogatást?

> [!Note]  
> A Azure Stack korábbi kiadásaival kapcsolatos támogatási információk megkereséséhez (pre-1905) tekintse meg a [korábbi kiadásokkal kapcsolatos súgó és támogatás Azure stack (pre-1905)](azure-stack-servicing-policy.md)című témakört.

### <a name="integrated-systems"></a>Integrált rendszerek

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Ha támogatási kérést szeretne megnyitni, válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában, válassza a **Súgó + támogatás**lehetőséget, majd válassza az **új támogatási kérelem** lehetőséget a **támogatás** szakaszban.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) vagy bármilyen hardveres szoftverrel, például a hardver életciklus-állomásán futó szoftverrel, akkor először lépjen kapcsolatba az OEM hardver gyártójával.

Bármilyen más esetben forduljon a Microsoft CSS-hez.

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack fejlesztői készlet (ASDK)

A ASDK a támogatással kapcsolatos kérdéseket is megteheti a [Microsoft fórumokon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). A fórumok beszerzéséhez válassza a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában, majd válassza a **Súgó + támogatás**lehetőséget, majd válassza az MSDN- **fórumok** lehetőséget a **támogatás** szakaszban. Ezeket a fórumokat rendszeresen figyelik. Mivel a ASDK egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

## <a name="next-steps"></a>Következő lépések

[Területi felügyelet Azure Stack](azure-stack-region-management.md)