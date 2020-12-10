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
ms.openlocfilehash: b416e219aa599b0329c57ff7515b43afe2cc1ce9
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939832"
---
# <a name="billing-model-overview"></a>Számlázási modell áttekintése

A MDC vagy Azure Stack hub felhasználójának robusztus módon kell fizetnie a Microsofttól az egyes berendezésekhez tartozó időtartam alapján. A díj időszakonként történik, és magában foglalja az alapszintű számítási, tárolási és hálózati szolgáltatások, App Service, IoT Hub és az előfeltételei, valamint az olyan kiegészítő szolgáltatások használatát, mint például a Key Vault. A Windows Server vendég operációs rendszerként való használata is része.

A tényleges használat mérése nem történik meg, és a rendszer nem jelentett jelentést a Microsoftnak.

A számlázás 14 nappal a készülék szállítása után kezdődik. Az Azure Stack hub tárolóban a kezdeti számlázási időszak egy év. Egy év elteltével a rendelés meghosszabbítható egy további évre, míg a használat egy másik évében is megtörténik a számlázás. Ha a rendelés nem lett kiterjesztve, és az eszköz nem lesz visszaadva, a számlázás havi rendszerességgel folytatódik. A Azure Stack hub robusztus, a számlázás mindig havonta történik.

Ha a készüléket visszaküldi a Microsoftnak, a számlázás azonnal leáll, amint a készülék visszaérkezik a Microsoft Warehouse-ba.


