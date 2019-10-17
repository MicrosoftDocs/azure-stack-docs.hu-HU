---
title: Storage Explorer összekötése egy Azure Stack-előfizetéssel vagy egy Storage-fiókkal | Microsoft Docs
description: Útmutató a Storage Explorer Azure Stack-előfizetéshez való összekapcsolásához
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 454fe5b07dc5576cecdb11b59e5424e3c5ccbb72
ms.sourcegitcommit: df20662e77a6ed0a7eba03f79eb53e8cd4471206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445379"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>A Storage Explorer összekötése egy Azure Stack-előfizetéssel vagy egy Storage-fiókkal

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Azure Stack-előfizetésekhez és a Storage-fiókokhoz a [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)használatával. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy könnyedén működjön a Azure Stack Storage-beli adattárolási szolgáltatással Windows, macOS és Linux rendszeren.

> [!NOTE]  
> Több eszköz is rendelkezésre áll az adatok Azure Stack tárolóba való áthelyezéséhez. További információ: [adatátviteli eszközök Azure stack Storage](azure-stack-storage-transfer.md)szolgáltatáshoz.

Ha még nincs telepítve, [töltse le Storage Explorer](https://www.storageexplorer.com/) és telepítse.

Miután kapcsolódott egy Azure Stack előfizetéshez vagy a Storage-fiókhoz, a [Azure Storage Explorer cikkek](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával dolgozhat a Azure stack adataival. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Felkészülés a Azure Stackhoz való csatlakozásra

A Azure Stack-előfizetés eléréséhez közvetlen hozzáférésre van szükség a Storage Explorer Azure Stack vagy VPN-kapcsolathoz. A VPN-kapcsolat Azure Stackhez való beállításáról a [Csatlakozás az Azure Stackhez VPN segítségével](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) témakörben talál további információt.

> [!Note]  
> Ha a ASDK VPN-kapcsolaton keresztül csatlakozik a ASDK, ne használja a VPN telepítési folyamata során létrehozott főtanúsítványt (CA. cer).  Ez egy DER kódolású tanúsítvány, és nem teszi lehetővé Storage Explorer számára a Azure Stack-előfizetések lekérését. A következő lépésekkel exportálhatja a 64-es kódolású tanúsítványt, hogy az a Storage Explorer használatával legyen használatban.

A leválasztott és a ASDK integrált rendszerek esetében a javaslat egy belső vállalati hitelesítésszolgáltató használata a főtanúsítvány alapszintű 64 formátumban való exportálásához, majd a Azure Storage Explorerba történő importálásához.  

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Az Azure Stack-tanúsítvány exportálása és importálása

Exportálja és importálja Azure Stack tanúsítványt a leválasztott integrált rendszerekhez és a ASDK. A csatlakoztatott integrált rendszerek esetében a tanúsítvány nyilvánosan aláírva, és ez a lépés nem szükséges.

1. Nyissa meg `mmc.exe` értéket egy Azure Stack gazdagépen vagy egy olyan helyi gépen, amely VPN-kapcsolattal rendelkezik a Azure Stackhoz. 

2. A **fájl**menüben kattintson a **beépülő modul hozzáadása/eltávolítása**elemre. Válassza ki a **tanúsítványok** lehetőséget az elérhető beépülő modulokban. 

3. Válassza a **számítógépfiók**lehetőséget, majd kattintson a **Tovább gombra**. Válassza a **helyi számítógép**lehetőséget, majd kattintson a **Befejezés gombra**.

4.  A **konzol Root\Certificated (helyi számítógép) \Trusted legfelső szintű tanúsítási \ tanúsítványok részhez** keresse meg a **AzureStackSelfSignedRootCert**.

    ![Az Azure Stack főtanúsítványának betöltése az mmc.exe segítségével](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Kattintson a jobb gombbal a tanúsítványra, válassza a **minden feladat** > **Exportálás**lehetőséget, majd kövesse az utasításokat a tanúsítvány exportálásához **Base-64 kódolású X. 509 (. CER)** .

    Az exportált tanúsítványt a következő lépésben fogja használni.

6. Storage Explorer elindítása. Ha megjelenik a **Kapcsolódás az Azure Storage-hoz** párbeszédpanel, szakítsa meg a következőt:.

7. A **Szerkesztés** menüben válassza az **SSL-tanúsítványok**lehetőséget, majd válassza a **tanúsítványok importálása**lehetőséget. A fájlválasztó párbeszédpanellel keresse meg és nyissa meg az előző lépésben exportált tanúsítványt.

    A tanúsítvány importálása után a rendszer felszólítja Storage Explorer újraindítására.

    ![A tanúsítvány importálása a Storage Explorerben](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Storage Explorer újraindítása után kattintson a **Szerkesztés** menüre, és ellenőrizze, hogy a **cél Azure stack API** -k van-e kiválasztva. Ha nem, válassza a **cél Azure stack**lehetőséget, majd indítsa újra Storage Explorer a módosítás érvénybe léptetéséhez. Ez a konfiguráció szükséges az Azure Stack környezettel való kompatibilitáshoz.

    ![Győződjön meg arról, hogy be van jelölve a Cél Azure Stack.](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Kapcsolódás Azure Stack-előfizetéshez az Azure AD-vel

A következő lépésekkel csatlakozhat Storage Explorer egy Azure Stack-előfizetéshez, amely egy Azure Active Directory (Azure AD) fiókhoz tartozik.

1. Storage Explorer bal oldali ablaktábláján válassza a **fiókok kezelése**lehetőséget.  
    Megjelenik az összes bejelentkezett Microsoft-előfizetés.

2. A Azure Stack-előfizetéshez való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

    ![Azure Stack-fiók hozzáadása](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. A Kapcsolódás az Azure Storage-hoz párbeszédpanel Azure- **környezet**területén válassza az **Azure**, **Az Azure China 21Vianet**, az **Azure Germany**, az **Azure US government**lehetőséget, vagy **vegyen fel új környezetet**. Ez a használt Azure Stack fióktól függ. Válassza a **Bejelentkezés** lehetőséget a legalább egy aktív Azure stack-előfizetéshez társított Azure stack fiókkal való bejelentkezéshez.

    ![Csatlakozás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Amint sikeresen bejelentkezett egy Azure Stack-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure Stack-előfizetés. Válassza ki azt az Azure Stack-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztásával vagy törlésével kijelölheti az összes felsorolt Azure Stack-előfizetést, vagy törölheti mindegyik jelölését.)

    ![Az Egyéni felhőkörnyezet párbeszédpanel mezőinek kitöltése után válassza ki az Azure Stack-előfizetéseket](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A tárfiókok listája, benne az Azure Stack-előfizetéssel rendelkező fiókokkal](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Kapcsolódás AD FS-fiókkal rendelkező Azure Stack-előfizetéshez

> [!Note]  
> Az Azure összevont szolgáltatás (AD FS) bejelentkezési felülete támogatja a Storage Explorer 1.2.0 vagy újabb verziókat Azure Stack 1804-as vagy újabb frissítéssel.
A következő lépésekkel csatlakozhat a Storage Explorer egy AD FS-fiókhoz tartozó Azure Stack-előfizetéshez.

1. Válassza a **fiókok kezelése**lehetőséget. Az Intéző felsorolja azokat a Microsoft-előfizetéseket, amelyekre bejelentkezett.
2. Válassza a **fiók hozzáadása** lehetőséget a Azure stack-előfizetéshez való kapcsolódáshoz.

    ![Fiók hozzáadása – Storage Explorer](media/azure-stack-storage-connect-se/add-an-account.png)

3. Kattintson a **Tovább** gombra. A Kapcsolódás az Azure Storage-hoz párbeszédpanel Azure- **környezet**területén válassza az **Egyéni környezet használata**lehetőséget, majd kattintson a **tovább**gombra.

    ![Kapcsolódás az Azure Storage-hoz](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Adja meg Azure Stack egyéni környezet szükséges adatait. 

    | Mező | Megjegyzések |
    | ---   | ---   |
    | Környezet neve | A mezőt a felhasználó testreszabhatja. |
    | Azure Resource Manager végpont | A Azure Stack Development Kit Azure Resource Manager erőforrás-végpontjának mintái.<br>Operátorok esetén: https://adminmanagement.local.azurestack.external <br> Felhasználók számára: https://management.local.azurestack.external |

    Ha Azure Stack integrált rendszeren dolgozik, és nem ismeri a felügyeleti végpontját, forduljon a kezelőhöz.

    ![Fiók hozzáadása – egyéni környezetek](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Válassza a **Bejelentkezés** lehetőséget a legalább egy aktív Azure stack-előfizetéshez társított Azure stack fiókhoz való kapcsolódáshoz.



6. Válassza ki a használni kívánt Azure Stack-előfizetéseket, majd kattintson az **alkalmaz**gombra.

    ![Fiókkezelés](./media/azure-stack-storage-connect-se/account-management.png)

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A társított előfizetések listája](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Kapcsolódás Azure Stack Storage-fiókhoz

A Storage-fiók neve és a kulcspár használatával is csatlakozhat egy Azure Stack Storage-fiókhoz.

1. Storage Explorer bal oldali ablaktábláján válassza a fiókok kezelése lehetőséget. Megjelenik az összes bejelentkezett Microsoft-fiók.

    ![Fiók hozzáadása – Storage Explorer](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. A Azure Stack-előfizetéshez való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

    ![Fiók hozzáadása – kapcsolódás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. A Kapcsolódás az Azure Storage-hoz párbeszédpanelen jelölje be **a Storage-fiók nevének és kulcsának használata**jelölőnégyzetet.

4. Adja meg a fiók nevét a **fióknév mezőben, és illessze** be a fiók kulcsát a **fiók kulcsa** szövegmezőbe. Ezután válassza a **továbbiak lehetőséget (adja meg alább)** a **tárolási végpontok tartományban** , és adja meg a Azure stack végpontot.

    Egy Azure Stack végpont két részből áll: a régió és a Azure Stack tartomány neve. A Azure Stack Development Kit az alapértelmezett végpont **helyi. azurestack. external**. Ha nem biztos benne, hogy a végpontról van szó, forduljon a felhő rendszergazdájához.

    ![Név és kulcs csatolása](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Kattintson a **Csatlakozás** gombra.
6. A Storage-fiók sikeres csatolása után a Storage-fiók a nevéhez (**külső, egyéb**) hozzáfűzve jelenik meg.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Storage Explorerrel](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack Storage: különbségek és szempontok](azure-stack-acs-differences.md)
* Az Azure Storage szolgáltatással kapcsolatos további információkért lásd: [a Microsoft Azure Storage bemutatása](/azure/storage/common/storage-introduction)
