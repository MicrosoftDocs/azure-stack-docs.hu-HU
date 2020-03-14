---
title: Nyilvános SSH-kulcs használata Azure Stack hub használatával
description: Nyilvános SSH-kulcs használata
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 5f65b0ff096fbf84b140406b7cb98fdbdf5b0b11
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294153"
---
# <a name="use-an-ssh-public-key"></a>Nyilvános SSH-kulcs használata

Ha a fejlesztői gépről nyitott SSH-kapcsolattal szeretné használni a webalkalmazást futtató Azure Stack hub-példányban található kiszolgálói virtuális gépet, akkor lehet, hogy létre kell hoznia egy Secure Shell (SSH) nyilvános és titkos kulcspárt. 

Ebben a cikkben létrehozza a kulcsokat, majd a használatával csatlakozhat a kiszolgálóhoz. Egy SSH-ügyféllel beszerezhet egy bash-kérést a Linux-kiszolgálón, vagy használhat biztonságos FTP-(SFTP-) ügyfelet, hogy fájlokat helyezzen át a kiszolgálóra és a kiszolgálóról.

## <a name="create-an-ssh-public-key-on-windows"></a>Nyilvános SSH-kulcs létrehozása Windows rendszeren

Ebben a szakaszban a PuTTY Key Generator használatával hozzon létre egy nyilvános SSH-kulcsot és egy titkos kulcspárt, amelyet akkor kell használni, amikor biztonságos kapcsolódást hoz létre a Linux rendszerű gépekhez az Azure Stack hub-példányban. A PuTTY egy ingyenes terminál-emulátor, amely lehetővé teszi, hogy SSH-n és telneten keresztül csatlakozhasson a kiszolgálókhoz.

1. [Töltse le és telepítse a PuTTY-t a gépén.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Nyissa meg a PuTTY Key Generatort.

    ![A PuTTY Key Generator üres kulccsal](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. A **Paraméterek**területen válassza az **RSA**elemet.

1. A **generált kulcsban található bitek száma** mezőben adja meg a **2048**értéket.  

1. Válassza a **készítés**lehetőséget.

1. A **kulcs** területen állítson elő néhány véletlenszerű karaktert úgy, hogy az egérmutatót az üres területen helyezi át.

    ![A PuTTY Key Generator egy feltöltött kulccsal](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Adja meg a **kulcshoz tartozó jelszót** , és erősítse meg a **Jelszó megerősítése** mezőben. Jegyezze fel a jelszavát későbbi használatra.

1. Válassza a **nyilvános kulcs mentése**lehetőséget, és mentse azt egy olyan helyre, ahol hozzá tud férni.

1. Válassza a **titkos kulcs mentése**lehetőséget, és mentse azt egy olyan helyre, ahol hozzá tud férni. Ne feledje, hogy a nyilvános kulcshoz tartozik.

A nyilvános kulcsot a mentett szövegfájl tárolja. A szöveg a következőhöz hasonlóan néz ki:

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

Amikor egy alkalmazás kéri a kulcsot, másolja és illessze be a szövegfájl teljes tartalmát.

## <a name="connect-with-ssh-by-using-putty"></a>Az SSH-val való kapcsolat a PuTTY használatával

A PuTTY telepítésekor a PuTTY Key Generator és egy SSH-ügyfél is van. Ebben a szakaszban megnyithatja az SSH-ügyfelet, a PuTTY-t, és konfigurálhatja a kapcsolatok értékeit és az SSH-kulcsot. Ha a Azure Stack hub-példánnyal azonos hálózaton van, akkor a virtuális géphez csatlakozik.

A kapcsolat előtt a következőkre lesz szüksége:
- PuTTY
- Az Azure Stack hub-példányban található Linux-gép IP-címe és felhasználóneve, amely egy nyilvános SSH-kulcsot használ hitelesítési típusként.
- A 22-es port nyitva van a gépen.
- A számítógép létrehozásakor használt nyilvános SSH-kulcs.
- A PuTTY-t futtató ügyfélszámítógépnek ugyanazon a hálózaton kell lennie, mint az Azure Stack hub-példánnyal.

1. Nyissa meg a PuTTY eszközt.

    ![A PuTTY konfigurációs panelje](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Az **állomásnév (vagy IP-cím)** mezőbe írja be a gép felhasználónevét és nyilvános IP-címét (például **username@192.XXX.XXX.XX** ). 
3. Győződjön meg arról, hogy a **port** **22** , a **kapcsolattípus** pedig **SSH**.
4. A **Kategória** fában bontsa ki az **SSH** és az **Auth**elemet.

    ![A PuTTY konfigurációs paneljének-SSH titkos kulcsa](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. A **hitelesítő titkos kulcs fájlja** mellett válassza a **Tallózás**elemet, majd keresse meg a nyilvános és titkos kulcspár ( *\<filename >. PPK*) titkos kulcs fájlját.
6. A **Kategória** fában válassza a **munkamenet**elemet.

    ![A PuTTY konfigurációs ablaktábla "mentett munkamenetek" mezője](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. A **mentett munkamenetek**területen adja meg a munkamenet nevét, majd kattintson a **Mentés**gombra.
8. A **mentett munkamenetek** listában válassza ki a munkamenet nevét, majd válassza a **Betöltés**lehetőséget.
9. Válassza az **Open** (Megnyitás) elemet. Megnyílik az SSH-munkamenet.

## <a name="connect-with-sftp-with-filezilla"></a>Kapcsolat az SFTP-vel és a FileZilla-vel

A fájlok a Linux rendszerű számítógépekre való áthelyezéséhez a FileZilla, a biztonságos FTP-t (SFTP) támogató FTP-ügyfél is használható. A FileZilla Windows 10-es, Linux és macOS rendszeren fut. A FileZilla-ügyfél támogatja az FTP-t, az FTP-t a TLS (FTPS) és az SFTP protokollon keresztül. Nyílt forráskódú szoftver, amelyet a GNU általános nyilvános licenc feltételei szerint ingyenesen terjesztenek.

### <a name="set-your-connection"></a>A kapcsolatok beállítása

1. [Töltse le és telepítse a FileZilla-](https://filezilla-project.org/download.php)t.
1. Nyissa meg a FileZilla-t.
1. Válassza a **fájl** > **Site Manager**lehetőséget.

    ![A FileZilla Site Manager panel](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. A **protokoll** legördülő listában válassza az **SFTP-SSH File Transfer Protocol**elemet.
1. A **gazdagép** mezőben adja meg a számítógép nyilvános IP-címét.
1. A **Bejelentkezés típusa** mezőben válassza a **normál**elemet.
1. Adja meg a felhasználónevét és a jelszavát.
1. Kattintson az **OK** gombra.
1. Válassza a > **Beállítások** **szerkesztése** lehetőséget.

    ![A FileZilla beállítások panel](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. A **lap kijelölése** fán bontsa ki a **kapcsolatok**csomópontot, majd válassza az **SFTP**lehetőséget.
1. Válassza a **Kulcs hozzáadása**lehetőséget, majd adja meg a titkos kulcs fájlját (például *\<filename >. PPK*).
1. Kattintson az **OK** gombra.

### <a name="open-your-connection"></a>A kapcsolatok megnyitása

1. Nyissa meg a FileZilla-t.
1. Válassza a **fájl** > **Site Manager**lehetőséget.
1. Válassza ki a webhely nevét, majd válassza a **kapcsolat**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [állíthatja be a fejlesztési környezetet az Azure stack hub-ban](azure-stack-dev-start.md).
