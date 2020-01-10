---
title: A DNS-továbbító frissítése Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan frissítheti a DNS-továbbítót Azure Stack hub-ban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b16eade221c51664205e865d1680e7f048fbfc7a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817584"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>A DNS-továbbító frissítése Azure Stack központban

Legalább egy elérhető DNS-továbbító szükséges ahhoz, hogy a Azure Stack hub-infrastruktúra fel tudja oldani a külső neveket. Azure Stack hub üzembe helyezéséhez meg kell adni egy DNS-továbbítót. Ezt a bemenetet használja a Azure Stack hub belső DNS-kiszolgálóihoz továbbítóként, és lehetővé teszi a külső névfeloldást olyan szolgáltatások esetében, mint a hitelesítés, a piactér kezelése vagy a használat.

A DNS egy kritikus adatközpont-infrastruktúra szolgáltatás, amely megváltoztatható, és ha igen, akkor Azure Stack hub-t frissíteni kell.

Ez a cikk a rendszerjogosultságú végpont (PEP) használatát ismerteti Azure Stack hub DNS-továbbítójának frissítéséhez. Javasoljuk, hogy két megbízható DNS-továbbító IP-címet használjon.

1. Kapcsolódjon a [Kiemelt végponthoz](azure-stack-privileged-endpoint.md). Vegye figyelembe, hogy egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2. A következő parancs futtatásával tekintse át az aktuálisan konfigurált DNS-továbbítót. Másik lehetőségként használhatja a felügyeleti portál régiójának tulajdonságait is:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. A következő parancs futtatásával frissítse Azure Stack hub-t az új DNS-továbbító használatára:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.

## <a name="next-steps"></a>Következő lépések

[Tűzfal-integráció](azure-stack-firewall.md)
