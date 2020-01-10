---
title: Azure Stack hub biztonsági vezérlőinek konfigurálása
description: Megtudhatja, hogyan konfigurálhat biztonsági vezérlőket Azure Stack hub-ban
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 1423b21de6236bf316e426aa175ddb2b95c70199
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814932"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Azure Stack hub biztonsági vezérlőinek konfigurálása

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

Ez a cikk ismerteti az Azure Stack hub-ban módosítható biztonsági ellenőrzéseket, és kiemeli a kompromisszumokat, ahol lehetséges.

Azure Stack hub-architektúra két biztonsági elv pillérére épül: a rendszer alapértelmezés szerint megsérti és megerősíti a problémákat. További információ Azure Stack hub biztonságáról: [Azure stack hub-infrastruktúra biztonsági helyzete](azure-stack-security-foundations.md). Míg Azure Stack hub alapértelmezett biztonsági állapota éles használatra kész, néhány üzembe helyezési forgatókönyv további megerősítést igényel.

## <a name="tls-version-policy"></a>TLS-verzió házirendje

A Transport Layer Security (TLS) protokoll egy széles körben elfogadott titkosítási protokoll a hálózaton keresztüli titkosított kommunikáció létrehozásához. A TLS idővel bővült, és több verzió is megjelent. Az Azure Stack hub-infrastruktúra kizárólag TLS 1,2-et használ az összes kommunikációhoz. Külső felületek esetén a Azure Stack hub jelenleg alapértelmezés szerint a TLS 1,2-et használja. A visszamenőleges kompatibilitás érdekében azonban támogatja a TLS 1,1-re való egyeztetést is. és 1,0. Ha egy TLS-ügyfél a TLS 1,1 vagy a TLS 1,0 protokollal kommunikálva küldi a kommunikációt, Azure Stack hub egy alacsonyabb TLS-verzióra való egyeztetéssel fogadja el a kérést. Ha az ügyfél TLS 1,2-t kér, Azure Stack hub TLS-kapcsolatot létesít a TLS 1,2 használatával.

Mivel a TLS 1,0 és a 1,1 Növekményesen elavult vagy tiltott a szervezetek és a megfelelőségi szabványok alapján, a 1906-es frissítéstől kezdve a TLS-házirendet Azure Stack hub-ban is konfigurálhatja. Csak a TLS 1,2 házirendet kényszerítheti ki, ahol a TLS-munkamenetek 1,2-nél alacsonyabb verzióval való létesítésére tett kísérlet nem engedélyezett és nem utasítható el.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a TLS 1,2 csak Azure Stack hub éles környezetekre vonatkozó házirendet használjon.

## <a name="get-tls-policy"></a>TLS-szabályzat beolvasása

Az összes Azure Stack hub-végponthoz tartozó TLS-házirend megtekintéséhez használja a [Kiemelt jogosultságú végpontot (PEP)](azure-stack-privileged-endpoint.md) :

```powershell
Get-TLSPolicy
```

Példa a kimenetre:

    TLS_1.2

## <a name="set-tls-policy"></a>TLS-házirend beállítása

Az összes Azure Stack hub-végponthoz tartozó TLS-házirend beállításához használja a [Kiemelt jogosultságú végpontot (PEP)](azure-stack-privileged-endpoint.md) :

```powershell
Set-TLSPolicy -Version <String>
```

A *set-TLSPolicy* parancsmag paraméterei:

| Paraméter | Leírás | Type (Típus) | Szükséges |
|---------|---------|---------|---------|
| *Verzió* | A TLS engedélyezett verziója (i) Azure Stack hub-ban | Sztring | igen|

A következő értékek egyikének használatával konfigurálhatja az összes Azure Stack hub-végponthoz engedélyezett TLS-verziót:

| Verzió értéke | Leírás |
|---------|---------|
| *TLS_All* | Azure Stack hub TLS-végpontok támogatják a TLS 1,2-et, de a TLS 1,1 és a TLS 1,0 egyeztetése engedélyezett. |
| *TLS_1.2* | Azure Stack hub TLS-végpontok csak a TLS 1,2-et támogatják. | 

A TLS-házirend frissítése néhány percet is igénybe vehet.

### <a name="enforce-tls-12-configuration-example"></a>A TLS 1,2 konfiguráció kikényszerített példája

Ebben a példában a TLS-házirendet úgy állítja be, hogy csak a TLS 1,2-et alkalmazza.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Példa a kimenetre:

    VERBOSE: Successfully setting enforce TLS 1.2 to True
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is enforced

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>A TLS összes verziójának (1,2, 1,1 és 1,0) engedélyezése – példa

Ez a példa beállítja a TLS-házirendet, hogy engedélyezze a TLS összes verzióját (1,2, 1,1 és 1,0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Példa a kimenetre:

    VERBOSE: Successfully setting enforce TLS 1.2 to False
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is not enforced

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Azure Stack hub-infrastruktúra biztonsági helyzetéről](azure-stack-security-foundations.md)
- [Megtudhatja, hogyan forgathatja el a titkokat Azure Stack hub-ban](azure-stack-rotate-secrets.md)
- [A Windows Defender víruskereső frissítése Azure Stack hub-on](azure-stack-security-av.md)
