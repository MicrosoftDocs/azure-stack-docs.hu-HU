---
title: Fizikai eszközök naplózásának integrálása az Azure Stack hub-adatközponttal
description: Ismerje meg, hogyan integrálhatja a fizikai eszközök hozzáférésének naplózását az Azure Stack hub adatközpontjában.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 0ebf71cc6ba794169965fb17181ae497913bf30b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882104"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-hub-datacenter"></a>Fizikai eszközök naplózásának integrálása az Azure Stack hub-adatközponttal

Azure Stack hub összes fizikai eszköze, például a alaplapi felügyeleti vezérlők (bmc) és a hálózati kapcsolók, naplók bocsátanak ki. A naplókat integrálhatja a teljes auditálási megoldásba. Mivel az eszközök eltérnek a különböző Azure Stack hub OEM-hardvergyártók között, a naplózási integrációval kapcsolatos dokumentációért forduljon a gyártóhoz. Az alábbi szakaszokban általános információk találhatók a fizikai eszközök naplózásáról Azure Stack központban.  

## <a name="physical-device-access-auditing"></a>Fizikai eszközök hozzáférésének naplózása

Az Azure Stack hub összes fizikai eszköze támogatja a TACACS vagy a RADIUS használatát. A támogatás magában foglalja a alaplapi felügyeleti vezérlő (BMC) és a hálózati kapcsolók elérését.

Azure Stack hub-megoldások nem szállítanak beépített RADIUS-vagy TACACS. A megoldások azonban érvényesítve lettek a piacon elérhető meglévő RADIUS-vagy TACACS-megoldások használatának támogatásához.

Csak a RADIUS esetében a MSCHAPv2 érvényesítése megtörtént. Ez a legbiztonságosabb implementációt jelenti a RADIUS használatával. Az Azure Stack hub-megoldásban foglalt eszközökön a TACAS vagy a RADIUS engedélyezéséhez forduljon az OEM hardver gyártójához.

## <a name="syslog-forwarding-for-network-devices"></a>Syslog-továbbítás hálózati eszközökhöz

Az Azure Stack hub összes fizikai hálózati eszköze támogatja a syslog-üzeneteket. Azure Stack hub-megoldások nem rendelkeznek syslog-kiszolgálóval. Az eszközök azonban érvényesítve lettek, hogy támogassák az üzenetek küldését a piacon elérhető meglévő syslog-megoldásokkal.

A syslog-célcím egy, az üzembe helyezéshez összegyűjtött opcionális paraméter, de az üzembe helyezés után is hozzáadhatók. A syslog-továbbítás konfigurálásához a hálózati eszközökön forduljon az OEM hardver gyártójához.

## <a name="next-steps"></a>Következő lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)
