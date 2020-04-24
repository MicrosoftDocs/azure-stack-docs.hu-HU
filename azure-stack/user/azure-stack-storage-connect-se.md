---
title: Storage Explorer összekötése egy Azure Stack hub-előfizetéssel vagy egy Storage-fiókkal
description: Ismerje meg, hogyan csatlakozhat Storage Explorer egy Azure Stack hub-előfizetéshez
author: mattbriggs
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 701b2a0c6bae3fa4f246877f49f73aca8482b0de
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81660378"
---
# <a name="connect-storage-explorer-to-an-azure-stack-hub-subscription-or-a-storage-account"></a>Storage Explorer összekötése egy Azure Stack hub-előfizetéssel vagy egy Storage-fiókkal

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Azure Stack hub-előfizetésekhez és a Storage-fiókokhoz a [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)használatával. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy könnyedén működjön együtt a Azure Stack hub Storage-szolgáltatással a Windows, macOS és Linux rendszereken.

> [!NOTE]  
> Több eszköz is rendelkezésre áll az adatok áthelyezéséhez Azure Stack hub-tárolóba. További információ: [adatátviteli eszközök az Azure stack hub Storage](azure-stack-storage-transfer.md)szolgáltatáshoz.

Ha még nincs telepítve, [töltse le Storage Explorer](https://www.storageexplorer.com/) és telepítse.

Miután kapcsolódott egy Azure Stack hub-előfizetéshez vagy egy Storage-fiókhoz, a [Azure Storage Explorer cikkek](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával dolgozhat az Azure stack hub-adataival. 

## <a name="prepare-for-connecting-to-azure-stack-hub"></a>Felkészülés az Azure Stack hubhoz való csatlakozásra

A Azure Stack hub-előfizetés eléréséhez közvetlen hozzáférést kell biztosítania Azure Stack hubhoz vagy egy VPN-kapcsolathoz Storage Explorerhoz. Ha meg szeretné tudni, hogyan állíthat be VPN-kapcsolatot Azure Stack hubhoz, tekintse meg a [csatlakozás Azure stack HUBHOZ VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)használatával című témakört.

> [!Note]  
> Ha a ASDK VPN-kapcsolaton keresztül csatlakozik a ASDK, ne használja a VPN telepítési folyamata során létrehozott főtanúsítványt (CA. cer).  Ez egy DER kódolású tanúsítvány, és nem teszi lehetővé Storage Explorer számára a Azure Stack hub-előfizetések lekérését. A következő lépésekkel exportálhatja a 64-es kódolású tanúsítványt, hogy az a Storage Explorer használatával legyen használatban.

A leválasztott és a ASDK integrált rendszerek esetében a javaslat egy belső vállalati hitelesítésszolgáltató használata a főtanúsítvány alapszintű 64 formátumban való exportálásához, majd a Azure Storage Explorerba történő importálásához.  

### <a name="export-and-then-import-the-azure-stack-hub-certificate"></a>Az Azure Stack hub-tanúsítvány exportálása és importálása

Exportálja és importálja Azure Stack hub-tanúsítványt a leválasztott integrált rendszerekhez és a ASDK. A csatlakoztatott integrált rendszerek esetében a tanúsítvány nyilvánosan aláírva, és ez a lépés nem szükséges.

1. Nyisson `mmc.exe` meg egy Azure stack hub-gazdagépet vagy egy helyi gépet, amely VPN-kapcsolattal rendelkezik Azure stack hubhoz. 

2. A **fájl**menüben kattintson a **beépülő modul hozzáadása/eltávolítása**elemre. Válassza ki a **tanúsítványok** lehetőséget az elérhető beépülő modulokban. 

3. Válassza a **számítógépfiók**lehetőséget, majd kattintson a **Tovább gombra**. Válassza a **helyi számítógép**lehetőséget, majd kattintson a **Befejezés gombra**.

4.  A **konzol Root\Certificated (helyi számítógép) \Trusted legfelső szintű tanúsítási \ tanúsítványok részhez** keresse meg a **AzureStackSelfSignedRootCert**.

    ![Az Azure Stack hub főtanúsítványának betöltése az MMC. exe használatával](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Kattintson a jobb gombbal a tanúsítványra, válassza a **minden feladat** > **Exportálás**lehetőséget, majd kövesse az utasításokat a tanúsítvány exportálásához **Base-64 kódolású X. 509 (. CER)**.

    Az exportált tanúsítványt a következő lépésben fogja használni.

6. Storage Explorer elindítása. Ha megjelenik a **Kapcsolódás az Azure Storage-hoz** párbeszédpanel, szakítsa meg a következőt:.

7. A **Szerkesztés** menüben válassza az **SSL-tanúsítványok**lehetőséget, majd válassza a **tanúsítványok importálása**lehetőséget. A fájlválasztó párbeszédpanellel keresse meg és nyissa meg az előző lépésben exportált tanúsítványt.

    A tanúsítvány importálása után a rendszer felszólítja Storage Explorer újraindítására.

    ![A tanúsítvány importálása a Storage Explorerba](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Storage Explorer újraindítása után kattintson a **Szerkesztés** menüre, és ellenőrizze, hogy a **cél Azure stack hub API** -k van-e kiválasztva. Ha nem, válassza a **cél Azure stack hub**lehetőséget, majd indítsa újra Storage Explorer a módosítás érvénybe léptetéséhez. Ez a konfiguráció az Azure Stack hub-környezettel való kompatibilitáshoz szükséges.

    ![A cél Azure Stack hub kiválasztásának ellenőrzése](./media/azure-stack-storage-connect-se/target-azure-stack-new.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-azure-ad"></a>Kapcsolódás Azure Stack hub-előfizetéshez az Azure AD-vel

A következő lépésekkel csatlakoztathatja a Storage Explorert egy Azure Stack hub-előfizetéshez, amely egy Azure Active Directory (Azure AD) fiókhoz tartozik.

1. Storage Explorer bal oldali ablaktábláján válassza a **fiókok kezelése**lehetőséget.  
    Megjelenik az összes bejelentkezett Microsoft-előfizetés.

2. Az Azure Stack hub-előfizetéshez való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

    ![Azure Stack hub-fiók hozzáadása](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. A Kapcsolódás az Azure Storage-hoz párbeszédpanel Azure- **környezet**területén válassza az **Azure**, **Az Azure China 21Vianet**, az **Azure Germany**, az **Azure US government**lehetőséget, vagy **vegyen fel új környezetet**. Ez a használt Azure Stack hub-fióktól függ. A **Bejelentkezés** lehetőség kiválasztásával jelentkezzen be az Azure stack hub-fiókkal, amely legalább egy aktív Azure stack hub-előfizetéshez van társítva.

    ![Csatlakozás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Miután sikeresen bejelentkezett egy Azure Stack hub-fiókkal, a bal oldali ablaktábla a fiókhoz társított Azure Stack hub-előfizetésekkel lesz feltöltve. Válassza ki a használni kívánt Azure Stack hub-előfizetéseket, majd kattintson az **alkalmaz**gombra. (Az **összes előfizetés** kijelölése vagy törlése jelölőnégyzet bejelöli az összes felsorolt Azure stack hub-előfizetés kijelölését vagy egyikét sem.)

    ![Válassza ki az Azure Stack hub-előfizetéseket az egyéni felhőalapú környezet párbeszédpanel kitöltése után](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    A bal oldali ablaktábla a kiválasztott Azure Stack hub-előfizetésekhez társított Storage-fiókokat jeleníti meg.

    ![A Azure Stack hub előfizetési fiókjait tartalmazó Storage-fiókok listája](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-ad-fs-account"></a>Kapcsolódás Azure Stack hub-előfizetéshez AD FS-fiókkal

> [!Note]  
> Az Azure összevont szolgáltatás (AD FS) bejelentkezési felülete támogatja a Storage Explorer 1.2.0 vagy újabb verziókat az Azure Stack hub 1804-as vagy újabb frissítésével.
A következő lépésekkel csatlakoztathatja a Storage Explorert egy AD FS-fiókhoz tartozó Azure Stack hub-előfizetéshez.

1. Válassza a **fiókok kezelése**lehetőséget. Az Intéző felsorolja azokat a Microsoft-előfizetéseket, amelyekre bejelentkezett.
2. Válassza a **fiók hozzáadása** lehetőséget az Azure stack hub-előfizetéshez való kapcsolódáshoz.

    ![Fiók hozzáadása – Storage Explorer](media/azure-stack-storage-connect-se/add-an-account.png)

3. Kattintson a **Tovább** gombra. A Kapcsolódás az Azure Storage-hoz párbeszédpanel Azure- **környezet**területén válassza az **Egyéni környezet használata**lehetőséget, majd kattintson a **tovább**gombra.

    ![Csatlakozás az Azure Storage-hoz](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Adja meg Azure Stack hub egyéni környezet szükséges adatait. 

    | Mező | Megjegyzések |
    | ---   | ---   |
    | Környezet neve | A mezőt a felhasználó testreszabhatja. |
    | Azure Resource Manager végpont | A Azure Stack Development Kit Azure Resource Manager erőforrás-végpontjának mintái.<br>Operátorok esetén:https://adminmanagement.local.azurestack.external <br> Felhasználók számára:https://management.local.azurestack.external |

    Ha Azure Stack hub integrált rendszeren dolgozik, és nem ismeri a felügyeleti végpontját, forduljon a kezelőhöz.

    ![Fiók hozzáadása – egyéni környezetek](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Válassza a **Bejelentkezés** lehetőséget a legalább egy aktív Azure stack hub-előfizetéshez társított Azure stack hub-fiókhoz való kapcsolódáshoz.



6. Válassza ki a használni kívánt Azure Stack hub-előfizetéseket, majd kattintson az **alkalmaz**gombra.

    ![Fiókkezelés](./media/azure-stack-storage-connect-se/account-management.png)

    A bal oldali ablaktábla a kiválasztott Azure Stack hub-előfizetésekhez társított Storage-fiókokat jeleníti meg.

    ![A társított előfizetések listája](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-hub-storage-account"></a>Kapcsolódás Azure Stack hub Storage-fiókhoz

A Storage-fiók neve és a kulcspár használatával is csatlakozhat egy Azure Stack hub Storage-fiókhoz.

1. Storage Explorer bal oldali ablaktábláján válassza a fiókok kezelése lehetőséget. Megjelenik az összes bejelentkezett Microsoft-fiók.

    ![Fiók hozzáadása – Storage Explorer](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Az Azure Stack hub-előfizetéshez való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

    ![Fiók hozzáadása – kapcsolódás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. A Kapcsolódás az Azure Storage-hoz párbeszédpanelen jelölje be **a Storage-fiók nevének és kulcsának használata**jelölőnégyzetet.

4. Adja meg a fiók nevét a **fióknév mezőben, és illessze** be a fiók kulcsát a **fiók kulcsa** szövegmezőbe. Ezután válassza a **továbbiak lehetőséget (adja meg alább)** a **tárolási végpontok tartományban** , és adja meg az Azure stack hub-végpontot.

    Azure Stack hub-végpont két részből áll: egy régió és a Azure Stack hub tartományának nevével. A Azure Stack Development Kit az alapértelmezett végpont **helyi. azurestack. external**. Ha nem biztos benne, hogy a végpontról van szó, forduljon a felhő rendszergazdájához.

    ![Név és kulcs csatolása](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Kattintson a **Csatlakozás** gombra.
6. A Storage-fiók sikeres csatolása után a Storage-fiók a nevéhez (**külső, egyéb**) hozzáfűzve jelenik meg.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>További lépések

* [A Storage Explorer használatának első lépései](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack hub Storage: különbségek és megfontolások](azure-stack-acs-differences.md)
* Az Azure Storage szolgáltatással kapcsolatos további információkért lásd: [a Microsoft Azure Storage bemutatása](/azure/storage/common/storage-introduction)
