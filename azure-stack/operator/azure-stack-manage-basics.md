---
title: Az Azure Stack az Adminisztráció alapjai |} A Microsoft Docs
description: Ismerje meg, mit kell tudni az Azure Stack felügyeletéhez.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8449770d7fa3d42afa8aad790878b29c95be773e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64292221"
---
# <a name="azure-stack-administration-basics"></a>Az Azure Stack az Adminisztráció alapjai
Több szempontot, ha most ismerkedik az Azure Stack felügyeleti ismernie kell. Ez a cikk segítségével értesítheti a felhasználókat a számukra a gyors elindulásban kell, és a szerepkör az Azure Stack operátorait szerint áttekintését.

## <a name="understand-the-builds"></a>A buildek ismertetése

### <a name="integrated-systems"></a>Integrált rendszerek

Használja az Azure Stackkel integrált rendszer, ha frissített verzióit tartalmazza az Azure Stack révén a frissítési csomagok vannak osztva. Importálja ezeket a csomagokat, és alkalmazza azokat a frissítéseket csempe a felügyeleti portál használatával. ASDK telepítések frissítése nem támogatott. 
 
### <a name="development-kit"></a>Fejlesztői készlete

Ha az Azure Stack Development Kit használja, tekintse át a [az Azure Stack?](../asdk/asdk-what-is.md) cikket, ellenőrizze, hogy a fejlesztői készletet, majd korlátatait rendeltetésének megismerése. A csomag egy ", védőfal"mögött, kiértékelheti az Azure Stack, és fejlesztheti és tesztelheti az alkalmazásokat nem éles környezetben célszerű használni. (Üzembe helyezés információkért lásd: a [Azure Stack Development Kit központi telepítési](../asdk/asdk-install.md) cikkben.)

Például az Azure-ban hogy gyors innováció. Új buildek rendszeresen adunk lesz. Ha futtatja a csomagot, és szeretné helyezni a legújabb buildre kell [Azure Stack ismételt üzembe helyezése](../asdk/asdk-redeploy.md). Frissítési csomagokat nem lehet alkalmazni. Ez a folyamat időt vesz igénybe, de az az előnye, hogy a legújabb funkciók kipróbálhatja. A fejlesztői csomag dokumentációjában találhatók a webhelyen tükrözi a legfrissebb kiadott buildjét.

## <a name="learn-about-available-services"></a>Elérhető szolgáltatások ismertetése

Szüksége lesz egy tájékoztatási, mely szolgáltatások elérhetővé teheti a felhasználók számára. Az Azure Stack egy Azure-szolgáltatások részét támogatja. Támogatott szolgáltatások teljes listájának továbbra is fejlesztheti tovább.

**Alapvető szolgáltatások**

Alapértelmezés szerint az Azure Stack tartalmazza a következő "alapvető szolgáltatások" az Azure Stack üzembe helyezésekor:

- Compute
- Storage
- Hálózat
- Key Vault

Alapvető szolgáltatások infrastruktúra--szolgáltatásként (IaaS) elérhetővé teheti a felhasználók minimális konfigurációval.

**További szolgáltatások**

Jelenleg az alábbi kiegészítő Platform--szolgáltatásként (PaaS) szolgáltatásokat:

- App Service
- Azure Functions
- SQL- és MySQL-adatbázisok

Ezek a szolgáltatások akkor is használhatja őket a felhasználóknak további konfigurációt igényelnek. További információkért tekintse meg a "oktatóanyagokban" és az Azure Stack operátori dokumentációja "Útmutató guides\Offer szolgáltatások" szakaszát.

**Szolgáltatás-ütemterv**

Az Azure Stack az Azure-szolgáltatások támogatása továbbra is. A tervezett fejlesztési ütemtervéhez, lásd: a [Azure Stack: Azure-bővítmény](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) tanulmány. Is figyelemmel kísérheti a [blogbejegyzések az Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) új hirdetmények.

## <a name="what-account-should-i-use"></a>Milyen fiókot használjak?
Van néhány fiókokkal kapcsolatos megfontolások érdemes figyelembe vennie, amikor kezelik az Azure Stack. Különösen a központi telepítések segítségével a Windows Server Active Directory összevonási szolgáltatások (AD FS) helyett az Azure Active Directory (Azure AD) identitás-szolgáltatóként. A következő fiókokkal kapcsolatos megfontolások az Azure Stack integrált rendszerek és ASDK üzemelő példányok is vonatkoznak:


