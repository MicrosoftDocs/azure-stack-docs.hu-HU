---
title: Frissítés a Windows Defender víruskereső az Azure Stackben
description: A víruskereső részleteiért van mindig naprakész az Azure Stackben
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 6e9c4a5334ce44da777126f09ca0ecf578ccfcbd
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828109"
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
