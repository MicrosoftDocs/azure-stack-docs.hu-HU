---
title: A CredSSP hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani a CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743539"
---
# <a name="troubleshoot-credssp"></a>A CredSSP hibáinak megoldása

> A következőre vonatkozik Azure Stack HCI, Version v20H2

Egyes Azure Stack HCI-műveletek a Rendszerfelügyeleti webszolgáltatások (WinRM) szolgáltatást használják, ami alapértelmezés szerint nem teszi lehetővé a hitelesítő adatok delegálását. A delegálás engedélyezéséhez a számítógépnek átmenetileg engedélyezni kell a hitelesítő adatok biztonsági támogatásának szolgáltatóját (CredSSP). A CredSSP egy biztonsági támogatási szolgáltató, amely lehetővé teszi, hogy az ügyfél hitelesítő adatokat delegáljon egy kiszolgálónak a távoli hitelesítéshez.

A CredSSP engedélyezése csökkentett teljesítményű biztonsági testhelyzet, és a legtöbb esetben a feladat vagy a művelet befejeződése után le kell tiltani.

Az CredSSP-t igénylő egyes feladatok a következők:

- Fürt létrehozása varázsló munkafolyamata
- Active Directory lekérdezések és frissítések
- SQL Server lekérdezések és frissítések
- Fiókok vagy számítógépek keresése eltérő tartományban vagy tartományon kívüli környezetekben

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ha a CredSSP kapcsolatos problémákat tapasztal, a következő hibaelhárítási tippek segíthetnek:

- Ha a fürt létrehozása varázslót szeretné használni a Windows felügyeleti központ SZÁMÍTÓGÉPeken való futtatásakor, akkor a Windows felügyeleti központ kiszolgálón az átjáró-rendszergazdák csoport tagjának kell lennie. További információ: [felhasználói hozzáférési beállítások a Windows felügyeleti központban](/windows-server/manage/windows-admin-center/plan/user-access-options).

- A fürt létrehozása varázsló futtatásakor a CredSSP jelenthet problémát, ha egy Active Directory megbízhatóság nem jön létre vagy megszakadt. Ez azt eredményezi, hogy a rendszer munkacsoport-alapú kiszolgálókat használ a fürt létrehozásához. Ebben az esetben próbálja meg manuálisan újraindítani az egyes kiszolgálókat a fürtben.

- Ha a Windows felügyeleti központot egy kiszolgálón futtatja, győződjön meg arról, hogy a felhasználói fiók az átjáró-rendszergazdák csoport tagja.

- Javasoljuk, hogy a Windows felügyeleti központot olyan számítógépen futtassa, amely tagja a felügyelt kiszolgálókkal megegyező tartománynak.

- A CredSSP engedélyezéséhez vagy letiltásához a kiszolgálón ellenőrizze, hogy az átjáró-rendszergazdák csoport tagja-e a számítógépen. További információkért tekintse meg a [felhasználói Access Control és engedélyek konfigurálásának](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)első két fejezetét.

- Ha újraindítja a WinRM szolgáltatást a fürtben lévő kiszolgálókon, előfordulhat, hogy újra létre kell hoznia a WinRM-kapcsolatot az egyes fürtcsomópontok és a Windows felügyeleti központ között.

    Ezt úgy teheti meg, hogy az egyes fürtökre kerül, majd a Windows felügyeleti központban az **eszközök** menüben válassza a **szolgáltatások** lehetőséget, válassza a **WinRM** elemet, válassza az **Újraindítás** lehetőséget, majd a **szolgáltatás újraindítása** párbeszédpanelen válassza az **Igen** lehetőséget.

## <a name="manual-troubleshooting"></a>Manuális hibaelhárítás

Ha a következő WinRM-hibaüzenetet kapja, próbálja meg az ebben a szakaszban található kézi ellenőrzési lépéseket a hiba elhárításához. Példa hibaüzenetre:

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

Az ebben a szakaszban található manuális ellenőrzés lépései a következő számítógépek konfigurálását igénylik:
- A Windows felügyeleti központot futtató számítógép
- Az a kiszolgáló, amelyen a hibaüzenetet kapta

A hiba elhárításához próbálja meg a következő korrekciós lépéseket igény szerint:

**1. orvoslás:**
1. Indítsa újra a Windows felügyeleti központot és a kiszolgálót futtató számítógépet.
1. Próbálja meg újból futtatni a fürt létrehozása varázslót.

    A varázsló futtatásával kapcsolatos részletekért lásd: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központból](../deploy/create-cluster.md).

**2. orvoslás:**
1. A Windows felügyeleti központot futtató számítógépen nyissa meg a Windows PowerShellt rendszergazdaként, és futtassa a következő parancsokat:

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. A kiszolgálóhoz való kapcsolódáshoz használja az RDP-szolgáltatást, majd futtassa a következő PowerShell-parancsokat:

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. Próbálja meg újból futtatni a fürt létrehozása varázslót.

    A varázsló futtatásával kapcsolatos részletekért lásd: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központból](../deploy/create-cluster.md).

**3. orvoslás:**
1. A Windows felügyeleti központot futtató számítógépen futtassa a következő PowerShell-parancsot az egyszerű szolgáltatásnév (SPN) vizsgálatához:

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    Az eredménynek a következő kimenetet kell kilistáznia:

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. Ha az eredmények nem szerepelnek a felsorolásban, futtassa a következő PowerShell-parancsokat az SPN regisztrálásához:

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. A kiszolgálóhoz való kapcsolódáshoz használja az RDP-szolgáltatást, majd futtassa a következő PowerShell-parancsot az egyszerű szolgáltatásnév vizsgálatához:

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    Az eredménynek a következő kimenetet kell kilistáznia:

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. Ha az eredmények nem szerepelnek a felsorolásban, futtassa a következő PowerShell-parancsokat az SPN regisztrálásához:

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. Próbálja meg újból futtatni a fürt létrehozása varázslót.

    A varázsló futtatásával kapcsolatos részletekért lásd: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központból](../deploy/create-cluster.md).


**4. orvoslás:**

Ha az előző orvoslási lépések bármelyike meghiúsult vagy nem fejeződött be, ez a rekord ütközését jelezheti Active Directoryban. Egy másik számítógépnév használatával új rekordként állíthatja vissza a rekordot a Active Directoryban.

Az Active Directoryban lévő rekord alaphelyzetbe állításához telepítse újra a Azure Stack HCI operációs rendszert új számítógépnévvel.

## <a name="next-steps"></a>További lépések

További információ a CredSSP: [hitelesítő adatok biztonsági támogatása szolgáltató](/windows/win32/secauthn/credential-security-support-provider).