|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazda (. \Administrator)|ASDK gazdagép-rendszergazda|ASDK gazdagép-rendszergazda|
|AzureStack\AzureStackAdmin|ASDK gazdagép-rendszergazda<br><br>Jelentkezzen be az Azure Stack felügyeleti portál segítségével<br><br>Megtekintése és kezelése a Service Fabric körök való hozzáférés|ASDK gazdagép-rendszergazda<br><br>Nincs hozzáférés az Azure Stack felügyeleti portálon<br><br>Megtekintése és kezelése a Service Fabric körök való hozzáférés<br><br>Már nem tulajdonosa, az alapértelmezett szolgáltatója előfizetés (DPS)|
|AzureStack\CloudAdmin|Majd futtassa a kiemelt végponthoz belül engedélyezett parancsokat is|Majd futtassa a kiemelt végponthoz belül engedélyezett parancsokat is<br><br>Nem jelentkezhetnek be a ASDK gazdagép<br><br>Az alapértelmezett szolgáltatója (DPS) előfizetés tulajdonosa|
|Az Azure AD globális rendszergazda|A telepítés során használt<br><br>Az alapértelmezett szolgáltatója (DPS) előfizetés tulajdonosa|Nem alkalmazható|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok kezeléséhez?
 
Használhatja a [adminisztrátori portál](azure-stack-manage-portals.md) vagy a PowerShell használatával kezelheti az Azure Stack. Ismerje meg, az alapvető fogalmait a legegyszerűbben a portálon keresztül van. Ha azt szeretné, ha a PowerShell segítségével, nincsenek előkészítési lépéseket. Meg kell [telepítése](azure-stack-powershell-install.md) PowerShell [letöltése](azure-stack-powershell-download.md) további modulok és [konfigurálása](azure-stack-powershell-configure-admin.md) PowerShell.

