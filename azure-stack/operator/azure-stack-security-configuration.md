---
title: Biztonsági vezérlők konfigurálása Azure Stack hub-ban
description: Megtudhatja, hogyan konfigurálhat biztonsági vezérlőket Azure Stack hub-ban.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 0426dc0ba78b992ccc1605cca6c0f581cb9a24b9
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375037"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Azure Stack hub biztonsági vezérlőinek konfigurálása

Ez a cikk ismerteti az Azure Stack hub-ban módosítható biztonsági ellenőrzéseket, és kiemeli a kompromisszumokat, ahol lehetséges.

Azure Stack hub-architektúra két biztonsági elv pillérére épül: a rendszer alapértelmezés szerint megsérti és megerősíti a problémákat. A Azure Stack hub biztonságával kapcsolatos további információkért lásd: [Azure stack hub-infrastruktúra biztonsági helyzete](azure-stack-security-foundations.md). Míg Azure Stack hub alapértelmezett biztonsági állapota éles használatra kész, néhány üzembe helyezési forgatókönyv további megerősítést igényel.

## <a name="tls-version-policy"></a>TLS-verzió házirendje

A Transport Layer Security (TLS) protokoll egy széles körben elfogadott titkosítási protokoll, amellyel titkosított kommunikáció hozható létre a hálózaton keresztül. A TLS idővel bővült, és több verzió is megjelent. Az Azure Stack hub-infrastruktúra kizárólag TLS 1,2-et használ az összes kommunikációhoz. Külső felületek esetén a Azure Stack hub jelenleg alapértelmezés szerint a TLS 1,2-et használja. A visszamenőleges kompatibilitás érdekében azonban támogatja a TLS 1,1-re való egyeztetést is. és 1,0. Ha egy TLS-ügyfél a TLS 1,1 vagy a TLS 1,0 protokollal kommunikálva küldi a kommunikációt, Azure Stack hub egy alacsonyabb TLS-verzióra való egyeztetéssel fogadja el a kérést. Ha az ügyfél TLS 1,2-t kér, Azure Stack hub TLS-kapcsolatot létesít a TLS 1,2 használatával.

Mivel a TLS 1,0 és a 1,1 Növekményesen elavult vagy tiltott a szervezetek és a megfelelőségi szabványok alapján, a 1906-es frissítéstől kezdve a TLS-házirendet Azure Stack hub-ban is konfigurálhatja. Csak akkor kényszerítheti a TLS 1,2 házirendet, ha az 1,2-nél alacsonyabb verziójú TLS-munkamenet létesítésére tett kísérlet nem engedélyezett, és el lett utasítva.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a TLS 1,2 csak Azure Stack hub éles környezetekre vonatkozó házirendet használjon.

## <a name="get-tls-policy"></a>TLS-szabályzat beolvasása

Az összes Azure Stack hub-végponthoz tartozó TLS-házirend megtekintéséhez használja a [Kiemelt jogosultságú végpontot (PEP)](azure-stack-privileged-endpoint.md) :

```powershell
Get-TLSPolicy
```

Példa a kimenetre:

```powershell
TLS_1.2
```

## <a name="set-tls-policy"></a>TLS-házirend beállítása

Az összes Azure Stack hub-végponthoz tartozó TLS-házirend beállításához használja a [Kiemelt jogosultságú végpontot (PEP)](azure-stack-privileged-endpoint.md) :

```powershell
Set-TLSPolicy -Version <String>
```

A *set-TLSPolicy* parancsmag paraméterei:

| Paraméter | Leírás | Típus | Kötelező |
|-----|-----|-----|-----|
| *Verzió* | A TLS engedélyezett verziója (i) Azure Stack hub-ban | Sztring | igen |

A következő értékek egyikének használatával konfigurálhatja az összes Azure Stack hub-végponthoz engedélyezett TLS-verziót:

| Verzió értéke | Leírás |
|-------|-------|
| *TLS_All* | Azure Stack hub TLS-végpontok támogatják a TLS 1,2-et, de a TLS 1,1 és a TLS 1,0 egyeztetése engedélyezett. |
| *TLS_1.2* | Azure Stack hub TLS-végpontok csak a TLS 1,2-et támogatják. |

A TLS-házirend frissítése néhány percet is igénybe vehet.

### <a name="enforce-tls-12-configuration-example"></a>A TLS 1,2 konfiguráció kikényszerített példája

Ebben a példában a TLS-házirendet úgy állítja be, hogy csak a TLS 1,2-et alkalmazza.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Példa a kimenetre:

```powershell
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
```

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>A TLS összes verziójának (1,2, 1,1 és 1,0) engedélyezése – példa

Ez a példa beállítja a TLS-házirendet, hogy engedélyezze a TLS összes verzióját (1,2, 1,1 és 1,0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Példa a kimenetre:

```powershell
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
```

## <a name="next-steps"></a>Következő lépések

- [Ismerje meg Azure stack hub-infrastruktúra biztonsági](azure-stack-security-foundations.md)helyzetét.
- [Megtudhatja, hogyan forgathatja el a titkokat Azure stack hub-ban](azure-stack-rotate-secrets.md).
- [Frissítse a Windows Defender víruskeresőt Azure stack hub-on](azure-stack-security-av.md).
