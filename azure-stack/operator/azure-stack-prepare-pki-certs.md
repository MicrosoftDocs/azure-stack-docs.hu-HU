---
title: Azure Stack hub PKI-tanúsítványok előkészítése üzembe helyezéshez vagy elforgatáshoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan készítheti elő a PKI-tanúsítványokat Azure Stack hub integrált rendszerek üzembe helyezéséhez vagy a titkok megforgatásához egy meglévő Azure Stack hub-környezetben.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/16/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: e7b346f8c87e49980a57c39d0bebc6a88d21415f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698215"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Azure Stack hub PKI-tanúsítványok előkészítése üzembe helyezéshez vagy elforgatáshoz

Az [Ön által választott hitelesítésszolgáltatótól (CA) kapott](azure-stack-get-pki-certs.md) tanúsítványfájl-fájlokat importálni és exportálni kell a Azure stack hub tanúsítványára vonatkozó követelményeknek megfelelő tulajdonságokkal.

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok előkészítése központi telepítéshez

A következő lépések végrehajtásával előkészítheti és érvényesítheti az Azure Stack hub PKI-tanúsítványait, amelyeket egy új Azure Stack hub-környezet telepítéséhez vagy a titkok egy meglévő Azure Stack hub-környezetben való elforgatásához fog használni.

### <a name="import-the-certificate"></a>A tanúsítvány importálása

1. Másolja a [választott hitelesítésszolgáltatótól beszerzett](azure-stack-get-pki-certs.md) eredeti tanúsítványokat a telepítési gazdagép egyik könyvtárába. 
   > [!WARNING]
   > Ne másolja a közvetlenül a HITELESÍTÉSSZOLGÁLTATÓ által biztosított fájlokból a már importált, exportált vagy módosított fájlokat.

1. Kattintson a jobb gombbal a tanúsítványra, és válassza a **tanúsítvány telepítése** vagy a **pfx telepítése**lehetőséget attól függően, hogy a tanúsítványt hogyan szállították le a hitelesítésszolgáltatótól.

1. A **tanúsítvány importálása varázslóban**válassza a **helyi gép** importálási helyként lehetőséget. Kattintson a **Tovább** gombra. A következő képernyőn kattintson ismét a Tovább gombra.

    ![Helyi számítógép importálási helye a tanúsítványhoz](./media/prepare-pki-certs/1.png)

1. Válassza a **minden tanúsítvány elhelyezése a következő tárban** lehetőséget, majd válassza a **vállalati megbízhatóság** lehetőséget a helyként. A tanúsítványtároló kiválasztása párbeszédpanel bezárásához kattintson **az OK gombra** , majd válassza a **tovább**lehetőséget.

   ![A tanúsítványtároló konfigurálása a tanúsítványok importálásához](./media/prepare-pki-certs/3.png)

   a. Ha PFX-t importál, egy további párbeszédablak jelenik meg. A **titkos kulcs védelme** lapon adja meg a tanúsítványfájl jelszavát, majd engedélyezze a **kulcs megjelölését exportálhatóként. Ez lehetővé teszi, hogy a kulcsok egy későbbi időpontban történő biztonsági mentésére vagy továbbítására legyen** lehetőség. Kattintson a **Tovább** gombra.

   ![Kulcs megjelölése exportálhatóként](./media/prepare-pki-certs/2.png)

1. Az importálás befejezéséhez kattintson a **Befejezés** gombra.

> [!NOTE]
> Azure Stack hub tanúsítványának importálása után a rendszer a tanúsítvány titkos kulcsát PKCS 12 fájlként (PFX) tárolja a fürtözött tárolón.

### <a name="export-the-certificate"></a>A tanúsítvány exportálása

Nyissa meg a Tanúsítványkezelő MMC-konzolt, és kapcsolódjon a helyi számítógép tanúsítványtárolóhoz.

1. Nyissa meg a Microsoft Management Console-t. A Windows 10-es konzol megnyitásához kattintson a jobb gombbal a **Start menü** **Futtatás**parancsára, majd írja be az **MMC** parancsot, majd nyomja le az ENTER billentyűt.

2. Válassza a **fájl** > **beépülő modul hozzáadása/eltávolítása**elemet, majd kattintson a **tanúsítványok** elemre, és válassza a **Hozzáadás**lehetőséget.

    ![Tanúsítványkezelő beépülő modul hozzáadása a Microsoft Management Console-ban](./media/prepare-pki-certs/mmc-2.png)

3. Válassza a **számítógépfiók**lehetőséget, majd kattintson a **Tovább gombra**. Válassza a **helyi számítógép** lehetőséget, majd fejezze be a **befejezést**. A beépülő modul hozzáadása/eltávolítása lap bezárásához kattintson **az OK gombra** .

    ![Fiók kiválasztása a tanúsítványok hozzáadása beépülő modulhoz a Microsoft Management Console-ban](./media/prepare-pki-certs/mmc-3.png)

4. Keresse meg a **tanúsítványok** > a **vállalati megbízhatóság** > a **tanúsítvány helyét**. Győződjön meg arról, hogy a jobb oldalon megjelenik a tanúsítvány.

5. A Tanúsítványkezelő konzol tálcán válassza a **műveletek** > az **összes feladat** > **Exportálás**lehetőséget. Kattintson a **Tovább** gombra.

   > [!NOTE]
   > Attól függően, hogy hány Azure Stack hub-tanúsítvány van, előfordulhat, hogy a folyamatot többször kell végrehajtania.

6. Válassza **az igen, a titkos kulcs exportálása**lehetőséget, majd kattintson a **tovább**gombra.

7. A fájlformátum exportálása szakaszban:
    
   - **Ha lehetséges, jelölje be a tanúsítványban szereplő összes tanúsítvány belefoglalása**jelölőnégyzetet.  
   - Válassza **az összes kibővített tulajdonság exportálása**lehetőséget.  
   - Válassza a **tanúsítvány adatvédelem engedélyezése**lehetőséget.  
   - Kattintson a **Tovább** gombra.  
    
     ![Tanúsítvány exportálása varázsló kiválasztott beállításokkal](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Válassza a **jelszó** lehetőséget, és adja meg a tanúsítványok jelszavát. Hozzon létre egy jelszót, amely megfelel a következő jelszó-összetettségi követelményeknek:

    * Legalább nyolc karakter hosszúnak kell lennie.
    * Legalább hármat a következők közül: nagybetűs, kisbetűk, 0-9, speciális karakterek, alfabetikus karakter, amely nem nagybetűs vagy kisbetűs.

    Jegyezze fel ezt a jelszót. Ezt fogja használni központi telepítési paraméterként.

9. Kattintson a **Tovább** gombra.

10. Válassza ki az exportálandó PFX-fájl nevét és helyét. Kattintson a **Tovább** gombra.

11. Válassza a **Finish** (Befejezés) elemet.

## <a name="next-steps"></a>Következő lépések

[PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)