---
title: Inaktív adatok titkosítása
titleSuffix: Azure Stack
description: Megtudhatja, hogyan védi a Azure Stack az adatok védelmét a REST-titkosítással.
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 13455668330571e9190d37ea0abb4de2a7b88a5d
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780711"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Inaktív adatok titkosítása Azure Stack

Azure Stack a tárolási alrendszer szintjén védi a felhasználói és az infrastrukturális adatok védelmét a inaktív titkosítás használatával. Azure Stack Storage alrendszer titkosítása a BitLocker és a 128 bites AES titkosítás használatával történik. A BitLocker-kulcsok belső titkos tárolóban maradnak.

Az inaktív adatok titkosítása a legfontosabb megfelelőségi szabványok (például a PCI-DSS, a FedRAMP, a HIPAA) gyakori követelménye. Azure Stack lehetővé teszi a követelmények teljesítését, és nem igényel további munkát vagy konfigurációkat. További információ arról, hogyan segíti a Azure Stack a megfelelőségi előírások teljesítését: a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Az inaktív adatok titkosítása megvédi az adatait, és olyan személy fér hozzá, aki fizikailag ellopta egy vagy több merevlemezt. A REST-titkosításban tárolt adatok nem védik a hálózaton keresztül elfogott adatok (az átvitel során továbbított adatok), a jelenleg használatban lévő adatok (a memóriában tárolt adatok), vagy általánosságban az adatok exfiltrated, miközben a rendszer működik.

## <a name="retrieving-bitlocker-recovery-keys"></a>BitLocker helyreállítási kulcsok beolvasása

Azure Stack BitLocker-kulcsok az inaktív adatok esetében belsőleg kezelhetők. Nem szükséges a normál működéshez vagy a rendszerindításhoz biztosítani őket. A támogatási forgatókönyvek esetében azonban szükség lehet a BitLocker helyreállítási kulcsokra, hogy a rendszer online állapotba kerüljön.  

> [!WARNING]
> Kérje le a BitLocker helyreállítási kulcsait, és tárolja azokat biztonságos helyen Azure Stackon kívül. Bizonyos támogatási helyzetekben a helyreállítási kulcsok nem rendelkeznek adatvesztéssel, és a rendszer-visszaállítást igényelnek egy biztonsági mentési rendszerképből.

A BitLocker helyreállítási kulcsainak beolvasásához hozzá kell férnie a [privilegizált végponthoz](azure-stack-privileged-endpoint.md) (PEP). A PEP-munkamenetből futtassa a Get-AzsRecoveryKeys parancsmagot.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Választható paraméterek a *Get-AzsRecoveryKeys* parancsmaghoz:

| Paraméter | Leírás | Type (Típus) | Szükséges |
|---------|---------|---------|---------|
|*nyers* | Az egyes titkosított kötetek helyreállítási kulcsa, számítógépneve és jelszavas azonosítója (i) közötti leképezés nyers adatokból való beolvasása.  | Kapcsoló | Nem (támogatási forgatókönyvekhez tervezve)|

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Szélsőséges körülmények között a BitLocker feloldására irányuló kérelem meghiúsulhat, ami miatt egy adott kötet nem indítható el. Az architektúra egyes összetevőinek rendelkezésre állása alapján ez a hiba leállást eredményezhet, és esetleges adatvesztést okozhat, ha nem rendelkezik a BitLocker helyreállítási kulcsaival.

> [!WARNING]
> Kérje le a BitLocker helyreállítási kulcsait, és tárolja azokat biztonságos helyen Azure Stackon kívül. Bizonyos támogatási helyzetekben a helyreállítási kulcsok nem rendelkeznek adatvesztéssel, és a rendszer-visszaállítást igényelnek egy biztonsági mentési rendszerképből.

Ha azt gyanítja, hogy a rendszer problémát észlelt a BitLockerben, például Azure Stack sikertelenül, forduljon az ügyfélszolgálathoz. A támogatáshoz a BitLocker helyreállítási kulcsai szükségesek. A BitLockerrel kapcsolatos problémák többsége az adott virtuális gép/gazdagép/kötet esetében egy cserélhető művelettel oldható fel. Más esetekben a BitLocker helyreállítási kulcsait használó manuális felszabadítási eljárás végezhető el. Ha a BitLocker helyreállítási kulcsai nem érhetők el, az egyetlen lehetőség a biztonsági mentési rendszerképből való visszaállítás. Attól függően, hogy mikor történt az utolsó biztonsági mentés, adatvesztést tapasztalhat.

## <a name="next-steps"></a>Következő lépések

- [További információ a Azure stack biztonságról](azure-stack-security-foundations.md).
- További információ arról, hogyan védi a BitLocker a CSV: a [fürt megosztott kötetei és a tárolóhelyek hálózatának védelme a BitLockerrel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).