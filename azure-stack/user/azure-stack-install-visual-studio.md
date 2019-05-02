---
title: A Visual Studio telepítése és csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, a Visual Studio telepítése és csatlakozás az Azure Stack szükséges lépések
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 11d389b96ed730d6395231ecf24eced6a65fbae8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64302594"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>A Visual Studio telepítése és csatlakozás az Azure Stack

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A Visual Studio használatával és üzembe helyezését az Azure Resource Manager [sablonok](azure-stack-arm-templates.md) az Azure Stackhez. A jelen cikkben ismertetett lépések azt ismertetik, hogyan telepíthető a Visual Studio [Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), vagy egy külső számítógépen, ha azt tervezi, használja az Azure Stack segítségével [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>A Visual Studio telepítése

1. Töltse le és futtassa a [Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Nyissa meg a **Microsoft Webplatform-telepítő**.

3. Keresse meg **a Visual Studio Community 2015 Microsoft Azure SDK - 2.9.6**. Kattintson a **hozzáadása**, és **telepítése**.

4. Távolítsa el a **a Microsoft Azure PowerShell** részeként az Azure SDK telepítve van.

    ![Képernyőkép a WebPI-telepítés lépések](./media/azure-stack-install-visual-studio/image1.png)

5. [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)

6. Indítsa újra az operációs rendszer, a telepítés befejezése után.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Csatlakozás az Azure Stack az Azure ad-vel

1. Indítsa el a Visual Studiót.

2. Az a **nézet** menüjében válassza **Cloud Explorer**.

3. Jelölje ki az új ablaktáblán **fiók hozzáadása** , és jelentkezzen be az Azure Active Directory (Azure AD) hitelesítő adataival.  

    ![Képernyőfelvétel a Cloud Explorer egyszer bejelentkezett, és csatlakozik az Azure Stackben](./media/azure-stack-install-visual-studio/image2.png)

Miután bejelentkezett, is [sablonok üzembe helyezése](azure-stack-deploy-template-visual-studio.md) vagy tallózással keresse meg az elérhető erőforrástípusok és -erőforráscsoportok saját sablonok létrehozásához.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Csatlakozás az Azure Stack az AD FS-sel

1. Indítsa el a Visual Studiót.

2. A **eszközök**válassza **beállítások**.

3. Bontsa ki a **környezet** a a **navigációs** válassza **fiókok**.

4. Válassza ki **Hozzáadás**, és adja meg a felhasználó Azure Resource Manager-végpontot. Az Azure Stack Development Kit URL-je: `https://management.local.azurestack/external`.  Az Azure Stack integrált rendszerek, az URL-je: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Válassza a **Hozzáadás** lehetőséget.  

    A Visual Studio Azure Resource Manager-hívások, és felderíti a végpont, a hitelesítési végpont, beleértve az Azure Directory összevont szolgáltatások (AD FS).

    ![Képernyőfelvétel a Cloud Explorer egyszer bejelentkezett, és csatlakozik az Azure Stackben](./media/azure-stack-install-visual-studio/image6.png)

6. Válassza ki **Cloud Explorer** származó a **nézet** menü.

7. Válassza ki **fiók hozzáadása** , és jelentkezzen be az AD FS hitelesítő adataival.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer lekérdezi az elérhető előfizetésekkel. Kiválaszthat egy érhető el előfizetés kezelésére.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Keresse meg a meglévő erőforrások, erőforráscsoportok vagy sablonok üzembe helyezése.

## <a name="next-steps"></a>További lépések

- További információk a Visual Studio [egymás mellett](/visualstudio/install/install-visual-studio-versions-side-by-side) egyéb Visual Studio-verziókkal.
- [Sablonok fejlesztése az Azure Stack](azure-stack-develop-templates.md).