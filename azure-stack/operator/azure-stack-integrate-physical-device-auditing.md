---
title: Fizikai eszközök naplózásának integrálása az Azure Stack hub-adatközponttal
description: Ismerje meg, hogyan integrálhatja a fizikai eszközök hozzáférésének naplózását az Azure Stack hub adatközpontjában.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 2bfef311e8a57187aef9e8af2c4f110f8dd54ba2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870970"
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

## <a name="next-steps"></a>További lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)
