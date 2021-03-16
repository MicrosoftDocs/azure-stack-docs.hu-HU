---
title: A Visual Studio telepítése és kapcsolódás Azure Stack hubhoz
description: Ismerje meg, hogyan telepítheti a Visual studiót, és hogyan csatlakozhat Azure Stack hubhoz.
author: sethmanheim
ms.topic: article
ms.date: 03/12/2021
ms.author: sethm
ms.reviewer: raymondl
ms.lastreviewed: 01/04/2020
ms.openlocfilehash: cd8f3c7f8d8f47ab6b99f6b96a6180b849b04e27
ms.sourcegitcommit: 1a8175cf19e1472b24f89eb8bad35f5ec64fab34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472612"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>A Visual Studio telepítése és kapcsolódás Azure Stack hubhoz

A Visual Studióval Azure Resource Manager [sablonokat](azure-stack-arm-templates.md) írhat és telepíthet Azure stack hubhoz. A cikkben ismertetett lépések bemutatják, hogyan telepítheti a Visual studiót [Azure stack hubhoz](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) vagy külső számítógépre, ha a Azure stack hub [-t VPN-en](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)keresztül tervezi használni.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

1. [Telepítse a Visual Studio Community 2019](https://visualstudio.microsoft.com/)-es verzióját vagy a próbaverziók egyikét.  

2. Távolítsa el az Azure SDK részeként telepített **Microsoft Azure PowerShell** .

    ![Képernyőkép a WebPI telepítési lépéseiről](./media/azure-stack-install-visual-studio/uninstall-powershell.png)

3. [Telepítse a powershellt Azure stack hubhoz](../operator/powershell-install-az-module.md).

4. A telepítés befejezése után indítsa újra a számítógépet.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Kapcsolódás Azure Stack hubhoz az Azure AD-vel

1. Indítsa el a Visual studiót.

2. A **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

3. Válassza a **fiókok kezelése** lehetőséget, majd az új ablaktáblán jelentkezzen be Azure Active Directory (Azure ad) hitelesítő adataival.  

    ![Képernyőfelvétel: a Cloud Explorer a bejelentkezett és Azure Stack hubhoz való csatlakozás után a thas mutatja](./media/azure-stack-install-visual-studio/sign-in-visual-studio.png)

A bejelentkezést követően [sablonok üzembe helyezésével](azure-stack-deploy-template-visual-studio.md) vagy az elérhető erőforrástípusok és erőforráscsoportok tallózásával hozhatja létre saját sablonjait.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Kapcsolódás Azure Stack hubhoz AD FS

1. Indítsa el a Visual studiót.

2. Az **eszközök** területen válassza a **Beállítások lehetőséget**.

3. Bontsa ki a **környezet** elemet a navigációs ablaktáblán, és válassza a **fiókok** lehetőséget.

4. Válassza a **Hozzáadás** lehetőséget, majd adja meg a felhasználói Azure Resource Manger-végpontot. A Azure Stack Development Kit (ASDK) esetében az URL-cím a következő: `https://management.local.azurestack/external` .  Azure Stack hub integrált rendszerek esetében az URL-cím a következő: `https://management.[Region}.[External FQDN]` .

    ![Új Azure Cloud Discovery-végpont hozzáadása](./media/azure-stack-install-visual-studio/add-cloud.png)

5. Válassza a **Hozzáadás** lehetőséget. A Visual Studio meghívja az Azure Resource Mangert, és felfedi a végpontokat, beleértve a hitelesítési végpontot az Azure Directory összevont szolgáltatások (AD FS) számára. Azure Stack hub most megjelenik a regisztrált felhők listájában.

6. A **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

7. Válassza a **fiókok kezelése** lehetőséget, és jelentkezzen be a AD FS hitelesítő adataival.  

    ![Bejelentkezés a Visual studióba a Cloud Explorerben](./media/azure-stack-install-visual-studio/sign-in-visual-studio.png)

    A Cloud Explorer lekérdezi az elérhető előfizetéseket. Kiválaszthatja a felügyelni kívánt elérhető előfizetést.

    ![A Cloud Explorerben kezelendő előfizetések kiválasztása](./media/azure-stack-install-visual-studio/image8.png)

8. Tallózással keresse meg a meglévő erőforrásokat, erőforráscsoportokat vagy telepítse a sablonokat.

## <a name="next-steps"></a>Következő lépések

- További információ a Visual Studio [oldaláról](/visualstudio/install/install-visual-studio-versions-side-by-side) más Visual Studio-verziókkal.
- [Sablonok fejlesztése Azure stack hub számára](azure-stack-develop-templates.md).
