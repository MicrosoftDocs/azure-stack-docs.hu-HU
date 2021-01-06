---
title: Az ügyfél számlázási modelljének áttekintése a Azure Stack-MDC | Microsoft Docs
description: Megtudhatja, hogyan számolnak fel Azure Stack felhasználókat egy moduláris adatközpontban (MDC) az erőforrás-használatért.
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
ms.date: 12/04/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 15f894a668374be5380f322d368b76d88bb93cba
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910754"
---
# <a name="billing-model-overview---modular-data-center-mdc"></a>Számlázási modell áttekintése – moduláris adatközpont (MDC)

A MDC vagy Azure Stack hub felhasználójának robusztus módon kell fizetnie a Microsofttól az egyes berendezésekhez tartozó időtartam alapján. A díj időszakonként, és magában foglalja az alapszintű számítási, tárolási és hálózati szolgáltatások használatának jogát. A díjszabás a App Service, a Event Hubs és bármely más, a többi Péter-szolgáltatásra, valamint a Windows Server TB-ra épülő, Azure Stack hub-on futó virtuális gépekre és a MDC. Ha teljesen le van választva, és nem tud jelentést készíteni a használati adatokról, be kell szereznie a frissítéshez szükséges kapacitási licenceket a Windows rendszerű virtuális gépekhez.

A számlázás 14 nappal a készülék szállítása után kezdődik. Az Azure Stack hub tárolóban a kezdeti számlázási időszak egy év. Egy év elteltével a rendelés meghosszabbítható egy további évre, míg a használat egy másik évében is megtörténik a számlázás. Ha a rendelés nem lett kiterjesztve, és az eszköz nem lesz visszaadva, a számlázás havi rendszerességgel folytatódik. A Azure Stack hub robusztus, a számlázás mindig havonta történik.

Ha a készüléket visszaküldi a Microsoftnak, a számlázás azonnal leáll, amint a készülék visszaérkezik a Microsoft Warehouse-ba.

## <a name="next-steps"></a>Következő lépések

[Használati API-referenciák](analyze-usage-tzl.md)
