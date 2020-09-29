---
title: Sikertelen meghajtók cseréje Azure Stack HCI-re
description: Sikertelen meghajtók cseréje Azure Stack HCI-re.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: d3d03f1e5cc89186e2eb8198b52e96bffbd21768
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866483"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Sikertelen meghajtók cseréje Azure Stack HCI-re

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI közvetlenül csatlakoztatott SATA-, SAS-, NVMe-vagy állandó memória-meghajtókkal működik, amelyek fizikailag csak egy kiszolgálóhoz csatlakoznak. Ha egy meghajtó meghibásodik, akkor a fizikai kiszolgáló hardveréhez hozzá kell férnie.

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
- Annak megismeréséhez, hogy a tárolási állapot hogyan legyen nyomon követve különböző szinteken, például a meghajtó szintjén, tekintse meg az [állapot és működési állapotok](/windows-server/storage/storage-spaces/storage-spaces-states)című témakört.
- Ha PMem használ, Ismerje meg [és telepítse az állandó memóriát](/windows-server/storage/storage-spaces/deploy-pmem)