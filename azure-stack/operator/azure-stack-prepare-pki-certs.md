---
title: Az Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványokat előkészítése az Azure Stackkel integrált rendszerek üzembe helyezési vagy titkos Elforgatás |} A Microsoft Docs
description: Ismerteti, hogyan készíti elő az Azure Stack PKI-tanúsítványok az Azure Stack integrált rendszerek.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 682a5ce693bd9f184fd73a322ab9ed29c2d90fae
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782494"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Az Azure Stack PKI-tanúsítványok előkészítése az üzembe helyezési vagy elforgatás használható

A tanúsítványfájlokat [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) kell exportálható és importálható az Azure Stack szemben támasztott követelményeknek megfelelő tulajdonságokkal.

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok telepítésének előkészítése

Ezeket a lépéseket segítségével készítheti elő, és ellenőrizze az Azure Stack PKI-tanúsítványokat, amely egy új Azure Stack-környezet üzembe helyezéséhez, vagy a titkos kulcsokat egy meglévő Azure Stack-környezet a rotált használható: 

### <a name="import-the-certificate"></a>Importálja a tanúsítványt

1. Másolja az eredeti tanúsítvány verziók [választott a hitelesítésszolgáltatótól kapott](azure-stack-get-pki-certs.md) a központi telepítési gazdagépen egy könyvtárba. 
   > [!WARNING]
   > Ne másolja a fájlokat, amelyek már importálása, exportálása, vagy módosítani a fájlok közvetlenül a CA által nyújtott semmilyen módon.

1. Kattintson a jobb gombbal a tanúsítványt, és válassza **tanúsítvány telepítése** vagy **PFX telepítése** attól függően, hogy a tanúsítványt a hitelesítésszolgáltatótól lett kézbesítve.

1. Az a **Tanúsítványimportáló varázsló**válassza **helyi gépen** importálás helyeként. Kattintson a **Tovább** gombra. A következő képernyőn kattintson a Tovább újra.

    ![Importálási hely helyi számítógépre](./media/prepare-pki-certs/1.png)

1. Válassza a **ebben a tárolóban az összes tanúsítvány helye** majd **vállalati szintű megbízhatóság** helyeként. Kattintson a **OK** a tároló kiválasztása párbeszédpanel bezárásához, majd **tovább**.

   ![A tanúsítványtároló konfigurálása](./media/prepare-pki-certs/3.png)

   a. Ha egy PFX importál, megjelenik egy további párbeszédpanel. Az a **titkos kulcs védelme** lap, adja meg a jelszót a tanúsítványfájlokat, majd engedélyezze a **a kulcs megjelölése exportálhatóként. Ez lehetővé teszi, hogy készítsen biztonsági másolatot, vagy egy későbbi időpontban a kulcsok átviteli** lehetőséget. Kattintson a **Tovább** gombra.

   ![Kulcs megjelölése exportálhatóként](./media/prepare-pki-certs/2.png)

1. Az importálás befejeződik a Befejezés gombra.

### <a name="export-the-certificate"></a>A tanúsítvány exportálása

Nyissa meg a tanúsítványkezelőben MMC-konzolt, és csatlakozzon a helyi számítógép tanúsítványtárolójába.

1. Nyissa meg a Microsoft Management Console, a Windows 10-ben kattintson jobb gombbal a Start menüben, majd kattintson az OK gombra. Típus **mmc** kattintson az OK gombra.

1. Kattintson a fájlra, majd válassza ki a tanúsítványok beépülő modul hozzáadása/eltávolítása kattintson a Hozzáadás gombra.

    ![Tanúsítványkezelő beépülő modul hozzáadása](./media/prepare-pki-certs/mmc-2.png)
 
1. Válassza ki a fiókot, kattintson a Tovább gombra, majd válassza ki a helyi számítógépen, majd fejezze be. Kattintson az ok gombra kattintva zárja be a beépülő modul hozzáadása/eltávolítása lapon.

    ![Tanúsítványkezelő beépülő modul hozzáadása](./media/prepare-pki-certs/mmc-3.png)

1. Keresse meg a tanúsítványok > Vállalati szintű megbízhatóság > tanúsítvány helye. Győződjön meg arról, hogy megjelenik-e a tanúsítvány a jobb oldalon.

1. A feladat sáv, Manager konzolt, válassza ki **műveletek** > **feladatok** > **exportálása**. Kattintson a **Tovább** gombra.

   > [!NOTE]
   > Attól függően, hogy hány Azure Stack tanúsítványok rendelkezik, előfordulhat, hogy egyszer kell elvégezni a folyamat több mint.

1. Válassza ki **Igen, a titkos kulcs exportálását választom**, és kattintson a **tovább**.

1. Az Exportfájlformátum szakaszban:
    
   - Válassza ki **minden tanúsítvány belefoglalása a tanúsítvány**.  
   - Válassza ki **exportálja az összes kiterjesztett tulajdonság**.  
   - Válassza ki **engedélyezése tanúsítvány adatvédelmi**.  
   - Kattintson a **tovább**.  
    
     ![Tanúsítvány exportálása varázslóban a kiválasztott beállítások](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Válassza ki **jelszó** , és adjon meg egy jelszót a tanúsítványokat. Hozzon létre egy jelszót, amely megfelel a következő jelszó összetettségi követelményeknek. A minimális hossza nyolc karakternél. A jelszó tartalmazza a következők közül legalább háromból: nagybetűs levél, kisbetű, számok, a 0-9, különleges karakterek nem nagybetűssé vagy kisbetűssé nagybetű. Jegyezze meg ezt a jelszót. Üzembehelyezési paraméterként alkalmaznak.

1. Kattintson a **Tovább** gombra.

1. Válassza ki a fájl nevét és helyét, a pfx-fájljának exportálása. Kattintson a **Tovább** gombra.

1. Válassza a **Finish** (Befejezés) elemet.

## <a name="next-steps"></a>További lépések

[PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)