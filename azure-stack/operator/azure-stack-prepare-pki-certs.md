---
title: Azure Stack nyilvános kulcsokra épülő infrastruktúra-tanúsítványok előkészítése Azure Stack integrált rendszerek üzembe helyezéséhez vagy a titkos kód elforgatásához | Microsoft Docs
description: Ismerteti, hogyan lehet előkészíteni a Azure Stack PKI-tanúsítványokat Azure Stack integrált rendszerekhez.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: 38175eee8be9b8f678405e0ad1ec6f01bdba8b77
ms.sourcegitcommit: ca5025fb04250271fe0b2b2df8ad0b3b9ed3e604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020844"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Azure Stack PKI-tanúsítványok előkészítése üzembe helyezéshez vagy elforgatáshoz való használatra

Az [Ön által választott hitelesítésszolgáltatótól beszerzett](azure-stack-get-pki-certs.md) tanúsítványfájl-fájlokat importálni és exportálni kell a Azure stack tanúsítványára vonatkozó követelményeknek megfelelő tulajdonságokkal.

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok előkészítése központi telepítéshez

A következő lépésekkel előkészítheti és érvényesítheti a Azure Stack PKI-tanúsítványokat, amelyeket egy új Azure Stack-környezet telepítéséhez vagy a titkok egy meglévő Azure Stack környezetben való elforgatásához fog használni. 


### <a name="import-the-certificate"></a>A tanúsítvány importálása

1. Másolja a [választott hitelesítésszolgáltatótól beszerzett](azure-stack-get-pki-certs.md) eredeti tanúsítványokat a telepítési gazdagép egyik könyvtárába. 
   > [!WARNING]
   > Ne másolja a közvetlenül a HITELESÍTÉSSZOLGÁLTATÓ által biztosított fájlokból a már importált, exportált vagy módosított fájlokat.

1. Kattintson a jobb gombbal a tanúsítványra, és válassza a **tanúsítvány telepítése** vagy a **pfx telepítése** lehetőséget attól függően, hogy a tanúsítványt hogyan adták ki a hitelesítésszolgáltatótól.

1. A **tanúsítvány importálása varázslóban**válassza a **helyi gép** importálási helyként lehetőséget. Kattintson a **Tovább** gombra. A következő képernyőn kattintson ismét a Tovább gombra.

    ![Helyi számítógép importálási helye](./media/prepare-pki-certs/1.png)

1. Válassza a **minden tanúsítvány elhelyezése a következő tárban** lehetőséget, majd válassza a **vállalati megbízhatóság** lehetőséget a helyként. Kattintson az **OK** gombra a tanúsítványtároló kiválasztása párbeszédpanel bezárásához, majd a **tovább**gombra.

   ![A tanúsítványtároló konfigurálása](./media/prepare-pki-certs/3.png)

   a. Ha PFX-t importál, egy további párbeszédpanel jelenik meg. A **titkos kulcs védelme** lapon adja meg a tanúsítványfájl jelszavát, majd engedélyezze a **kulcs megjelölését exportálhatóként. Ez lehetővé teszi, hogy a kulcsok egy későbbi időpontban** történő biztonsági mentésére vagy továbbítására legyen lehetőség. Kattintson a **Tovább** gombra.

   ![Kulcs megjelölése exportálhatóként](./media/prepare-pki-certs/2.png)

1. Az importálás befejezéséhez kattintson a Befejezés gombra.

> [!NOTE]
> Miután importált egy tanúsítványt a Azure Stackhoz, a rendszer a tanúsítvány titkos kulcsát PKCS 12 fájlként (PFX) tárolja a fürtözött tárolón.

### <a name="export-the-certificate"></a>A tanúsítvány exportálása

Nyissa meg a Tanúsítványkezelő MMC-konzolt, és kapcsolódjon a helyi számítógép tanúsítványtárolóhoz.

1. Nyissa meg a Microsoft Management Console-t a Windows 10 jobb gombbal a Start menüben, majd kattintson a Futtatás parancsra. Írja be az **MMC** kifejezést, majd kattintson az OK gombra.

1. Kattintson a fájl, beépülő modul hozzáadása/eltávolítása elemre, majd válassza a tanúsítványok lehetőséget, majd kattintson a Hozzáadás gombra.

    ![Tanúsítványok beépülő modul hozzáadása](./media/prepare-pki-certs/mmc-2.png)
 
1. Válassza a számítógépfiók lehetőséget, kattintson a Tovább gombra, majd válassza a helyi számítógép, majd a Befejezés lehetőséget. A beépülő modul hozzáadása/eltávolítása lap bezárásához kattintson az OK gombra.

    ![Tanúsítványok beépülő modul hozzáadása](./media/prepare-pki-certs/mmc-3.png)

1. Keresse meg a tanúsítványok > a vállalati megbízhatóság > a tanúsítvány helyét. Győződjön meg arról, hogy a jobb oldalon megjelenik a tanúsítvány.

1. A Tanúsítványkezelő konzol tálcán válassza a **műveletek** > **minden feladat** > **Exportálás**lehetőséget. Kattintson a **Tovább** gombra.

   > [!NOTE]
   > Attól függően, hogy hány Azure Stack tanúsítványra van szüksége, többször is végre kell hajtania ezt a folyamatot.

1. Válassza **az igen, a titkos kulcs exportálása**lehetőséget, majd kattintson a **tovább**gombra.

1. A fájlformátum exportálása szakaszban:
    
   - **Ha lehetséges, jelölje be a tanúsítványban szereplő összes tanúsítvány belefoglalása**jelölőnégyzetet.  
   - Válassza **az összes kibővített tulajdonság exportálása**lehetőséget.  
   - Válassza a **tanúsítvány adatvédelem engedélyezése**lehetőséget.  
   - Kattintson a **Tovább** gombra.  
    
     ![Tanúsítvány exportálása varázsló kiválasztott beállításokkal](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Válassza a **jelszó** lehetőséget, és adja meg a tanúsítványok jelszavát. Hozzon létre egy jelszót, amely megfelel az alábbi jelszó-összetettségi követelményeknek. Legalább nyolc karakter hosszúnak kell lennie. A jelszó legalább hármat tartalmaz a következők közül: nagybetűk, kisbetűk, 0-9, speciális karakterek, alfabetikus karakter, amely nem nagybetűs és nem kisbetűs. Jegyezze fel ezt a jelszót. Ezt fogja használni központi telepítési paraméterként.

1. Kattintson a **Tovább** gombra.

1. Válassza ki az exportálandó PFX-fájl nevét és helyét. Kattintson a **Tovább** gombra.

1. Válassza a **Finish** (Befejezés) elemet.

## <a name="next-steps"></a>További lépések

[PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)