---
title: Hibrid felhőbeli identitás konfigurálása az Azure és az Azure Stack alkalmazásokkal |} A Microsoft Docs
description: Ismerje meg, hogy hibrid felhőbeli identitás konfigurálása az Azure és az Azure Stack-alkalmazásokkal.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 074d971c1f951797b5dc2d53a62eef56d0b7249f
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492326"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Oktatóanyag: A hibrid felhőbeli identitás az Azure és az Azure Stack-alkalmazások konfigurálása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, a hibrid felhőbeli identitás az Azure és az Azure Stack-alkalmazások konfigurálása.

Az alkalmazások globális Azure-beli és az Azure Stack való hozzáférést két lehetősége van.

 * Amikor az Azure Stack egy folyamatos internetkapcsolattal rendelkezik, az Azure Active Directory (Azure AD) is használhatja.
 * Ha az Azure Stack nem kapcsolódik az internethez, használhatja az Azure Directory összevont szolgáltatások (AD FS).

A szolgáltatásnevek használatával az Azure Stack-alkalmazások üzembe helyezési vagy az Azure Resource Manager használatával az Azure Stack-konfigurációhoz való hozzáférést biztosít.

Ebben az oktatóanyagban egy mintául szolgáló környezet építi fel:

> [!div class="checklist"]
> - A globális Azure és az Azure Stack egy hibrid identitás létrehozása
> - Az Azure Stack API eléréséhez jogkivonat beszerzésére.

Ez az oktatóanyag lépéseit az Azure Stack operátori kell rendelkeznie.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack biztosítható a hatékonyságával és innovációjával emeli felhő-számítástechnika a helyszíni környezetben, amely lehetővé teszi, hogy a hibrid alkalmazások bárhol üzembe helyezése az egyetlen olyan hibrid felhős engedélyezése.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) áttekinti a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) a kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>A portálon az Azure ad egyszerű szolgáltatás létrehozása

Ha üzembe helyezte az Azure AD az ügyfélidentitás-tárolóval, mint az Azure Stack, létrehozhat egyszerű szolgáltatásokat, mint az Azure-ban végezhet el. [Egy alkalmazás-identitást erőforrások eléréséhez](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) bemutatja, hogyan hajtsa végre a lépéseket a portálon keresztül. Ellenőrizze, hogy rendelkezik a [szükséges Azure AD-engedélyekről](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) megkezdése előtt.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Hozzon létre egy egyszerű szolgáltatást az AD FS Szolgáltatásokhoz a PowerShell használatával

Ha telepítette az AD FS az Azure Stack, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, hozzáférés szerepkör hozzárendelése és jelentkezzen be a PowerShell használatával az identitásukat. [Egy alkalmazás-identitást erőforrások eléréséhez](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) bemutatja, hogyan végezheti el a PowerShell-lel szükséges lépéseket.

## <a name="using-the-azure-stack-api"></a>Az API-t az Azure Stack használatával

A [Azure Stack API](azure-stack-rest-api-use.md) az oktatóanyag végigvezeti egy tokent az Azure Stack API eléréséhez lekérését jelenti.

## <a name="connect-to-azure-stack-using-powershell"></a>Csatlakozhat az Azure Stack Powershell-lel

Ez a rövid útmutató [az első lépésekhez a PowerShell-lel az Azure Stackben](../operator/azure-stack-powershell-install.md) végigvezeti Önt az Azure PowerShell telepítése és csatlakozás az Azure Stack-telepítés szükséges lépéseket.

### <a name="prerequisites"></a>Előfeltételek

Azure Stack-előfizetéssel is elérheti az Azure Active Directoryhoz csatlakoztatott telepítve van szüksége. Ha egy Azure Stack-telepítés nem rendelkezik, ezek az utasítások beállításához használhatja egy [Azure Stack Development Kit](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Azure Stack-kód használatával csatlakozhat.

Azure Stack-kód használatával csatlakozhat, használja az Azure Resource Manager-végpontok API, a hitelesítés és a graph-végpont lekérése az Azure Stack telepítése, és hitelesítse a REST-kérelmek használatával. Egy mintaalkalmazás ügyfél találhat [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Tetszőleges nyelven készült Azure SDK támogatja az Azure API-profilok, hacsak az SDK nem működnek az Azure Stack használatával. Az Azure API-profilokkal kapcsolatos további tudnivalókért tekintse meg a [API-verzióprofilok kezelése](azure-stack-version-profiles.md) cikk.

## <a name="next-steps"></a>További lépések

 - Hogyan kezeli az Azure Stackben identitás kapcsolatos további információkért lásd: [identitásarchitektúra az Azure Stackhez készült](../operator/azure-stack-identity-architecture.md).
 - Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
