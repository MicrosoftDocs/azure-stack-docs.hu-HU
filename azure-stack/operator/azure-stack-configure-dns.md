---
title: A DNS-továbbító frissítése Azure Stack központban
description: Ismerje meg, hogyan frissítheti a DNS-továbbítót Azure Stack hub-ban.
author: PatAltimore
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 09c827d3b00470dd8e241181fc243bace62fc38b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871174"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>A DNS-továbbító frissítése Azure Stack központban

Legalább egy elérhető DNS-továbbító szükséges ahhoz, hogy a Azure Stack hub-infrastruktúra fel tudja oldani a külső neveket. Azure Stack hub üzembe helyezéséhez meg kell adni egy DNS-továbbítót. Ez a bemenet a Azure Stack hub belső DNS-kiszolgálóihoz továbbítóként használható, és lehetővé teszi a külső névfeloldást olyan szolgáltatások esetében, mint a hitelesítés, a piactér kezelése vagy a használat.

A DNS egy kritikus adatközpont-infrastruktúra szolgáltatás, amely megváltozhat. Ha igen, Azure Stack hub-t frissíteni kell.

Ez a cikk a rendszerjogosultságú végpont (PEP) használatát ismerteti Azure Stack hub DNS-továbbítójának frissítéséhez. Javasoljuk, hogy két megbízható DNS-továbbító IP-címet használjon.

## <a name="steps-to-update-the-dns-forwarder"></a>A DNS-továbbító frissítésének lépései

1. Kapcsolódjon a [Kiemelt végponthoz](azure-stack-privileged-endpoint.md). Egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2. A következő parancs futtatásával tekintse át az aktuálisan konfigurált DNS-továbbítót. Másik lehetőségként használhatja a felügyeleti portál régiójának tulajdonságait is:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. A következő parancs futtatásával frissítse Azure Stack hub-t az új DNS-továbbító használatára:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
