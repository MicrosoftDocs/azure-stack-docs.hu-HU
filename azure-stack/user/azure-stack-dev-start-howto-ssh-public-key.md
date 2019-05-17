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
ms.openlocfilehash: ca8d81e9d0baf08adde257340b2a8ac3bffd6c36
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783191"
---
# <a name="how-to-use-an-ssh-public-key"></a>Nyilvános SSH-kulcs használata

Előfordulhat, hogy szeretne létrehozni a nyilvános és titkos ssh-kulcs egy SSH-kapcsolatának megnyitása a fejlesztői gépen, és a kiszolgáló virtuális gép használata az Azure Stack a webalkalmazás üzemeltetéséhez. Ez a cikk végigvezeti a lépéseken, a kulcsok beolvasása és a kulcsokat használ a kiszolgálóhoz való csatlakozáshoz. SSH-ügyfelet úgy szerezheti be a Linux-kiszolgálón egy bash-parancssort, vagy a fájlok áthelyezése a kiszolgálótól érkező és a egy SFTP-ügyfél is használhatja.

## <a name="create-an-ssh-public-key-on-windows"></a>A Windows egy nyilvános SSH-kulcs létrehozása

Ebben a szakaszban a PuTTY key generator használandó hozzon létre egy nyilvános SSH-kulcsot és titkos kulcsból álló kulcspárt a Linux rendszerű gépek biztonságos kapcsolatot az Azure Stack létrehozásakor. A puTTY egy ingyenes terminálemulátorral, amely lehetővé teszi, az SSH és a Telnet használatával egy kiszolgálóhoz való csatlakozáshoz.

1. [Töltse le és telepítse a Puttyt a gép.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Nyissa meg a PuTTY Key Generator.

1. Állítsa be a **paraméterek** való **RSA**.

1. Bitek számának beállítása egy létrehozott kulcsot a `2048`.  

    ![A PuTTY használatával létrehozhat egy nyilvános SSH-kulcs](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Válassza ki **készítése**. Az a **kulcs** területet, hozzon létre néhány véletlenszerűségre azért helyezi a kurzort az üres területen.

1. Adjon hozzá egy **kulcs hozzáférési kódot** , és győződjön meg arról, a a **megerősítése** mezőbe. Jegyezze meg a jelszót.
    ![A PuTTY használatával létrehozhat egy nyilvános SSH-kulcs](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Válassza ki **a nyilvános kulcs mentése** , és mentse egy olyan helyre, ahol hozzá tud férni.

1. Válassza ki **titkos kulcs mentése** , és mentse egy olyan helyre, ahol elérheti, és ne feledje, hogy nyilvános kulcshoz tartozik.

A nyilvános kulcsot a mentett szövegfájlban tárolódnak. Ha megnyitja, tartalmazni fog szöveg, amely a következőhöz hasonló:

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

A nyilvános kulcs használatakor másolása és beillesztése a teljes környezetében, a szövegmező értékét, amikor egy alkalmazás megkérdezi a kulcs.

## <a name="connect-with-ssh-using-putty"></a>Csatlakozzon az ssh-val a PuTTY használatával

Ha már telepítette a putty-t, hogy a megosztottelérésikulcs-készítő és a egy SSH-ügyfél. Nyissa meg a PuTTY SSH-ügyfél, a csatlakozási értékeket és SSH-kulcs konfigurálásával, és ha ugyanazon a hálózaton, az Azure Stack, csatlakozzon a virtuális Géphez.

Csatlakozás előtt lesz szüksége:
- Putty-kapcsolaton keresztül
- Az IP-cím és a Linux-gép az Azure stack, amely egy nyilvános SSH-kulcsot használ a hitelesítési típus tartozó felhasználónév.
- 22-es portot kell megnyitni a gép.
- A nyilvános SSH-kulcs, amely a gép létrehozásakor használt.
- A putty-t az Azure Stack ugyanazon a hálózaton lehet futtató ügyfélszámítógép.

### <a name="connect-via-ssh-with-putty"></a>Csatlakozzon a PuTTy SSH-n keresztül

1. Nyissa meg a putty-kapcsolaton keresztül.

    ![Csatlakozzon a PuTTY használatával](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Adja hozzá a felhasználónevet és a gép nyilvános IP-címét. Ha például `username@192.XXX.XXX.XX` számára a **állomásnév**. 
3. Ellenőrizze, hogy **Port** `22` van beállítva, és **kapcsolattípus** értékre van állítva `SSH`.
4. Bontsa ki a **SSH** > **Auth** a a **kategória** fában.

    ![Titkos SSH-kulcs](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Válassza ki **Tallózás** , és keresse meg a titkos kulcs fájlját (filename.ppk) a nyilvános és titkos kulcsból álló párként.
6. Válassza ki a munkamenet a kategórialista.

    ![SSH nyilvános kulcs és a titkos kulcs](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Adjon meg egy nevet a munkamenet alatt **mentett munkamenetek** válassza **mentése**.
8. Válassza ki a nevét, a munkamenet és **terhelés**.
9. Válassza az **Open** (Megnyitás) elemet. Az SSH-munkamenet nyílik meg.

## <a name="connect-with-sftp-with-filezilla"></a>SFTP Filezillát való összekapcsolása

Az FTP-ügyfél, amely támogatja az SFTP-helyezze át a fájlokat, és a Linux-gép Filezillát használhatja. A Windows 10, Linux és macOS Filezillát fut. Filezillát ügyfél támogatja az FTP, hanem FTP TLS (FTPS), valamint az SFTP. Nyílt forráskódú szoftverek elosztott a GNU általános nyilvános licenc feltételei szerint ingyenesen elérhető.

### <a name="set-your-connection"></a>A kapcsolat beállítása

1. [Töltse le és telepítse a Filezillát](https://filezilla-project.org/download.php).
1. Nyissa meg a Filezillát.
1. Válassza ki **fájl** > **Webhelykezelője**.

    ![SSH nyilvános kulcs és a titkos kulcs](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Válassza ki **SFTP - SSH File Transfer Protocol** a **protokoll**.
1. A gép a nyilvános IP-cím hozzáadása a **gazdagép** mezőbe.
1. Válassza ki **normál** a **bejelentkezés típusa**.
1. Adja hozzá a felhasználónevet és jelszót.
1. Kattintson az **OK** gombra.
1. Válassza ki **szerkesztése** > **beállítások**.

    ![SSH nyilvános kulcs és a titkos kulcs](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Bontsa ki a **kapcsolat** a a **kiválasztása oldalon** fában. Válassza ki **SFTP**.
1. Válassza ki **Hozzáadás kulcsfájl** , és adja hozzá a titkos kulcs fájlját, például a filename.ppk.
1. Kattintson az **OK** gombra.

### <a name="open-your-connection"></a>A kapcsolat megnyitása

1. Nyissa meg a Filezillát.
1. Válassza ki **fájl** > **Webhelykezelője**.
1. Válassza ki a webhely nevét, és válassza ki **Connect**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)