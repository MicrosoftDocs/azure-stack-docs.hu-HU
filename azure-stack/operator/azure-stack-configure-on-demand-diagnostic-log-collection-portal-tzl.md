---
title: Azure Stack hub diagnosztikai naplóinak küldése
description: Megtudhatja, hogyan gyűjthet diagnosztikai naplókat igény szerint Azure Stack hub-ban a felügyeleti portál vagy egy PowerShell-parancsfájl használatával.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: fb79a6378d2dec69804019b3ab0648ce874bf99d
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520308"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Azure Stack hub diagnosztikai naplóinak küldése

Azure Stack operátorok igény szerinti diagnosztikai naplókat küldhetnek a Microsoft ügyfél-támogatási szolgáltatásainak (CSS) a támogatás kérelmezése előtt a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, a felügyeleti portálon a **naplók elküldése most** lehetőség is ajánlott, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, a kezelőknek Ehelyett a [PowerShell használatával kell elküldeni a naplókat](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. A következő folyamatábra azt mutatja be, hogy mely lehetőség használható a diagnosztikai naplók küldésére az egyes esetekben. 

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>A naplók igény szerinti gyűjtésének alternatívájaként a [diagnosztikai naplók proaktív összegyűjtésével](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)egyszerűsítheti a hibaelhárítási folyamatot. Ha a rendszerállapot-feltételek kivizsgálására van szükség, a rendszer automatikusan feltölti a naplókat az elemzésekhez, mielőtt a CSS-sel megnyit egy esetet. Ha az előjelzéses naplók gyűjtése engedélyezve van, a **Súgó és támogatás** funkció azt mutatja be, hogy mikor van folyamatban a naplózási gyűjtemény. Ha a **naplók küldése most** lehetőségre kattint a naplók egy adott időpontból történő gyűjtéséhez, miközben az előjelzéses napló gyűjtése folyamatban van, az igény szerinti gyűjtemény az előjelzéses naplózási gyűjtemény befejeződése után kezdődik.

Itt adhatja meg a naplók kezdési és befejezési idejét, majd kattintson a **gyűjtés és feltöltés**gombra. 

![Képernyőfelvétel a naplók küldéséről](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Következő lépések

[A rendszerjogosultságú végpont (PEP) használata Azure Stack hub diagnosztikai naplói küldéséhez](azure-stack-get-azurestacklog.md)
