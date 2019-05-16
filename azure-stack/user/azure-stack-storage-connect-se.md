---
title: Storage Explorer csatlakoztatása Azure Stack-előfizetéshez vagy a tárfiók |} A Microsoft Docs
description: Ismerje meg a storage explorer csatlakoztatása Azure Stack-előfizetéshez
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: f7631b049197c0721ffa801c225e1ecdf853524f
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712478"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Storage explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ebből a cikkből megtudhatja, hogyan szeretne csatlakozni az Azure Stack-előfizetést, és a storage-fiókok használatával [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer). Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy egyszerűen dolgozhat Azure Stack storage-adatokkal Windows, macOS és Linux rendszereken.

> [!NOTE]  
> Nincsenek elérhető adatok áthelyezése az Azure Stack storage szolgáltatásba vagy onnan számos eszközt. További információkért lásd: [adatátviteli eszközök az Azure Stack storage](azure-stack-storage-transfer.md).

Ha még nincs telepítve, [a Storage Explorer letöltése](https://www.storageexplorer.com/) , és telepítse.

Miután csatlakozott az Azure Stack-előfizetés vagy tárfiók, használhatja a [Azure Storage Explorer cikkek](/azure/vs-azure-tools-storage-manage-with-storage-explorer) az Azure Stack-adatokkal szeretne dolgozni. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Kapcsolódás az Azure Stack előkészítése

Azure Stack vagy a Storage Explorer egy VPN-kapcsolat az Azure Stack-előfizetés eléréséhez közvetlen hozzáférésre van szüksége. A VPN-kapcsolat Azure Stackhez való beállításáról a [Csatlakozás az Azure Stackhez VPN segítségével](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) témakörben talál további információt.

Az az Azure Stack Development Kit (ASDK), kell exportálni az Azure Stack szolgáltató főtanúsítványát.

> [!Note]  
> Ha a VPN-en keresztül ASDK, amelyhez csatlakozik, a ASDK a VPN-telepítési folyamat során létrehozott főtanúsítványt (CA.cer) nem használja.  Ez az egy DER kódolású tanúsítványt, és azt nem engedélyezik a Storage Explorer beolvasni az Azure Stack-előfizetést. A Storage Explorer használata Base-64 kódolású tanúsítvány exportálása az alábbi lépésekkel.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportálja és importálja az Azure Stack-tanúsítvány

Exportálja és importálja az Azure Stack-tanúsítvány esetében a ASDK. Integrált rendszerek esetén a tanúsítvány nyilvánosan alá van írva, és ez a lépés nem szükséges.

1. Nyissa meg `mmc.exe` az Azure Stack gazdagépen vagy az Azure Stackhez VPN-kapcsolattal egy helyi számítógépre. 

2. A **fájl**válassza **beépülő modul hozzáadása/eltávolítása**. Válassza ki **tanúsítványok** az elérhető beépülő modulok. 

3. Válassza ki **számítógépfiók**, majd válassza ki **tovább**. Válassza ki **helyi számítógép**, majd válassza ki **Befejezés**.

4.  A **Console Root\Certificated (helyi számítógép) \Trusted Root Certification Authorities\Certificates** található **AzureStackSelfSignedRootCert**.

    ![Az Azure Stack főtanúsítványának betöltése az mmc.exe segítségével](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Válassza ki a tanúsítványra jobb kattintás **feladatok** > **exportálása**, majd kövesse az utasításokat a tanúsítvány exportálása a **Base-64 kódolású X.509 (. CER)**.

    Az exportált tanúsítványt a következő lépésben fogja használni.

6. Start Storage Explorer. Ha megjelenik a **csatlakozás az Azure Storage** párbeszédpanel mezőben, törölje azt.

7. Az a **szerkesztése** menüjében válassza **SSL-tanúsítványok**, majd válassza ki **tanúsítványok importálása**. A fájlválasztó párbeszédpanellel keresse meg és nyissa meg az előző lépésben exportált tanúsítványt.

    A tanúsítvány az importálás után kéri, hogy a Tártallózó újraindítására.

    ![A tanúsítvány importálása a tártallózóba](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Storage Explorer újraindítása után válassza ki a **szerkesztése** menüt, és ellenőrzi, hogy **cél Azure Stack API-k** van kiválasztva. Ha nem, válassza ki a **cél Azure Stack**, majd indítsa újra a Tártallózót a módosítás érvénybe léptetéséhez. Ez a konfiguráció szükséges az Azure Stack környezettel való kompatibilitáshoz.

    ![Győződjön meg arról, hogy be van jelölve a Cél Azure Stack.](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Az Azure Stack-előfizetéshez az Azure AD Connect

A következő lépések segítségével a Storage Explorer csatlakoztatása Azure Stack-előfizetéshez, amely egy Azure Active Directory (Azure AD-) fiók tartozik.

1. A Storage Explorer, a bal oldali panelen válassza ki a **fiókok kezelése**.  
    A Microsoft az összes olyan előfizetést, a regisztrált jelennek meg.

2. Szeretne csatlakozni az Azure Stack-előfizetéshez, válasszon **vegyen fel egy fiókot**.

    ![Azure Stack-fiók hozzáadása](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. A csatlakozás Azure Storage párbeszédpanelen alatt **Azure-környezet**válassza **Azure**, **Azure China 21Vianet**, **Azure Germany**, **Azure US Government**, vagy **adja hozzá az új környezet**. Ez az Azure Stack-fiókhoz használt függ. Válassza ki **jelentkezzen be a** legalább egy aktív Azure Stack-előfizetéssel társított az Azure Stack-fiókkal.

    ![Csatlakozás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Amint sikeresen bejelentkezett egy Azure Stack-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure Stack-előfizetés. Válassza ki azt az Azure Stack-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztásával vagy törlésével kijelölheti az összes felsorolt Azure Stack-előfizetést, vagy törölheti mindegyik jelölését.)

    ![Az Egyéni felhőkörnyezet párbeszédpanel mezőinek kitöltése után válassza ki az Azure Stack-előfizetéseket](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A tárfiókok listája, benne az Azure Stack-előfizetéssel rendelkező fiókokkal](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Csatlakozás az AD FS-fiókot az Azure Stack-előfizetéshez

> [!Note]  
> Bejelentkezési felület Azure összevonási szolgáltatással (AD FS) támogatja a Storage Explorer 1.2.0-s vagy annál újabb és újabb verzióin, az Azure Stack 1804 vagy újabb frissítés.
A következő lépések segítségével a Storage Explorer csatlakoztatása Azure Stack-előfizetéssel, amely egy AD FS-fiókhoz tartozik.

1. Válassza ki **fiókok kezelése**. Az explorer a jelentkezett be Microsoft-előfizetések sorolja fel.
2. Válassza ki **vegyen fel egy fiókot** csatlakozni az Azure Stack-előfizetéssel.

    ![Vegyen fel egy fiókot – Storage Explorer](media/azure-stack-storage-connect-se/add-an-account.png)

3. Kattintson a **Tovább** gombra. A csatlakozás Azure Storage párbeszédpanelen alatt **Azure-környezet**, jelölje be **használata egyéni környezet**, majd kattintson a **tovább**.

    ![Csatlakozás az Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Adja meg az Azure Stack egyéni környezet a szükséges adatokat. 

    | Mező | Megjegyzések |
    | ---   | ---   |
    | Környezet neve | A mező a felhasználó testreszabhatja. |
    | Az Azure Resource Manager-végpont | A minták Azure Resource Manager-erőforrás végpontok az Azure Stack Development Kit.<br>A kezelők: https://adminmanagement.local.azurestack.external <br> A felhasználók számára: https://management.local.azurestack.external |

    Ha dolgozik, és ismeri a felügyeleti végpontra, forduljon az operátor az Azure Stack integrált rendszerek nem.

    ![Vegyen fel egy fiókot – egyéni környezetekben](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Válassza ki **jelentkezzen be a** csatlakozni az Azure Stack-fiókkal, amely legalább egy aktív Azure Stack-előfizetéssel társított.



6. Válassza ki az Azure Stack-előfizetést, amelyet szeretne dolgozni, majd válassza a **alkalmaz**.

    ![Fiókkezelés](./media/azure-stack-storage-connect-se/account-management.png)

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A társított előfizetésekkel listája](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Csatlakozás az Azure Stack tárfiókok

Egy Azure Stack tárfiókok a tárfiók nevét és a kulcspár használatával is csatlakozhat.

1. A Storage Explorer, a bal oldali panelen válassza ki a fiókok kezelése. Korábban bejelentkezett az összes Microsoft-fiókok jelennek meg.

    ![Vegyen fel egy fiókot – Storage Explorer](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Szeretne csatlakozni az Azure Stack-előfizetéshez, válasszon **vegyen fel egy fiókot**.

    ![-Fiók hozzáadása az Azure Storage csatlakoztatása](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. A csatlakozás Azure Storage-párbeszédpanel, válassza ki a **tárfiók nevének és kulcsának**.

4. Adjon meg a fiók neve az a **fióknév** , és illessze be a fiókkulcsot a **fiókkulcs** szövegmezőben. Ezután válassza ki **egyéb (adja meg alább)** a **tárolási végpontok tartománya** , és adjon meg az Azure Stack-végpont.

    Egy Azure Stack-végpont tartalmaz két részből áll: nevét és a egy régióban az Azure Stack tartományhoz. Az Azure Stack fejlesztői készletének az alapértelmezett végpont az **local.azurestack.external**. Lépjen kapcsolatba a felhő rendszergazdájával, ha nem biztos a végpontra vonatkozó.

    ![Név és kulcs csatolása](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Kattintson a **Csatlakozás** gombra.
6. A tárfiók sikeresen csatolta, miután a tárfiók megjelenik, (**külső, más**) a névhez.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>További lépések

* [Ismerkedés a storage Explorerben](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Az Azure Stack storage: különbségek és szempontok](azure-stack-acs-differences.md)
* Az Azure storage szolgáltatással kapcsolatos további tudnivalókért lásd: [a Microsoft Azure storage bemutatása](/azure/storage/common/storage-introduction)
