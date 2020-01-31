---
title: A Visual Studio telepítése és kapcsolódás Azure Stack hubhoz
description: Ismerje meg, hogyan telepítheti a Visual studiót, és hogyan csatlakozhat Azure Stack hubhoz.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7c5183c88a973756685c5831d10d4f5c6aeb2ce0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883669"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>A Visual Studio telepítése és kapcsolódás Azure Stack hubhoz

A Visual Studióval Azure Resource Manager [sablonokat](azure-stack-arm-templates.md) írhat és telepíthet Azure stack hubhoz. A cikkben ismertetett lépések bemutatják, hogyan telepítheti a Visual studiót [Azure stack hubhoz](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) vagy külső számítógépre, ha a Azure stack hub [-t VPN-en](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)keresztül tervezi használni.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

1. Töltse le és futtassa a [webplatform-telepítőt](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Nyissa meg a **Microsoft webplatform-telepítőt**.

3. Keressen rá a **Visual Studio Community 2015-re Microsoft Azure SDK-2.9.6**. Kattintson a **Hozzáadás**, majd a **telepítés**gombra.

4. Távolítsa el az Azure SDK részeként telepített **Microsoft Azure PowerShell** .

    ![Képernyőkép a WebPI telepítési lépéseiről](./media/azure-stack-install-visual-studio/image1.png)

5. [Telepítse a powershellt Azure stack hubhoz](../operator/azure-stack-powershell-install.md).

6. A telepítés befejezése után indítsa újra az operációs rendszert.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Kapcsolódás Azure Stack hubhoz az Azure AD-vel

1. Indítsa el a Visual studiót.

2. A **nézet** menüben válassza a **Cloud Explorer**lehetőséget.

3. Az új panelen válassza a **fiók hozzáadása** lehetőséget, és jelentkezzen be a Azure Active Directory (Azure ad) hitelesítő adataival.  

    ![A Cloud Explorer képernyőképe a bejelentkezés után és a Azure Stack hubhoz való csatlakozáskor](./media/azure-stack-install-visual-studio/image2.png)

A bejelentkezést követően [sablonok üzembe helyezésével](azure-stack-deploy-template-visual-studio.md) vagy az elérhető erőforrástípusok és erőforráscsoportok tallózásával hozhatja létre saját sablonjait.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Kapcsolódás Azure Stack hubhoz AD FS

1. Indítsa el a Visual studiót.

2. Az **eszközök**területen válassza a **Beállítások lehetőséget**.

3. Bontsa ki a **környezet** elemet a **navigációs ablaktáblán** , és válassza a **fiókok**lehetőséget.

4. Válassza a **Hozzáadás**lehetőséget, majd adja meg a felhasználói Azure Resource Manger-végpontot. A Azure Stack Development Kit (ASDK) URL-címe: `https://management.local.azurestack/external`.  Azure Stack hub integrált rendszerek esetében az URL-cím a következő: `https://management.[Region}.[External FQDN]`.

    ![Új Azure Cloud Discovery-végpont hozzáadása](./media/azure-stack-install-visual-studio/image5.png)

5. Válassza a **Hozzáadás** lehetőséget.  

    A Visual Studio meghívja az Azure Resource Mangert, és felfedi a végpontokat, beleértve a hitelesítési végpontot az Azure Directory összevont szolgáltatások (AD FS) számára.

    ![A Cloud Explorer képernyőképe a bejelentkezés után és a Azure Stack hubhoz való csatlakozáskor](./media/azure-stack-install-visual-studio/image6.png)

6. A **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

7. Válassza a **fiók hozzáadása** lehetőséget, és jelentkezzen be a AD FS hitelesítő adataival.  

    ![Bejelentkezés a Visual studióba a Cloud Explorerben](./media/azure-stack-install-visual-studio/image7.png)

    A Cloud Explorer lekérdezi az elérhető előfizetéseket. Kiválaszthatja a felügyelni kívánt elérhető előfizetést.

    ![A Cloud Explorerben kezelendő előfizetések kiválasztása](./media/azure-stack-install-visual-studio/image8.png)

8. Tallózással keresse meg a meglévő erőforrásokat, erőforráscsoportokat vagy telepítse a sablonokat.

## <a name="next-steps"></a>Következő lépések

- További információ a Visual Studio [oldaláról](/visualstudio/install/install-visual-studio-versions-side-by-side) más Visual Studio-verziókkal.
- [Sablonok fejlesztése Azure stack hub számára](azure-stack-develop-templates.md).
