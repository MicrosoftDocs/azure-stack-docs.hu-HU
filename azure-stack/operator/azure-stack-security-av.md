---
title: Frissítés a Windows Defender víruskereső az Azure Stackben
description: A víruskereső részleteiért van mindig naprakész az Azure Stackben
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 01/14/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7899f235853991160e4d8b2c2c832d9a77171e83
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293662"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Frissítés a Windows Defender víruskereső az Azure Stackben

[A Windows Defender víruskereső](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) kártevőirtó megoldás, amely a biztonsági és védelmet biztosít. Minden Azure Stack-infrastruktúra összetevő (a Hyper-V-gazdagépek és virtuális gépeket) a Windows Defender víruskereső használható védett. A védelem naprakészen a Windows Defender víruskereső-definíciókat, a motor és a platform rendszeres frissítés szükséges. Konfigurációjától függ, hogy hogyan telepítse a frissítéseket.

## <a name="connected-scenario"></a>Csatlakoztatott forgatókönyv

A kártevőirtó definíciójának és keresőmotor-frissítések, az Azure Stack [frissítés erőforrás-szolgáltató](azure-stack-updates.md#the-update-resource-provider) letölti a kártevőirtó-definíciókat és motor-frissítések naponként több alkalommal. Minden egyes Azure Stack-infrastruktúra összetevője a frissítés beolvassa a frissítési erőforrás-szolgáltató, és automatikusan alkalmazza a frissítést.

A kártevőirtó platform frissítéseinek alkalmazása a [havi Azure Stack-frissítés](azure-stack-apply-updates.md). A havi Azure Stack-frissítés magában foglalja a Windows Defender víruskereső platform frissítései hónap.

## <a name="disconnected-scenario"></a>Kapcsolat nélküli forgatókönyv

 Alkalmazza a [havi Azure Stack-frissítés](azure-stack-apply-updates.md). A havi Azure Stack-frissítés is tartalmaz, a Windows Defender víruskereső definíciókat, motor és a hónap frissítései platform.

## <a name="next-steps"></a>További lépések

[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md)
