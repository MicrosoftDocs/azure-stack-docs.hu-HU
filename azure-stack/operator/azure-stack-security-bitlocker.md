---
title: Inaktív adatok titkosítása
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan védi a Azure Stack hub az adatok védelmét a REST-titkosítással.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 08/06/2020
ms.openlocfilehash: 8d188c77ab04044db94e13cdbf36fdb0e7cbe839
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638818"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Inaktív adatok titkosítása Azure Stack hub-ban

Azure Stack hub a tárolás alrendszer szintjén védi a felhasználói és az infrastrukturális adatok védelmét a REST titkosítás használatával. Alapértelmezés szerint a Azure Stack hub tárolási alrendszer titkosítása a BitLocker és a 128 bites AES titkosítás használatával történik. A BitLocker-kulcsok belső titkos tárolóban maradnak. A telepítés ideje alatt a BitLocker a 256 bites AES-titkosítás használatára is konfigurálható.

Az inaktív adatok titkosítása a legfontosabb megfelelőségi szabványok (például a PCI-DSS, a FedRAMP, a HIPAA) gyakori követelménye. Azure Stack hub lehetővé teszi a követelmények teljesítését, és nem igényel további munkát vagy konfigurációkat. További információ arról, hogyan segíti a Azure Stack hub a megfelelőségi előírások teljesítését: a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Az inaktív adatok titkosítása megvédi az adatait, és olyan személy fér hozzá, aki fizikailag ellopta egy vagy több merevlemezt. A REST-titkosításban tárolt adatok nem védik a hálózaton keresztül elfogott adatok (az átvitel során továbbított adatok), a jelenleg használatban lévő adatok (a memóriában tárolt adatok), vagy általánosságban az adatok exfiltrated, miközben a rendszer működik.

## <a name="retrieving-bitlocker-recovery-keys"></a>BitLocker helyreállítási kulcsok beolvasása

Azure Stack hub-beli inaktív adatokhoz tartozó BitLocker-kulcsok belsőleg kezelhetők. Nem szükséges a normál működéshez vagy a rendszerindításhoz biztosítani őket. A támogatási forgatókönyvek esetében azonban szükség lehet a BitLocker helyreállítási kulcsokra, hogy a rendszer online állapotba kerüljön.  

> [!WARNING]
> Kérje le a BitLocker helyreállítási kulcsait, és tárolja azokat biztonságos helyen Azure Stack hub-on kívül. Bizonyos támogatási helyzetekben a helyreállítási kulcsok nem rendelkeznek adatvesztéssel, és a rendszer-visszaállítást igényelnek egy biztonsági mentési rendszerképből.

A BitLocker helyreállítási kulcsainak beolvasásához hozzá kell férnie a [privilegizált végponthoz](azure-stack-privileged-endpoint.md) (PEP). A PEP-munkamenetből futtassa a Get-AzsRecoveryKeys parancsmagot.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys -raw
```

Paraméterek a *Get-AzsRecoveryKeys* parancsmaghoz:

| Paraméter | Leírás | Típus | Kötelező |
|---------|---------|---------|---------|
|*nyers* | Az adatleképezést adja vissza a helyreállítási kulcs, a számítógép neve és az egyes titkosított kötetek jelszavas azonosítója között.  | Kapcsoló (switch) | Nem, de ajánlott |

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Szélsőséges körülmények között a BitLocker feloldására irányuló kérelem meghiúsulhat, ami miatt egy adott kötet nem indítható el. Az architektúra egyes összetevőinek rendelkezésre állása alapján ez a hiba leállást eredményezhet, és esetleges adatvesztést okozhat, ha nem rendelkezik a BitLocker helyreállítási kulcsaival.

> [!WARNING]
> Kérje le a BitLocker helyreállítási kulcsait, és tárolja azokat biztonságos helyen Azure Stack hub-on kívül. Bizonyos támogatási helyzetekben a helyreállítási kulcsok nem rendelkeznek adatvesztéssel, és a rendszer-visszaállítást igényelnek egy biztonsági mentési rendszerképből.

Ha azt gyanítja, hogy a rendszer problémát észlelt a BitLockerben, például Azure Stack hub nem indul el, forduljon az ügyfélszolgálathoz. A támogatáshoz a BitLocker helyreállítási kulcsai szükségesek. A BitLockerrel kapcsolatos problémák többsége az adott virtuális gép/gazdagép/kötet esetében egy cserélhető művelettel oldható fel. Más esetekben a BitLocker helyreállítási kulcsait használó manuális felszabadítási eljárás végezhető el. Ha a BitLocker helyreállítási kulcsai nem érhetők el, az egyetlen lehetőség a biztonsági mentési rendszerképből való visszaállítás. Attól függően, hogy mikor történt az utolsó biztonsági mentés, adatvesztést tapasztalhat.

## <a name="next-steps"></a>Következő lépések

- [További információ a Azure stack hub biztonságáról](azure-stack-security-foundations.md).
- További információ arról, hogyan védi a BitLocker a CSV: a [fürt megosztott kötetei és a tárolóhelyek hálózatának védelme a BitLockerrel](/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).
