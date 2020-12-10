---
title: Biztonsági mentés engedélyezése a Azure Stack a felügyeleti portálról | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a Infrastructure Backup szolgáltatást a felügyeleti portálról, hogy a rendszer meghibásodás esetén is visszaállítsa Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940187"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>A Azure Stack hub biztonsági mentésének konfigurálása a felügyeleti portálról

*A következőkre vonatkozik: moduláris adatközpont, Azure Stack hub robusztus*

A Infrastructure Backup szolgáltatás a felügyeleti portálról konfigurálható úgy, hogy az infrastruktúra biztonsági másolatait külső tárolóhelyre exportálja. Az infrastruktúra biztonsági mentéseit a támogatás a csökkentett teljesítményű szolgáltatások kijavításához használhatja.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

1. Nyissa meg a [Azure stack felügyeleti portált](../../operator/azure-stack-manage-portals.md).

2. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza az **infrastruktúra biztonsági mentése** lehetőséget. Válassza a **konfiguráció** lehetőséget az **infrastruktúra biztonsági mentése** panelen.

3. Adja meg a **biztonsági mentési tár helyének** elérési útját. Használjon egy univerzális elnevezési konvenció (UNC) karakterláncot a különálló eszközön tárolt fájlmegosztás elérési útjához. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét. A szolgáltatáshoz használhat IP-címet. A biztonsági mentési adatmennyiségnek a katasztrófa utáni rendelkezésre állásának biztosításához az eszköznek külön helyen kell lennie.

    > [!NOTE]  
    > Ha a környezet támogatja a névfeloldást a Azure Stack infrastruktúra-hálózatról a vállalati környezetre, az IP helyett teljes tartománynevet (FQDN) is használhat.

4. Írja be a **felhasználónevet** a tartomány és a Felhasználónév használatával, megfelelő hozzáféréssel az olvasási és írási fájlokhoz; például: **Contoso\backupshareuser**.

5. Adja meg a felhasználó **jelszavát** .

6. Írja be ismét a jelszót a **jelszó megerősítéséhez**.

7. Az **órák gyakorisága** határozza meg, hogy milyen gyakran jönnek létre a biztonsági másolatok. Az alapértelmezett érték 12. Az ütemező legalább 4 és legfeljebb 12 értéket támogat.

8. A **megőrzési időtartam napokban** határozza meg, hogy a biztonsági másolatok hány napja maradnak meg a külső helyen. Az alapértelmezett érték 7. A Scheduler legalább 2, de legfeljebb 14 értéket támogat. A megőrzési időtartamnál régebbi biztonsági másolatokat a rendszer automatikusan törli a külső helyről.

   > [!NOTE]
   > Ha a biztonsági mentéseket a megőrzési időtartamnál régebbi verzióra szeretné archiválni, mindenképpen készítsen biztonsági másolatot a fájlokról, mielőtt az ütemező törli a biztonsági mentéseket. Ha csökkenti a biztonsági másolatok megőrzési időtartamát (például 7 nap és 5 nap között), akkor az ütemező törli az új megőrzési időtartamnál régebbi összes biztonsági mentést. Az érték frissítése előtt győződjön meg arról, hogy az OK gombra kattintott a biztonsági másolatok törléséhez.

9. A **titkosítási beállításokban** a nyilvános Tanúsítvány ujjlenyomata az üzembe helyezés során biztosított tanúsítványhoz tartozik. A meglévő tanúsítványt nem kell frissíteni.

10. A biztonsági mentési vezérlő beállításainak mentéséhez kattintson **az OK gombra** .

    ![Azure Stack – a biztonsági mentési vezérlő beállításai](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>Automatikus biztonsági mentések engedélyezése vagy letiltása

A rendszer automatikusan engedélyezi a biztonsági mentéseket az üzembe helyezés során. A tárolási hely konfigurálása után az automatikus biztonsági mentések a gyakoriság beállítása alapján a biztonsági másolatokat egy külső tárolási helyre exportálják. A következő ütemezett biztonsági mentés időpontját az **alapvető** erőforrások panelen tekintheti meg.

![Igény szerinti Azure Stack biztonsági mentés](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

Ha le kell tiltania a jövőbeli ütemezett biztonsági mentéseket, válassza az **automatikus biztonsági mentések letiltása** lehetőséget. Az automatikus biztonsági mentések letiltása megőrzi a biztonsági mentési beállításokat, és megőrzi a biztonsági mentés ütemezését. Ez a művelet közli a Feladatütemezővel, hogy kihagyja a jövőbeli biztonsági mentéseket.

![Azure Stack – ütemezett biztonsági mentések letiltása](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

Győződjön meg arról, hogy a jövőbeli ütemezett biztonsági mentések le vannak tiltva az **essentialsben**:

![Azure Stack – a biztonsági másolatok megerősítése le lettek tiltva](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

Jelölje be az **automatikus biztonsági mentések engedélyezése** lehetőséget, hogy a Feladatütemezőt tájékoztassa a jövőbeli biztonsági mentésekről az ütemezett időpontban.

![Azure Stack – ütemezett biztonsági mentések engedélyezése](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>Biztonsági mentési beállítások frissítése

A biztonsági mentési adatok titkosításához használt tanúsítvány frissítéséhez töltsön fel egy újat. A nyilvános kulcs részét képező CER-fájl, majd a beállítások mentéséhez kattintson az OK gombra.

Az új biztonsági mentések a nyilvános kulcsot fogják használni az új tanúsítványban. Az előző tanúsítvánnyal létrehozott összes biztonsági mentésre nincs hatással. Ügyeljen arra, hogy a régebbi tanúsítvány biztonságos helyen maradjon, ha szüksége van rá a Felhőbeli helyreállításhoz.

![Azure Stack – tanúsítvány ujjlenyomatának megtekintése](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>Következő lépések

A biztonsági mentés futtatásának ellenőrzéséhez tekintse meg a [biztonsági mentés befejezése a felügyeleti portálon](../../operator/azure-stack-backup-back-up-azure-stack.md)című témakört.
