---
title: Az ügyfél számlázási modelljének áttekintése Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan számolják fel a Azure Stack felhasználóit az erőforrás-használatért.
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
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 625ec975ee5f9f3f8a80beb505f2bd10929dcda0
ms.sourcegitcommit: aef251d6771400b21a314bbfbea4591ab263f8fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97726152"
---
# <a name="billing-model-overview"></a>Számlázási modell áttekintése

A MDC vagy Azure Stack hub felhasználójának robusztus módon kell fizetnie a Microsofttól az egyes berendezésekhez tartozó időtartam alapján. A díj időszakonként történik, és magában foglalja az alapszintű számítási, tárolási és hálózati szolgáltatások, App Service, IoT Hub és az előfeltételei, valamint az olyan kiegészítő szolgáltatások használatát, mint például a Key Vault. A Windows Server vendég operációs rendszerként való használata is része.

A számlázás 14 nappal a készülék szállítása után kezdődik. Az Azure Stack hub tárolóban a kezdeti számlázási időszak egy év. Egy év elteltével a rendelés meghosszabbítható egy további évre, míg a használat egy másik évében is megtörténik a számlázás. Ha a rendelés nem lett kiterjesztve, és az eszköz nem lesz visszaadva, a számlázás havi rendszerességgel folytatódik. A Azure Stack hub robusztus, a számlázás mindig havonta történik.

Ha a készüléket visszaküldi a Microsoftnak, a számlázás azonnal leáll, amint a készülék visszaérkezik a Microsoft Warehouse-ba.


