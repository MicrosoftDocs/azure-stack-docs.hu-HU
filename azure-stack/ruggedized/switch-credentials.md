---
title: Hitelesítő adatok váltása
description: A cikk azt ismerteti, hogyan lehet frissíteni a kapcsoló hitelesítő adatait egy Azure Stack hub számára
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939963"
---
# <a name="switch-credentials"></a>Hitelesítő adatok váltása

Ebből a témakörből megtudhatja, hogyan módosíthatja a rendszergazda (rendszergazdai) és a Simple Network Management Protocol (SNMP) hitelesítő adatait az összes kapcsolón. 

## <a name="prerequisites"></a>Előfeltételek

Az eljárások futtatása előtt:

- A HLH való kapcsolódáshoz használjon Távoli asztal.
- Keresse meg a PuTTY-t a HLH, általában E:\Tools\Putty\putty.exe. Ha a PuTTY nem érhető el, töltse le és másolja át a HLH.
- Győződjön meg arról, hogy rendelkezik a kapcsolók aktuális és új hitelesítő adataival.

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>A rendszergazda hitelesítő adatainak frissítése és fiókok engedélyezése 

A skálázási egység minden kapcsolója esetében (BMC, TOR1 és TOR2):

1. A HLH a PuTTY használatával jelentkezzen be a kapcsolóba az aktuális hitelesítő adatok használatával. 
1. Futtassa a következő parancsot, \<new password\> és cserélje le az új rendszergazdát, és engedélyezze a hitelesítő adatokat. 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. Hagyja nyitva az aktuális munkamenetet a kapcsolón.
1. A HLH a PuTTY használatával jelentkezzen be a kapcsolóba az új hitelesítő adatok használatával.
1. Futtassa az alábbi parancsot. Az engedélyezés nem kérheti a jelszót.

   ```ini
   enable
   write
   dir flash:
   ```

1. Ellenőrizze, hogy a Startup-config szolgáltatás mai dátummal rendelkezik-e.
1. Erősítse meg, hogy a munkamenet és az eredeti munkamenet is be van zárva.

## <a name="update-snmp-accounts"></a>SNMP-fiókok frissítése

A skálázási egység minden kapcsolója esetében (BMC, TOR1 és TOR2):

1. A HLH a PuTTY használatával jelentkezzen be a kapcsolóba.
1. Futtassa a következő parancsot az aktuális SNMP olvasási/írási felhasználók és csoportok lekéréséhez:

   ```ini
   enable
   show run snmp | grep user
   ```

   Az alábbi példa a visszaadott tartalom típusát mutatja be, például a felhasználó, a csoportnév és a jelszó kivonatait:

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. Futtassa az alábbi parancsot, \<user\> és cserélje \<group\> le az előző lépésben meghatározott adatokat, és cserélje le az \<password\> Új jelszóra:

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Hub titkos kulcsainak rotálása](../operator/azure-stack-rotate-secrets.md)