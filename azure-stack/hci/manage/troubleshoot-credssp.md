---
title: A CredSSP hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani a CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f90e03c275c4ca7a28a9d8392351bf55d0adb2c0
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010838"
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

- Ha a fürt létrehozása varázslót szeretné használni a Windows felügyeleti központ SZÁMÍTÓGÉPeken való futtatásakor, akkor a Windows felügyeleti központ kiszolgálón az átjáró-rendszergazdák csoport tagjának kell lennie. További információ: [felhasználói hozzáférési beállítások a Windows felügyeleti központban](/windows-server/manage/windows-admin-center/plan/user-access-options).

- A fürt létrehozása varázsló futtatásakor a CredSSP jelenthet problémát, ha egy Active Directory megbízhatóság nem jön létre vagy megszakadt. Ez azt eredményezi, hogy a rendszer munkacsoport-alapú kiszolgálókat használ a fürt létrehozásához. Ebben az esetben próbálja meg manuálisan újraindítani az egyes kiszolgálókat a fürtben.

- Ha a Windows felügyeleti központot egy kiszolgálón (szolgáltatási módban) futtatja, győződjön meg arról, hogy a felhasználói fiók az átjáró-rendszergazdák csoport tagja.

- Javasoljuk, hogy a Windows felügyeleti központot olyan számítógépen futtassa, amely tagja a felügyelt kiszolgálókkal megegyező tartománynak.

- A CredSSP engedélyezéséhez vagy letiltásához a kiszolgálón ellenőrizze, hogy az átjáró-rendszergazdák csoport tagja-e a számítógépen. További információkért tekintse meg a [felhasználói Access Control és engedélyek konfigurálásának](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)első két fejezetét.

- Ha újraindítja a Rendszerfelügyeleti webszolgáltatások (WinRM) szolgáltatást a fürtben lévő kiszolgálókon, a rendszer felszólíthatja, hogy hozza létre újra a WinRM-kapcsolatot az egyes kiszolgálófürt és a Windows felügyeleti központ között.

    Ezt úgy teheti meg, hogy az egyes fürtökre kerül, majd a Windows felügyeleti központban az **eszközök** menüben válassza a **szolgáltatások** lehetőséget, válassza a **WinRM** elemet, válassza az **Újraindítás** lehetőséget, majd a **szolgáltatás újraindítása** párbeszédpanelen válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a CredSSP: [hitelesítő adatok biztonsági támogatása szolgáltató](/windows/win32/secauthn/credential-security-support-provider).