---
title: Azure Stack hub diagnosztikai naplóinak küldése
description: Megtudhatja, hogyan gyűjthet diagnosztikai naplókat igény szerint Azure Stack hub-ban a felügyeleti portál vagy egy PowerShell-parancsfájl használatával.
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2a46c031be648eedf42e204496ed2a123c926dd9
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402722"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Azure Stack hub diagnosztikai naplóinak küldése

Azure Stack operátorok igény szerinti diagnosztikai naplókat küldhetnek a Microsoft ügyfél-támogatási szolgáltatásainak (CSS) a támogatás kérelmezése előtt a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, a felügyeleti portálon a **naplók elküldése most** lehetőség is ajánlott, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, a kezelők Ehelyett a [Send-AzureStackDiagnosticLog használatával küldenek naplókat](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. A következő folyamatábra azt mutatja be, hogy mely lehetőség használható a diagnosztikai naplók küldésére az egyes esetekben. 

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>A naplók igény szerinti gyűjtésének alternatívájaként a [diagnosztikai naplók proaktív összegyűjtésével](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)egyszerűsítheti a hibaelhárítási folyamatot. Ha a rendszerállapot-feltételek kivizsgálására van szükség, a rendszer automatikusan feltölti a naplókat az elemzésekhez, mielőtt a CSS-sel megnyit egy esetet. Ha az előjelzéses naplók gyűjtése engedélyezve van, a **Súgó és támogatás** funkció azt mutatja be, hogy mikor van folyamatban a naplózási gyűjtemény. Ha a **naplók küldése most** lehetőségre kattint a naplók egy adott időpontból történő gyűjtéséhez, miközben az előjelzéses napló gyűjtése folyamatban van, az igény szerinti gyűjtemény az előjelzéses naplózási gyűjtemény befejeződése után kezdődik.

Itt adhatja meg a naplók kezdési és befejezési idejét, majd kattintson a **gyűjtés és feltöltés**gombra. 

![Képernyőfelvétel a naplók küldéséről](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Következő lépések

[A rendszerjogosultságú végpont (PEP) használata Azure Stack hub diagnosztikai naplói küldéséhez](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)
