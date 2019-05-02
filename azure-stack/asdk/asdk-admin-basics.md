---
title: Az Azure Stack Development Kit alapjai |} A Microsoft Docs
description: Ismerteti, hogyan hajthat végre alapszintű felügyeleti feladatokat a az Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 4dc4f1696266234d5aaaa14f27fbf4a0ba458108
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290010"
---
# <a name="asdk-administration-basics"></a>ASDK Adminisztráció alapjai 
Több szempontot, ha most ismerkedik az Azure Stack Development Kit (ASDK) felügyeleti ismernie kell. Ez az útmutató a szerepkör, a kiértékelési környezet az Azure Stack operátorait áttekintést nyújt, és hogyan biztosítson a tesztfelhasználók gyorsan hatékony válhat.

Első lépésként tekintse át a [Mi az Azure Stack Development Kit?](asdk-what-is.md) cikket, hogy biztosan a ASDK és korlátatait rendeltetésének megismerése. Használja a csomag egy ", védőfal"mögött, ahol kiértékelheti az Azure Stack-alkalmazások fejlesztése és tesztelése a nem éles környezetben való. 

Azure-ban, mint például az Azure Stack innovates gyorsan úgy fogjuk rendszeresen adunk a ASDK új buildek. Azonban a ASDK nem tudja frissíteni, mint az Azure Stackkel integrált rendszerek üzemelő példányok. Ezért, ha szeretné helyezni a legújabb buildre, meg kell teljesen [ismételt üzembe helyezése a ASDK](asdk-redeploy.md). Frissítési csomagokat nem lehet alkalmazni. Ez a folyamat időt vesz igénybe, de az az előnye, hogy kipróbálni a legújabb funkciók, amint azok elérhetővé válnak. 

## <a name="what-account-should-i-use"></a>Milyen fiókot használjak?
Van néhány fiókokkal kapcsolatos megfontolások érdemes figyelembe vennie, amikor kezelik az Azure Stack. Különösen a központi telepítések segítségével a Windows Server Active Directory összevonási szolgáltatások (AD FS) helyett az Azure Active Directory (Azure AD) identitás-szolgáltatóként. A következő fiókokkal kapcsolatos megfontolások az Azure Stack integrált rendszerek és ASDK üzemelő példányok is vonatkoznak:

|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazda (. \Administrator)|ASDK gazdagép-rendszergazda|ASDK gazdagép-rendszergazda|
|AzureStack\AzureStackAdmin|ASDK gazdagép-rendszergazda<br><br>Jelentkezzen be az Azure Stack felügyeleti portál segítségével<br><br>Megtekintése és kezelése a Service Fabric körök való hozzáférés|ASDK gazdagép-rendszergazda<br><br>Nincs hozzáférés az Azure Stack felügyeleti portálon<br><br>Megtekintése és kezelése a Service Fabric körök való hozzáférés<br><br>Már nem tulajdonosa, az alapértelmezett szolgáltatója előfizetés (DPS)|
|AzureStack\CloudAdmin|Majd futtassa a kiemelt végponthoz belül engedélyezett parancsokat is|Majd futtassa a kiemelt végponthoz belül engedélyezett parancsokat is<br><br>A ASDK gazdagép nem tud bejelentkezni<br><br>Az alapértelmezett szolgáltatója (DPS) előfizetés tulajdonosa|
|Az Azure AD globális rendszergazda|A telepítés során használt<br><br>Az alapértelmezett szolgáltatója (DPS) előfizetés tulajdonosa|Nem alkalmazható|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközöket használhatok kezeléséhez?
Használhatja a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external) vagy a PowerShell használatával kezelheti az Azure Stack. Ismerje meg, az alapvető fogalmait a legegyszerűbben a portálon keresztül van. Ha azt szeretné, ha a PowerShell segítségével, telepítenie kell [az Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) és [az Azure Stack-eszközök letöltése a githubról](asdk-post-deploy.md#download-the-azure-stack-tools).

Az Azure Stack Azure Resource Managert használja az alapul szolgáló telepítés, a felügyeleti és a szervezet mechanizmusként. Megkönnyíti a felhasználók támogatása az Azure Stack kezelését és fog, ha Azure Resource Managerről kell bemutatja. További olvassa el a [Ismerkedés az Azure Resource Manager-tanulmány](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>A tipikus feladatkörei
A felhasználók érdemes szolgáltatásokat használni. A saját szempontjából a fő szerepkör, hogy ezek a szolgáltatások számára elérhető. Használja a ASDK, megtudhatja, hogy mely szolgáltatásokat kínálnak, valamint hogyan javíthatja azokat, szolgáltatásokhoz által elérhető [csomagok, ajánlatok és kvóták létrehozása](../operator/azure-stack-tutorial-tenant-vm.md). Emellett szüksége elemek hozzáadása a Marketplace-en, például a virtuálisgép-lemezképeket. A legegyszerűbb módja az, hogy [Piactéri termékek letöltése](../operator/azure-stack-create-and-publish-marketplace-item.md) az Azure Stack az Azure-ból.

> [!NOTE]
> Ha szeretné tesztelni a csomagok, ajánlatok és szolgáltatásokat, használja a [felhasználói portál](https://portal.local.azurestack.external); nem a [adminisztrátori portál](https://adminportal.local.azurestack.external).

Amellett, hogy a szolgáltatások, el kell végeznie minden rendszeres feladatait az Azure Stack-operátorokról, hogy a ASDK helyezheti üzembe. E feladatok közé tartozik többek között a következőket:
- Adja hozzá a felhasználói fiókok Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) központi telepítések.
- Rendelje hozzá a szerepköralapú hozzáférés-vezérlés (RBAC) szerepkört (ez nem korlátozódik csak rendszergazdák)
- Infrastruktúra-állapot figyelése
- Hálózati és tárolási erőforrások kezelése
- Sikertelen development kit gazdagép számítógép hardver cseréje 

## <a name="where-to-get-support"></a>Hol kérhet támogatást
A fejlesztői csomag az egyetlen támogatási lehetősége a támogatási kapcsolatos kérdéseket tehet fel a [a Microsoft Azure Stack-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatási kérelem**, ekkor megnyílik a fórumok webhelyre közvetlenül. Ezek a fórumok rendszeresen figyeli a program. 

> [!IMPORTANT]
> Mivel a ASDK kiértékelési környezet, rendszer nem érhető el – a Microsoft támogatja a szolgáltatások (CSS) hivatalos támogatja.

## <a name="next-steps"></a>További lépések
[A ASDK üzembe helyezése](asdk-install.md)

