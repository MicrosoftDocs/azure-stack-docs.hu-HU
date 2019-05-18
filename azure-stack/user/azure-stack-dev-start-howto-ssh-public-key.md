---
title: Nyilvános SSH-kulcs használata az Azure Stackkel való használathoz |} A Microsoft Docs
description: Nyilvános SSH-kulcs használata
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: c0b55579c5103c7bb1073546243dbfcc0b700b4a
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838395"
---
# <a name="use-an-ssh-public-key"></a>Nyilvános SSH-kulcsot használ

SSH-kapcsolat a fejlesztői gépről a kiszolgáló virtuális Gépét az Azure Stack-példányában használandó futtató a webalkalmazásokat, előfordulhat, hogy szeretne létrehozni egy Secure Shell (SSH) nyilvános és titkos kulcsból álló párként. 

Ebben a cikkben, a kulcsok létrehozásához, majd azokat a kiszolgálóhoz való csatlakozáshoz. Használhatja az SSH-ügyfelet egy bash-parancssort a Linux-kiszolgálón vagy egy biztonságos FTP (SFTP) ügyfél használatával helyezze át a fájlokat, és a kiszolgálóról.

## <a name="create-an-ssh-public-key-on-windows"></a>A Windows egy nyilvános SSH-kulcs létrehozása

Ebben a szakaszban a PuTTY Key Generator hozzon létre egy nyilvános SSH-kulcsot és titkos kulcsból álló kulcspárt Linux rendszerű gépek biztonságos kapcsolatot hoz létre az Azure Stack-példányában használandó használhatja. A puTTY egy ingyenes terminálemulátorral, amely lehetővé teszi, az SSH és a Telnet használatával egy kiszolgálóhoz való csatlakozáshoz.

1. [Töltse le és telepítse a Puttyt a gép.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Nyissa meg a PuTTY Key Generator.

    ![A kulcs üres mező puTTY Key Generator](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. A **paraméterek**válassza **RSA**.

1. Az a **létrehozott kulcs bitjeinek száma** mezőbe írja be **2048**.  

1. Válassza ki **készítése**.

1. Az a **kulcs** területet, hozzon létre egy véletlenszerű karakter helyezi a kurzort az üres területen.

    ![A feltöltött kulcs mezőben a puTTY Key Generator](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Adjon meg egy **kulcs hozzáférési kódot** , és győződjön meg arról, az a **jelszó megerősítése** mezőbe. Megjegyzés: a megadott jelszó későbbi használatra.

1. Válassza ki **mentés nyilvános kulcs**, és mentse egy olyan helyre, ahol hozzá tud férni.

1. Válassza ki **titkos kulcs mentése**, és mentse egy olyan helyre, ahol hozzá tud férni. Ne feledje, hogy nyilvános kulcshoz tartozik.

A nyilvános kulcsot a mentett szövegfájlban tárolódnak. A szöveg a következőhöz hasonlóan néz ki:

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Amikor egy alkalmazás a kulcsot, másolja és illessze be a szöveges fájl teljes tartalmát.

## <a name="connect-with-ssh-by-using-putty"></a>Csatlakozás SSH-n keresztül a PuTTY használatával

Telepítse a Puttyt, ha rendelkezik a PuTTY Key Generator és a egy SSH-ügyfél. Ebben a szakaszban, nyissa meg az SSH-ügyfél, a PuTTY, és konfigurálja a csatlakozási értékeket és SSH-kulcsot. Ha Ön az Azure Stack-példány ugyanazon a hálózaton, a virtuális Géphez csatlakozik.

Csatlakozás előtt lesz szüksége:
- Putty-kapcsolaton keresztül
- Az IP-cím és a Linux-gép az Azure Stack-példányban, amely egy nyilvános SSH-kulcsot használ a hitelesítési típus tartozó felhasználónév.
- A gép megnyitni a 22-es port.
- A nyilvános SSH-kulcsot, amelyet a gép létrehozásakor használt.
- Az ügyfélszámítógépen, amely futtatja a putty-kapcsolaton keresztül kell ugyanazon a hálózaton található, mint az Azure Stack-példány.

1. Nyissa meg a PuTTY eszközt.

    ![A PuTTY konfigurációs panelen](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Az a **gazdagép nevét (vagy IP-cím)** mezőbe írja be a felhasználónevet és a gép nyilvános IP-címét (például **username@192.XXX.XXX.XX**). 
3. Ellenőrizze, hogy a **Port** van **22-es** és a **kapcsolattípus** van **SSH**.
4. Az a **kategória** fán, bontsa ki a **SSH** és **Auth**.

    ![A PuTTY konfigurációs panelen - SSH titkos kulcs](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Mellett a **titkos kulcs fájlját a hitelesítéshez** jelölje ki **Tallózás**, majd keresse meg a titkos kulcs fájlját (*\<filename > .ppk*) a nyilvános és titkos kulcsból álló párként.
6. Az a **kategória** konzolfáján válassza **munkamenet**.

    ![A PuTTY konfigurációs panelen "Mentett munkamenetek" képernyő](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. A **mentett munkamenetek**, adjon meg egy nevet a munkamenetet, és válassza **mentése**.
8. Az a **mentett munkamenetek** listában, majd válassza ki és válassza ki a nevét, a munkamenet **terhelés**.
9. Válassza az **Open** (Megnyitás) elemet. Megnyílik az SSH-munkamenetből.

## <a name="connect-with-sftp-with-filezilla"></a>SFTP Filezillát való összekapcsolása

Helyezze át a fájlokat, és a Linux rendszerű gép, Filezillát, az FTP-ügyfél, amely támogatja a biztonságos FTP (SFTP) használhatja. A Windows 10, Linux és macOS Filezillát fut. A Filezillát ügyfél támogatja az FTP-, FTP, a TLS (FTPS), valamint az SFTP. Nyílt forráskódú szoftverek elosztott a GNU általános nyilvános licenc feltételei szerint ingyenesen elérhető.

### <a name="set-your-connection"></a>A kapcsolat beállítása

1. [Töltse le és telepítse a Filezillát](https://filezilla-project.org/download.php).
1. Nyissa meg a Filezillát.
1. Válassza ki **fájl** > **Webhelykezelője**.

    ![A Site Manager Filezillát panel](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Az a **protokoll** legördülő listában válassza **SFTP - SSH File Transfer Protocol**.
1. Az a **gazdagép** mezőbe írja be a nyilvános IP-cím a gép.
1. Az a **bejelentkezési típus** jelölje ki **normál**.
1. Adja meg a felhasználónevét és jelszavát.
1. Kattintson az **OK** gombra.
1. Válassza ki **szerkesztése** > **beállítások**.

    ![A Filezillát beállítások panel](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. A a **kiválasztása lap** fán, bontsa ki a **kapcsolat**, majd válassza ki **SFTP**.
1. Válassza ki **Hozzáadás kulcsfájl**, majd adja meg a titkos kulcs fájlját (például  *\<filename > .ppk*).
1. Kattintson az **OK** gombra.

### <a name="open-your-connection"></a>A kapcsolat megnyitása

1. Nyissa meg a Filezillát.
1. Válassza ki **fájl** > **Webhelykezelője**.
1. A webhely nevét, majd válassza ki és **Connect**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állítsa be a fejlesztési környezetet az Azure Stackben](azure-stack-dev-start.md).
