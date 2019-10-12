---
title: Fizikai eszközök naplózásának integrálása az Azure Stack adatközponttal | Microsoft Docs
description: Ismerje meg, hogyan integrálhatja a fizikai eszközök hozzáférésének naplózását a Azure Stack adatközpontba.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
keywords: ''
ms.openlocfilehash: b5fa17b3913db7ebec210fc3bf986bac6414368e
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277225"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-datacenter"></a>Fizikai eszközök naplózásának integrálása az Azure Stack adatközponttal

A Azure Stack összes fizikai eszköze, például a alaplapi felügyeleti vezérlők (bmc) és a hálózati kapcsolók, naplókat bocsátanak ki. A naplókat integrálhatja a teljes auditálási megoldásba. Mivel az eszközök különböznek a különböző Azure Stack OEM hardvergyártók között, a naplózási integrációval kapcsolatos dokumentációért forduljon a gyártóhoz. Az alábbi szakaszokban általános információk találhatók a fizikai eszközök naplózásáról Azure Stackban.  

## <a name="physical-device-access-auditing"></a>Fizikai eszközök hozzáférésének naplózása

A Azure Stack összes fizikai eszköze támogatja a TACACS vagy a RADIUS használatát. A támogatás magában foglalja a alaplapi felügyeleti vezérlő (BMC) és a hálózati kapcsolók elérését.

Azure Stack megoldások nem rendelkeznek beépített RADIUS-vagy TACACS. A megoldások azonban érvényesítve lettek a piacon elérhető meglévő RADIUS-vagy TACACS-megoldások használatának támogatásához.

Csak a RADIUS esetében a MSCHAPv2 érvényesítése megtörtént. Ez a legbiztonságosabb implementációt jelenti a RADIUS használatával. Az Azure Stack-megoldásban foglalt eszközökön a TACAS vagy a RADIUS engedélyezéséhez forduljon az OEM-hardver gyártójához.

## <a name="syslog-forwarding-for-network-devices"></a>Syslog-továbbítás hálózati eszközökhöz

A Azure Stack összes fizikai hálózati eszköze támogatja a syslog-üzeneteket. Azure Stack megoldások nem rendelkeznek syslog-kiszolgálóval. Az eszközök azonban érvényesítve lettek, hogy támogassák az üzenetek küldését a piacon elérhető meglévő syslog-megoldásokkal.

A syslog-célcím egy, az üzembe helyezéshez összegyűjtött opcionális paraméter, de az üzembe helyezés után is hozzáadhatók. A syslog-továbbítás konfigurálásához a hálózati eszközökön forduljon az OEM hardver gyártójához.

## <a name="next-steps"></a>Következő lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)
