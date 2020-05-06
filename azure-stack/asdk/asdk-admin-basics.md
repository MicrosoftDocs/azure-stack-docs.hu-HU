---
title: ASDK-adminisztrátor alapjai
description: Ismerje meg, hogyan végezheti el az alapszintű rendszergazdai feladatokat a Azure Stack Development Kithoz (ASDK).
author: justinha
ms.topic: article
ms.date: 02/19/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 75e20d593f1fb07e55655a7128c12b5417835dfe
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82846891"
---
# <a name="asdk-admin-basics"></a>ASDK-adminisztrátor alapjai
Több dolgot kell tudnia, ha még nem ismeri a Azure Stack Development Kit (ASDK) felügyeletét. Ez az útmutató áttekintést nyújt a szerepkörről Azure Stack operátorként a kiértékelési környezetben. Az adatok megismerésével biztosíthatja, hogy a felhasználók a lehető leggyorsabban hatékonyak legyenek.

Először tekintse át a [Mi az Azure stack Development Kit?](asdk-what-is.md) című cikket, és ellenőrizze, hogy megértette-e a ASDK és annak korlátozásait. A fejlesztői készletet "homokozó"-ként kell használni, ahol kiértékelheti Azure Stack az alkalmazások fejlesztéséhez és teszteléséhez nem éles környezetben. 

Az Azure-hoz hasonlóan a Azure Stack gyorsan újítja meg, hogy a ASDK új buildeit rendszeresen kiadjuk. A ASDK azonban nem tudja frissíteni, mint Azure Stack integrált rendszerek központi telepítéseit. Ha tehát a legújabb buildre szeretne áttérni, teljesen újra kell [telepítenie a ASDK](asdk-redeploy.md). A frissítési csomagok nem alkalmazhatók. Ez a folyamat időt vesz igénybe, de az előnye, hogy azonnal kipróbálhatja a legújabb funkciókat, amint azok elérhetővé válnak. 

## <a name="what-account-should-i-use"></a>Milyen fiókot használok?
Az Azure Stack kezelésekor figyelembe kell vennie néhány fiókra vonatkozó szempontot. Ez különösen igaz a Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszerű, Azure Active Directory (Azure AD) helyett identitás-szolgáltatóként történő központi telepítések esetén. A következő fiókokra vonatkozó megfontolások a Azure Stack integrált rendszerekre és a ASDK üzemelő példányokra egyaránt érvényesek:

|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazda (.\Rendszergazda)|ASDK-gazda rendszergazdája|ASDK-gazda rendszergazdája|
|AzureStack\AzureStackAdmin|ASDK-gazda rendszergazdája<br><br>A Azure Stack felügyeleti portálra való bejelentkezéshez használható.<br><br>Hozzáférés Service Fabric gyűrűk megtekintéséhez és felügyeletéhez|ASDK-gazda rendszergazdája<br><br>Nincs hozzáférése a Azure Stack felügyeleti portálhoz<br><br>Hozzáférés Service Fabric gyűrűk megtekintéséhez és felügyeletéhez<br><br>Az alapértelmezett szolgáltatói előfizetés (DPS) már nem tulajdonosa|
|AzureStack\CloudAdmin|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat|A jogosultsági szintű végponton belül elérheti és futtathatja az engedélyezett parancsokat<br><br>Nem lehet bejelentkezni a ASDK-gazdagépre<br><br>Az alapértelmezett szolgáltatói előfizetés tulajdonosa (DPS)|
|Az Azure AD globális rendszergazdája|A telepítés során használatban van<br><br>Az alapértelmezett szolgáltatói előfizetés tulajdonosa (DPS)|Nem alkalmazható|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok a felügyelethez?
A Azure Stack kezelésére a Azure Stack felügyeleti portál `https://adminportal.local.azurestack.external` vagy a PowerShell használható. Az alapvető fogalmak megismerésének legegyszerűbb módja a portálon keresztül. Ha a PowerShellt szeretné használni, telepítenie kell a [PowerShellt a Azure Stackhoz](asdk-post-deploy.md#install-azure-stack-powershell) , és [le kell töltenie a Azure stack eszközöket a githubról](asdk-post-deploy.md#download-the-azure-stack-tools).

A Azure Stack a Azure Resource Manager használja a mögöttes üzembe helyezési, felügyeleti és szervezeti mechanizmusként. Ha Azure Stack fogja kezelni a felhasználókat, és segítségre van szüksége a felhasználók támogatásához, ismerkedjen meg a Azure Resource Managerokkal. További információt a Első lépések Azure Resource Manager tanulmányban való elolvasásával [foglalkozó](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)témakörben olvashat.

## <a name="your-typical-responsibilities"></a>Tipikus feladatai
A felhasználók a szolgáltatásokat szeretnék használni. A legfontosabb szerepe, hogy ezek a szolgáltatások elérhetők legyenek a saját szemszögéből. A ASDK segítségével megtudhatja, hogy mely szolgáltatásokat ajánljuk, és hogyan teheti elérhetővé ezeket a szolgáltatásokat [csomagok, ajánlatok és kvóták létrehozásával](../operator/azure-stack-tutorial-tenant-vm.md). Emellett elemeket is hozzá kell adnia a piactérhez, például a virtuális gépekhez (VM). A legegyszerűbb módszer, ha a [Piactéri elemeket](../operator/azure-stack-create-and-publish-marketplace-item.md) az Azure-ból Azure Stackba tölti le.

> [!NOTE]
> Ha tesztelni szeretné a csomagjait, ajánlatait és szolgáltatásait, használja a felhasználói portált `https://portal.local.azurestack.external`; nem a felügyeleti portálon `https://adminportal.local.azurestack.external`.

A szolgáltatások biztosítása mellett gondoskodnia kell arról, hogy az Azure Stack-kezelő minden rendszeres kötelessége a ASDK működésének megőrzése érdekében. Ezek a feladatok a következőket tartalmazzák:
- Felhasználói fiókokat adhat hozzá az Azure AD-hez vagy AD FS-példányokhoz.
- Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendeléséhez (ez nem korlátozódik csak a rendszergazdákra).
- Az infrastruktúra állapotának figyelése.
- A hálózati és tárolási erőforrások kezelése.
- Cserélje le a sikertelen fejlesztői készlet gazdagépének hardverét.

## <a name="where-to-get-support"></a>Honnan kaphat támogatást?
A ASDK a támogatással kapcsolatos kérdéseket a [Azure stack MSDN fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)teheti meg.

A fórumokat a felügyeleti portál jobb felső sarkában található **Súgó** (kérdőjel) lehetőségre kattintva is elérheti. Ezután kattintson a **Súgó + támogatás** lehetőségre a Súgó és támogatás **áttekintésének**megnyitásához, amely a fórumra mutató hivatkozással rendelkezik. Az MSDN-fórumok rendszeres figyelése.  

> [!IMPORTANT]
> Mivel a ASDK egy kiértékelési környezet, a Microsoft ügyfélszolgálati szolgálatai (CSS) nem kínálunk hivatalos támogatást.

## <a name="next-steps"></a>További lépések
[A ASDK üzembe helyezése](asdk-install.md)

