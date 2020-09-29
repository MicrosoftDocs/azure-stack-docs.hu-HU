---
title: Azure Stack hub diagnosztikai naplóinak küldése
description: Megtudhatja, hogyan gyűjthet diagnosztikai naplókat igény szerint Azure Stack hub-ban a felügyeleti portál vagy egy PowerShell-parancsfájl használatával.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 3d4725a832aa1ba88c0ecc5e2b435696cba3bbcd
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573156"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Azure Stack hub diagnosztikai naplóinak küldése

::: moniker range=">= azs-2002"

Azure Stack operátorok igény szerint elküldhetik a diagnosztikai naplókat, hogy a támogatás kérelmezése előtt Microsoft ügyfélszolgálata a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, a felügyeleti portálon a **naplók elküldése most** lehetőség is ajánlott, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, a kezelők Ehelyett a [Send-AzureStackDiagnosticLog használatával küldenek naplókat](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002). 

Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. A következő folyamatábra azt mutatja be, hogy mely lehetőség használható a diagnosztikai naplók küldésére az egyes esetekben. 

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>A naplók igény szerinti gyűjtésének alternatívájaként a [diagnosztikai naplók proaktív összegyűjtésével](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)egyszerűsítheti a hibaelhárítási folyamatot. Ha a rendszerállapot-feltételek kivizsgálására van szükség, a rendszer automatikusan feltölti a naplókat az elemzéshez, mielőtt megnyit egy esetet Microsoft ügyfélszolgálata. Ha az előjelzéses naplók gyűjtése engedélyezve van, a **Súgó és támogatás** funkció azt mutatja be, hogy mikor van folyamatban a naplózási gyűjtemény. Ha a **naplók küldése most** lehetőségre kattint a naplók egy adott időpontból történő gyűjtéséhez, miközben az előjelzéses napló gyűjtése folyamatban van, az igény szerinti gyűjtemény az előjelzéses naplózási gyűjtemény befejeződése után kezdődik.

Itt adhatja meg a naplók kezdési és befejezési idejét, majd kattintson a **gyűjtés és feltöltés**gombra. 

![Képernyőfelvétel a naplók küldéséről](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range=">= azs-2005"
## <a name="save-logs-locally"></a>Naplók helyi mentése

A naplók helyi SMB-megosztásba menthetők, ha Azure Stack hub le van választva az Azure-ból. A **Settings (beállítások** ) panelen adja meg az elérési utat és egy felhasználónevet és jelszót, amely jogosult a megosztásba való írásra. A támogatási esetekben a Microsoft ügyfélszolgálata részletesen ismerteti az átvitt helyi naplók beolvasásának lépéseit. Ha a felügyeleti portál nem érhető el, a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) használatával helyileg mentheti a naplókat.

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>A Súgó és támogatás használatával igény szerint gyűjthet diagnosztikai naplókat

A probléma elhárításához Microsoft ügyfélszolgálata kérhet egy Azure Stack hub-kezelőt, hogy igény szerint gyűjtsön diagnosztikai naplókat az előző hét egy adott időablakára vonatkozóan. Ebben az esetben a Microsoft ügyfélszolgálata a kezelőt egy SAS URL-címmel fogja megadni a gyűjtemény feltöltéséhez. 
A következő lépésekkel konfigurálhatja az igény szerinti naplózási gyűjteményt a Microsoft ügyfélszolgálata SAS URL-címével:

1. Nyissa meg a **Súgó + támogatás > a naplók gyűjtését > naplókat**. 
1. Válasszon egy 1-4 órás csúszó ablakot az elmúlt hét napban. 
1. Válassza ki a helyi időzónát.
1. Adja meg a Microsoft ügyfélszolgálata megadott SAS URL-címet.

   ![Képernyőkép az igény szerinti naplók gyűjtéséről](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Ha az automatikus diagnosztikai napló gyűjteménye engedélyezve van, a **Súgó és támogatás** megjeleníti a naplózási gyűjtemény folyamatát. Ha a **naplók gyűjtése** gombra kattint a naplók egy adott időpontból való összegyűjtéséhez, miközben az automatikus naplózási gyűjtemény folyamatban van, az igény szerinti gyűjtemény az automatikus naplózási gyűjtemény befejeződése után kezdődik. 


::: moniker-end


## <a name="next-steps"></a>Következő lépések

[A rendszerjogosultságú végpont (PEP) használata Azure Stack hub diagnosztikai naplói küldéséhez](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)