Az Azure Stack Azure Resource Managert használja az alapul szolgáló telepítés, a felügyeleti és a szervezet mechanizmusként. Megkönnyíti a felhasználók támogatása az Azure Stack kezelését és fog, ha azt a Resource Manager kell ismerteti. Tekintse meg a [első lépései az Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmány.

## <a name="your-typical-responsibilities"></a>A tipikus feladatkörei

A felhasználók érdemes szolgáltatásokat használni. A saját szempontjából a fő szerepkör, hogy ezek a szolgáltatások számára elérhető. Döntse el, melyik szolgáltatásokat kínál, és ezek a szolgáltatások elérhetővé tétele csomagok, ajánlatok és kvóták létrehozásával. További információkért lásd: [az Azure Stackben szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md). 

Az elemek hozzáadását is kell [a Marketplace-en](azure-stack-marketplace.md), például a virtuálisgép-lemezképeket. A legegyszerűbb módja az, hogy [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Ha szeretné tesztelni a csomagok, ajánlatok és szolgáltatásokat, használja a [felhasználói portál](azure-stack-manage-portals.md); nem a felügyeleti portálon.

Amellett, hogy a szolgáltatások, el kell végeznie minden rendszeres feladatai az operátornak, hogy az Azure Stack üzembe helyezéséig. Ebből a célból a következők:

- Felhasználói fiókok hozzáadása (a [Azure Active Directory](azure-stack-add-new-user-aad.md) üzembe helyezési vagy [Active Directory összevonási szolgáltatások](azure-stack-add-users-adfs.md) üzembe helyezés)
- [Szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök hozzárendelése](azure-stack-manage-permissions.md) (Ez a nem korlátozott a rendszergazdáknak.)
- [Infrastruktúra-állapot figyelése](azure-stack-monitor-health.md)
- Kezelése [hálózati](azure-stack-viewing-public-ip-address-consumption.md) és [tárolási](azure-stack-manage-storage-accounts.md) erőforrások
- Cserélje le a hibás hardverek, például [egy meghibásodott lemez cseréjéhez](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak?

Tudassa a felhasználókkal, a szolgáltatások az Azure Stack használatának módját, hogyan csatlakozhat a környezet és előfizetés az ajánlatok kell. Bármilyen egyéni dokumentáció mellett, érdemes lehet a végfelhasználóknak a felhasználók számára az Azure Stack felhasználói dokumentációja hely közvetlen.

**Megtudhatja, hogyan működik az Azure Stack-szolgáltatásokkal**

Nincs információ a felhasználók ismernie kell, mielőtt szolgáltatások és az Azure Stackben alkalmazásokat készíthet. Például nincsenek konkrét PowerShell és az API verziója. Nincsenek is, néhány funkció eltérések közötti egy az Azure-ban és az azzal egyenértékű szolgáltatást az Azure Stackben. Győződjön meg arról, hogy a felhasználók tekintse át a következő cikkeket:

- [Fontos szempontok: Szolgáltatások használatával, vagy alkalmazások készítéséhez az Azure Stackhez](../user/azure-stack-considerations.md)
- [Szempontok a virtuális gépek az Azure Stackben](../user/azure-stack-vm-considerations.md)
- [Storage: különbségek és szempontok](../user/azure-stack-acs-differences.md)

Ezekben a cikkekben található információk az Azure egy szolgáltatása, és az Azure Stack közötti különbségeket foglalja össze. Kiegészíti az adatokat a globális Azure-dokumentáció az Azure-szolgáltatások számára elérhető.

**Csatlakozás az Azure Stackhez felhasználóként**

Development kit környezetben ha egy felhasználó nem rendelkezik a development kit gazdagépre, a távoli asztali hozzáférés, konfigurálnia kell egy virtuális magánhálózati (VPN) kapcsolatot hozzáférhet az Azure Stack. Lásd: [csatlakozás az Azure Stack](../asdk/asdk-connect.md). 

A felhasználók tudni szeretnék majd, hogyan [elérhető a felhasználói portál](../user/azure-stack-use-portal.md) vagy a PowerShell-en keresztül csatlakozni. Integrált rendszerek környezetben a felhasználói portál cím száma üzemelő példányonként változik. Adja meg a felhasználók számára a helyes URL-címet kell.

Ha a PowerShell használatával, a felhasználóknak kell előfordulhat, hogy erőforrás-szolgáltatók regisztrálásával szolgáltatások használatához. (Erőforrás-szolgáltató kezeli a szolgáltatás. For example, a hálózati erőforrás-szolgáltató kezeli az erőforrások, például a virtuális hálózatok, hálózati adapterek és terheléselosztók.) Akkor kell [telepítése](azure-stack-powershell-install.md) PowerShell-lel, [letöltése](azure-stack-powershell-download.md) további modulok és [konfigurálása](../user/azure-stack-powershell-configure-user.md) PowerShell (amely tartalmazza az erőforrás-szolgáltatói regisztráció).

**Előfizetés egy ajánlatra**

Mielőtt egy felhasználó hozzáférhessen a szolgáltatásokhoz, akkor kell [előfizetés egy ajánlatra](azure-stack-subscribe-plan-provision-vm.md) kezelőként létrehozott.

## <a name="where-to-get-support"></a>Hol kérhet támogatást

### <a name="integrated-systems"></a>Integrált rendszerek

Egy integrált rendszer van egy koordinált eszkalációs és a Microsoft és a számítógépgyártó (OEM) hardvergyártó partnereinkkel névfeloldási folyamat.

Ha probléma van a cloud services megfeleléssel, a támogatási keresztül a Microsoft támogatási szolgáltatásokat (CSS) érhető el. Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatási kérelem**, ekkor megnyílik egy helyet, ahol közvetlenül is nyisson egy támogatási kérést.

Ha üzembe helyezés problémáját, javítási és frissítési hardver (beleértve a mező telepen cserélhető egység) és bármely hardver márkanevű szoftverek, például a hardver életciklus gazdagépen futó szoftver forduljon az OEM hardvergyártójához először.

Bármi más forduljon a Microsoft CSS.

### <a name="development-kit"></a>Fejlesztői készlete

A fejlesztői csomag az támogatási kapcsolatos kérdéseit teheti fel az [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatási kérelem**, ekkor megnyílik a fórumok webhelyre közvetlenül. Ezek a fórumok rendszeresen figyeli a program. Mivel a csomag egy kiértékelési környezete, rendszer nem hivatalos támogatja a Microsoft CSS keresztül érhető el.

## <a name="next-steps"></a>További lépések

[Régiók kezelése az Azure Stackben](azure-stack-region-management.md)


