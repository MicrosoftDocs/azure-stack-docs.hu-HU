---
title: A CredSSP hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani a CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899907"
---
# <a name="troubleshoot-credssp"></a>A CredSSP hibáinak megoldása

> A következőre vonatkozik Azure Stack HCI, Version v20H2

Egyes Azure Stack HCI-műveletek a Rendszerfelügyeleti webszolgáltatások (WinRM) szolgáltatást használják, ami alapértelmezés szerint nem teszi lehetővé a hitelesítő adatok delegálását. A delegálás engedélyezéséhez a számítógépnek átmenetileg engedélyezni kell a hitelesítő adatok biztonsági támogatásának szolgáltatóját (CredSSP). A CredSSP egy olyan biztonsági támogatási szolgáltató, amely lehetővé teszi az ügyfél számára a hitelesítő adatok delegálását egy célkiszolgálón a távoli hitelesítéshez. 

A CredSSP engedélyezése csökkentett teljesítményű biztonsági testhelyzet, és a legtöbb esetben a feladat vagy a művelet befejeződése után le kell tiltani.

Az CredSSP-t igénylő egyes feladatok a következők:

- Fürt létrehozása varázsló munkafolyamata
- Active Directory lekérdezések és frissítések
- SQL Server-lekérdezések vagy-frissítések
- Fiókok vagy számítógépek keresése eltérő tartományban vagy tartományon kívüli környezetekben

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ha a CredSSP kapcsolatos problémákat tapasztal, a következő hibaelhárítási tippek segíthetnek:

- A fürt létrehozása varázsló futtatásakor a CredSSP jelenthet problémát, ha egy Active Directory megbízhatóság nem jön létre vagy megszakadt. Ez azt eredményezi, hogy a rendszer munkacsoport-alapú kiszolgálókat használ a fürt létrehozásához. Ebben az esetben próbálja meg manuálisan újraindítani az egyes kiszolgálókat a fürtben.

- Ha a Windows felügyeleti központot egy kiszolgálón (szolgáltatási módban) futtatja, győződjön meg arról, hogy a felhasználói fiók az átjáró-rendszergazdák csoport tagja.

- A CredSSP engedélyezéséhez vagy letiltásához a kiszolgálón ellenőrizze, hogy az átjáró-rendszergazdák csoport tagja-e a számítógépen. További információkért tekintse meg a [felhasználói Access Control és engedélyek konfigurálásának](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)első két fejezetét.

## <a name="next-steps"></a>Következő lépések

További információ a CredSSP: [hitelesítő adatok biztonsági támogatása szolgáltató](/windows/win32/secauthn/credential-security-support-provider).