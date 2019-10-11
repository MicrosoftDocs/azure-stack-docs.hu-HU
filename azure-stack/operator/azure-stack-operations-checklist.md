---
title: Azure Stack műveleti ellenőrzőlista | Microsoft Docs
description: Megtudhatja, milyen gyakori feladatokra van szükség a Azure Stack operátorok számára, és hogy milyen gyakran kell azokat végrehajtani.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/07/2019
ms.openlocfilehash: 246fdc867080da1816f00c9fb6a68179b6944275
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038195"
---
# <a name="azure-stack-operators-checklist"></a>Azure Stack operátor ellenőrzőlistája

Összeállítottunk néhány műveleti ellenőrzőlistát, hogy Azure Stack operátorok sikeresek legyenek. Hozzáadhat olyan konkrét műveleteket, amelyeket el kell végeznie a környezetében. Célja, hogy segítséget nyújtson valakinek, hogy az operátor tudja, mi a napi, heti és havi rendszerességgel. 

## <a name="routine-daily-weekly-and-monthly-operations"></a>Rutin napi, heti és havi műveletei

Azure Stack operátoroknak rendszeresen kell végrehajtaniuk a következőket: 

> [!div class="checklist"]
> * [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) -és [systen-központban](https://docs.microsoft.com/system-center/scom/welcome) lévő hibák figyelése és szervizelése naponta kétszer Operations Manager
> * [Test-AzureStack](azure-stack-diagnostic-test.md) futtatása naponta egyszer
> * Jelentés [Azure stack heti használat](azure-stack-usage-reporting.md) 
> * [OEM belső vezérlőprogram](azure-stack-update-oem.md) frissítése kéthavonta vagy az OEM által bejelentett módon
> * [Azure stack frissítések](release-notes-checklist.md) havi előkészítése

## <a name="day-to-day-operations-as-needed"></a>A napi műveletek igény szerint

A napi Azure Stack operátoroknak is el kell végezniük ezeket a műveleteket: 

> [!div class="checklist"]
> * [Csomagok](azure-stack-create-plan.md) létrehozása
> * [Ajánlatok](azure-stack-create-offer.md) létrehozása
> * [Előfizetések](azure-stack-subscribe-plan-provision-vm.md) létrehozása
> * [Kvóták](azure-stack-quota-types.md) frissítése
> * [Marketplace-minták](azure-stack-create-and-publish-marketplace-item.md) közzététele
> * [Egyéni virtuálisgép-rendszerkép](azure-stack-add-vm-image.md) hozzáadása
> * [Virtuális gépek méretezési csoportjainak](azure-stack-compute-add-scalesets.md) ajánlata 
> * [Marketplace-frissítések](azure-stack-marketplace-changes.md) alkalmazása
> * [Telemetria](azure-stack-telemetry.md) konfigurálása
> * [Régió felügyeletének](azure-stack-region-management.md) áttekintése
> * [Azure stack szolgáltatások leállítása vagy újraindítása](azure-stack-start-and-stop.md) 
> * [Méretezési egység csomópontjának](azure-stack-replace-node.md) cseréje
> * [Fizikai lemez](azure-stack-replace-disk.md) cseréje
> * Hardver- [összetevő](azure-stack-replace-component.md) cseréje
> * A bővítmény erőforrás-szolgáltatóinak, például az [SQL](azure-stack-sql-resource-provider-update.md), a [MySQL](azure-stack-mysql-resource-provider-update.md)és a [app Service](azure-stack-app-service-update.md) frissítése
> * [Diagnosztikai napló-gyűjtemény](azure-stack-diagnostic-log-collection-overview.md) futtatása  



<!---Ask Jeff, Brian, is this everything you do, how can we make it more useful? Theebs has another user.

To be successful, do these things

ask Brian what are all the the things they need to write down for quick access, like passswords, IP addresses, and so on, to make them more efficient

have a password manager for azure-stack, pep, inventory

A checklist so operators can be successful, so someone onboarding is operator know what to do weekly, daily, monthly. --->


