---
title: Sikertelen meghajtók cseréje Azure Stack HCI-re
description: Sikertelen meghajtók cseréje Azure Stack HCI-re.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: e0aaed5d444a0d7b617ecd2ccd350a9812be8a2c
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025435"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Sikertelen meghajtók cseréje Azure Stack HCI-re

A Azure Stack HCI közvetlenül csatlakoztatott SATA-, SAS-vagy NVMe-meghajtókkal működik, amelyek fizikailag csak egy kiszolgálóhoz csatlakoznak. Ha egy meghajtó meghibásodik, akkor a fizikai kiszolgáló hardveréhez hozzá kell férnie.

## <a name="find-the-alert"></a>A riasztás megkeresése
Ha egy meghajtó meghibásodik, a **Windows felügyeleti központ** irányítópultjának **bal felső részén riasztás jelenik** meg. A bal oldali navigációs sávon is kiválaszthatja a **meghajtókat** , vagy a jobb alsó sarokban lévő csempén található **meghajtók megtekintése >** hivatkozásra kattintva böngészhet a meghajtókon, és megtekintheti saját állapotát. A **nézet** lapon a rács támogatja a rendezést, a csoportosítást és a kulcsszavas keresést.

1. Az irányítópulton kattintson a riasztásra, hogy megtekintse a részleteket, például a meghajtó fizikai helyét.
1. További információért kattintson a Ugrás a **meghajtóra** hivatkozásra a **meghajtó** részletei lapon.
1. Ha a hardver támogatja azt, akkor a **fény be-és kikapcsolása** elemre kattintva vezérelheti a meghajtó kijelző fényét.
   Közvetlen tárolóhelyek automatikusan újragumiabroncsokat végez, és kiüríti a sikertelen meghajtókat. Ha ez megtörtént, a meghajtó állapota kimaradt, és a tárolási kapacitás sávjának üresen kell lennie.
1. Távolítsa el a sikertelen meghajtót, és szúrja be a cserét.

## <a name="wait-for-the-alert-to-clear"></a>Várakozás a riasztás törlésére
A **meghajtók > leltárban**megjelenik az új meghajtó. Az idő elteltével a riasztás törölve lesz, a kötetek visszakerülnek az OK állapotba, a tárterület pedig az új meghajtóra lesz kiegyensúlyozva – nincs szükség felhasználói beavatkozásra.

## <a name="next-steps"></a>Következő lépések
-  Annak megismeréséhez, hogy a tárolási állapot hogyan legyen nyomon követve különböző szinteken, például a meghajtó szintjén, tekintse meg az [állapot és működési állapotok](/windows-server/storage/storage-spaces/storage-spaces-states)című témakört.
