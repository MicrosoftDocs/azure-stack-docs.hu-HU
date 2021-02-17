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
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 0ea8b5dc805cbd39bae875f55964e2142cbccdb2
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563009"
---
# <a name="billing-model-overview"></a>Számlázási modell áttekintése

Ha a moduláris adatközpont vagy Azure Stack hub felhasználója robusztusnak minősül, a Microsoft az egyes berendezésekhez tartozó időtartam alapján számlázza. A díj időszakonként történik, és magában foglalja az alapszintű számítási, tárolási és hálózati szolgáltatások, App Service, IoT Hub és az előfeltételei, valamint az olyan kiegészítő szolgáltatások használatát, mint például a Key Vault. A Windows Server vendég operációs rendszerként való használata is része.

A számlázás 14 nappal a készülék szállítása után kezdődik. Az Azure Stack hub tárolóban a kezdeti számlázási időszak egy év. Egy év elteltével a rendelés meghosszabbítható egy további évre, míg a használat egy másik évében is megtörténik a számlázás. Ha a rendelés nem lett kiterjesztve, és az eszköz nem lesz visszaadva, a számlázás havi rendszerességgel folytatódik. A Azure Stack hub robusztus, a számlázás mindig havonta történik.

Ha a készüléket visszaküldi a Microsoftnak, a számlázás azonnal leáll, amint a készülék visszaérkezik a Microsoft Warehouse-ba.
