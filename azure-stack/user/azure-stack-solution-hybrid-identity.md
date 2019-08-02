---
title: Hibrid Felhőbeli identitás konfigurálása az Azure-ban és a Azure Stack-alkalmazásokban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a hibrid Felhőbeli identitásokat az Azure-ban és Azure Stack alkalmazásokkal.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: bc614cb514b9d35523749944486aa375bb8fce9c
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603011"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Hibrid felhőalapú identitás konfigurálása az Azure-hoz és Azure Stack-alkalmazásokhoz

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Megtudhatja, hogyan konfigurálhat hibrid Felhőbeli identitást Azure-beli és Azure Stack-alkalmazásaihoz.

Két lehetősége van arra, hogy a globális Azure-ban és Azure Stackban is hozzáférjen az alkalmazásaihoz.

 * Ha Azure Stack folyamatos internetkapcsolattal rendelkezik, használhatja a Azure Active Directory (Azure AD).
 * Ha Azure Stack le van választva az internetről, használhatja az Azure Directory összevont szolgáltatások (AD FS) szolgáltatást.

Az egyszerű szolgáltatások segítségével a Azure Stack-Azure Resource Manager használatával biztosíthatja a hozzáférést a Azure Stack alkalmazásokhoz a telepítéshez vagy a konfiguráláshoz.

Ebben a megoldásban egy példaként szolgáló környezetet fog kiépíteni a következőkre:

> [!div class="checklist"]
> - Hibrid identitás létrehozása a globális Azure-ban és Azure Stack
> - Token beolvasása a Azure Stack API eléréséhez.

Ennek a megoldásnak a lépéseihez Azure Stack operátor engedélyekkel kell rendelkeznie.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Azure Stack a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](azure-stack-edge-pattern-overview.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Egyszerű szolgáltatásnév létrehozása az Azure AD-hez a portálon

Ha a Azure Stack az Azure AD-t az Identity Store-ban telepítette, akkor az Azure-hoz hasonló egyszerű szolgáltatásokat hozhat létre. Az [erőforrások eléréséhez használt alkalmazás-identitással](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) megtudhatja, hogyan hajthatja végre a lépéseket a portálon. A Kezdés előtt győződjön meg arról, hogy rendelkezik a [szükséges Azure ad](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) -engedélyekkel.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Egyszerű szolgáltatásnév létrehozása AD FShoz a PowerShell használatával

Ha a Azure Stackt AD FS használatával telepítette, akkor a PowerShell segítségével létrehozhat egy egyszerű szolgáltatásnevet, hozzárendelhet egy szerepkört a hozzáféréshez, és bejelentkezhet a PowerShellből az identitás használatával. Az [erőforrások eléréséhez használt alkalmazás-identitással](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) megtudhatja, hogyan hajthatja végre a szükséges lépéseket a PowerShell használatával.

## <a name="using-the-azure-stack-api"></a>A Azure Stack API használata

A [Azure stack API](azure-stack-rest-api-use.md) -megoldás végigvezeti a token lekérésének folyamatán a Azure stack API eléréséhez.

## <a name="connect-to-azure-stack-using-powershell"></a>Kapcsolódás Azure Stack a PowerShell használatával

Az [Azure stack PowerShell-](../operator/azure-stack-powershell-install.md) lel megkezdhető gyors útmutató végigvezeti a Azure PowerShell telepítéséhez és a Azure stack telepítéshez való kapcsolódáshoz szükséges lépéseken.

### <a name="prerequisites"></a>Előfeltételek

Szüksége van egy Azure Stack-telepítésre, amely a Azure Active Directoryhoz csatlakozik, amelyhez előfizetéssel férhet hozzá. Ha nem rendelkezik Azure Stack-telepítéssel, a következő utasításokat követve állíthatja be a [Azure stack Development Kit](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Kapcsolódás Azure Stack kód használatával

Ha kódot használ a Azure Stackhoz való kapcsolódáshoz, használja a Azure Resource Manager endpoints API-t a Azure Stack telepítésének hitelesítési és gráf végpontjának lekéréséhez, majd a REST-kérelmek használatával történő hitelesítéshez. A githubon megtalálhatja a minta [](https://github.com/shriramnat/HybridARMApplication)ügyfélalkalmazás alkalmazást.

>[!Note]
>Ha a választott nyelvhez készült Azure SDK támogatja az Azure API-profilokat, előfordulhat, hogy az SDK nem működik együtt Azure Stack. Az Azure API-profilokkal kapcsolatos további tudnivalókért tekintse meg az [API-verzió profiljainak kezelése](azure-stack-version-profiles.md) című cikket.

## <a name="next-steps"></a>További lépések

 - Ha többet szeretne megtudni arról, hogyan történik az identitás kezelése Azure Stackban, tekintse meg a [Azure stack Identity Architecture](../operator/azure-stack-identity-architecture.md)című témakört.
 - Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
