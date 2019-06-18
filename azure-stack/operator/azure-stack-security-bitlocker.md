---
title: Az adatok inaktív adatok titkosítása az Azure Stackben
description: Ismerje meg, hogyan lehet az Azure Stack védi az adatokat, a titkosítás inaktív állapotban
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 0e21808ad82a61014b69cb26958a0c13518af13a
ms.sourcegitcommit: 7fa9b64aeae2b22be7acfb5c4987d233303107c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166503"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Az adatok inaktív adatok titkosítása az Azure Stackben

Az Azure Stack védi a felhasználó- és infrastruktúra-adatok titkosítása inaktív állapotban tárolási alrendszer szinten. Az Azure Stack tárolóalrendszer 128 bites AES-titkosítással bitlockerrel van titkosítva. BitLocker-kulcsok megmaradnak, az egy belső titkoskód-tárolót.

Inaktív adatok titkosítása adata általános követelmény a számos, a fő megfelelőségi szabványoknak (például a PCI-DSS, a FedRAMP, HIPAA). Az Azure Stack segítségével további munkát és a szükséges konfigurációk nélkül erőforrásigények kielégítéséhez. További információ az Azure Stack segítségével miként megfelelőségi szabványoknak megfelelő, tekintse meg a [Microsoft Szolgáltatásmegbízhatósági portált](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Adatok inaktív adatok titkosítása a személy, aki legalább egy merevlemez-meghajtókat fizikailag ellopná által elért adatok védelme. Adatok inaktív adatok titkosítása nem nyújt védelmet a folyamatban van a hálózaton keresztül (az átvitt adatok), jelenleg használt (a memóriában) adatok vagy egyéb általában elfogott adatok exfiltrated folyamatban, amíg a rendszer működik és fut adatokat.

## <a name="retrieving-bitlocker-recovery-keys"></a>A BitLocker helyreállítási kulcsok beolvasása

Inaktív adatok az Azure Stack BitLocker-kulcsok belsőleg kezelik. Nem kell megadnia őket a normál működést, vagy a rendszer indításakor. Támogatási esetek azonban a BitLocker helyreállítási kulcsok ahhoz, hogy a rendszer online lehet szükség.  

> [!WARNING]
> A BitLocker helyreállítási kulcsok lekéréséhez, és az Azure Stack-en kívül egy biztonságos helyen tárolja őket. A helyreállítási kulcsok nem rendelkezik az egyes támogatási példák során adatvesztés, és szükséges biztonsági mentési rendszerképből egy rendszer-visszaállítás.

A BitLocker helyreállítási kulcsok beolvasása hozzáférésre van szüksége a [kiemelt végponthoz](azure-stack-privileged-endpoint.md) (EGP). Egy EGP-munkamenetben futtassa a Get-AzsRecoveryKeys parancsmagot.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

A választható paraméterek *Get-AzsRecoveryKeys* parancsmagot:

| Paraméter | Leírás | Típus | Kötelező |
|---------|---------|---------|---------|
|*nyers* | leképezés nyers adatokat ad vissza a helyreállítási kulcsot, a számítógép nevét és a jelszó azonosító(k) az egyes titkosított kötetek között  | Kapcsoló | Nincs (Designed támogatási forgatókönyvek esetén)|

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Rendkívüli körülmények között, a BitLocker feloldás kérelem is sikertelen, egy adott kötet nem rendszerindító eredményez. Az architektúra összetevői némelyikének függően ez vezethet állásidőt és az esetleges adatvesztés Ha nem rendelkezik a BitLocker helyreállítási kulcsok.

> [!WARNING]
> A BitLocker helyreállítási kulcsok lekéréséhez, és az Azure Stack-en kívül egy biztonságos helyen tárolja őket. A helyreállítási kulcsok nem rendelkezik az egyes támogatási példák során adatvesztés, és szükséges biztonsági mentési rendszerképből egy rendszer-visszaállítás.

Ha azt gyanítja, hogy a rendszer hibásan bitlockerrel, például az Azure Stack sikertelen elindításához, forduljon az ügyfélszolgálathoz. Támogatás a BitLocker helyreállítási kulcsra van szükség. A BitLocker a legtöbb kapcsolatos problémákat orvosolni tudja egy adott virtuális gép/host/kötet FRU műveletet. Egyéb esetben a videókban rejlő információk manuális eljárás a BitLocker helyreállítási kulcs használatával végezheti el. A BitLocker helyreállítási kulcsok nem érhetők el, ha az egyetlen lehetőség a visszaállítandó biztonsági rendszerképre. Attól függően, amikor az utolsó biztonsági másolat fordulhatnak elő adatvesztés.

## <a name="next-steps"></a>További lépések

- [További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md)
- Hogyan védje a BitLocker a CSV-k további információkért lásd: [védelméhez a fürt megosztott fürtkötetek és a tárolóhálózat bitlockerrel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).