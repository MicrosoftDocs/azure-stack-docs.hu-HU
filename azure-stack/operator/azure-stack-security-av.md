---
title: A Windows Defender víruskereső frissítése
titleSuffix: Azure Stack
description: Ismerje meg, hogyan frissítheti a Windows Defender víruskeresőt Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dc2a58e9b6e8701246a279b1f632ffcdd937c7ca
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780643"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>A Windows Defender víruskereső frissítése Azure Stack

A [Windows Defender Antivirus](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) egy antimalware-megoldás, amely biztonságot és vírusvédelmet biztosít. Minden Azure Stack infrastruktúra-összetevőt (Hyper-V-gazdagépeket és virtuális gépeket) a Windows Defender víruskereső védi. A naprakész védelem érdekében rendszeres frissítések szükségesek a Windows Defender víruskereső-definíciók, a motor és a platform számára. A frissítések alkalmazása a konfigurációtól függ.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

A Azure Stack [Update erőforrás-szolgáltató](azure-stack-updates.md#the-update-resource-provider) naponta többször letölti az antimalware-definíciókat és a motor-frissítéseket. Minden Azure Stack infrastruktúra-összetevő lekéri a frissítést a frissítési erőforrás-szolgáltatótól, és automatikusan alkalmazza a frissítést.

A kártevők elleni platform frissítései esetében alkalmazza a [havi Azure stack frissítést](azure-stack-apply-updates.md). A havi Azure Stack frissítés tartalmazza a Windows Defender víruskereső platformjának frissítéseit a hónapra vonatkozóan.

## <a name="disconnected-scenario"></a>Leválasztott forgatókönyv

 A [havi Azure stack frissítés](azure-stack-apply-updates.md)alkalmazása. A havi Azure Stack frissítés tartalmazza a Windows Defender víruskereső-definíciókat, a motort és a havi platform-frissítéseket.

## <a name="next-steps"></a>Következő lépések

[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md)
