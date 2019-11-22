---
title: A DNS-továbbító frissítése a Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan frissítheti a DNS-továbbítót a Azure Stackban.
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
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308201"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>A DNS-továbbító frissítése Azure Stack

Legalább egy elérhető DNS-továbbító szükséges ahhoz, hogy a Azure Stack-infrastruktúra fel tudja oldani a külső neveket. Azure Stack telepítéséhez meg kell adni egy DNS-továbbítót. Ez a bemenet a Azure Stack belső DNS-kiszolgálóinak továbbítóként való használata, valamint a külső névfeloldás engedélyezése olyan szolgáltatások esetében, mint a hitelesítés, a piactér kezelése vagy a használat.

A DNS egy kritikus adatközpont-infrastruktúra szolgáltatás, amely megváltoztathatja, és ha igen, Azure Stack frissítenie kell.

Ez a cikk a rendszerjogosultságú végpont (PEP) használatával frissíti a DNS-továbbítót a Azure Stackban. Javasoljuk, hogy két megbízható DNS-továbbító IP-címet használjon.

1. Kapcsolódjon a [Kiemelt végponthoz](azure-stack-privileged-endpoint.md). Vegye figyelembe, hogy egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2. A következő parancs futtatásával tekintse át az aktuálisan konfigurált DNS-továbbítót. Másik lehetőségként használhatja a felügyeleti portál régiójának tulajdonságait is:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Futtassa a következő parancsot a Azure Stack frissítéséhez az új DNS-továbbító használatára:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
