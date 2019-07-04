---
title: Azure Stack biztonsági vezérlő konfigurálása
description: Biztonsági vezérlők konfigurálása az Azure Stackben
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: b36a6d826dc7249f10b4785b27511096e45923a9
ms.sourcegitcommit: 7348876a97e8bed504b5f5d90690ec8d1d9472b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557850"
---
# <a name="configure-azure-stack-security-controls"></a>Azure Stack biztonsági vezérlő konfigurálása

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk azt ismerteti, hogy a biztonsági ellenőrzéseket, amelyeket az Azure Stackben is módosítható, és kiemeli a kompromisszumot kínál a, ha vannak ilyenek.

Az Azure Stack-architektúra két biztonsági alapelv alappillérét épül: feltételezése és a megerősített alapértelmezés szerint. További információ az Azure Stack biztonsági: [Azure Stack infrastruktúrájának biztonsági állapotáról](azure-stack-security-foundations.md). Bár az alapértelmezett biztonsági állapotát az Azure Stack éles használatra kész, vannak bizonyos központi telepítési forgatókönyvek, amelyek további megerősítés igényelnek.

## <a name="tls-version-policy"></a>A TLS-verzió szabályzat

A Transport Layer Security (TLS) protokoll egy olyan széles körben elfogadott titkosítási protokoll segítségével kapcsolatot hozhat létre egy titkosított a hálózaton keresztül. TLS alakult idővel, és több verzió adtak ki. Az Azure Stack-infrastruktúra a TLS 1.2 kizárólag használja a kommunikációhoz. Külső kapcsolatok az Azure Stack jelenleg alapértelmezés szerint a TLS 1.2 használatára. Azonban a visszamenőleges kompatibilitás érdekében is támogatja a TLS 1.1 le egyeztetése. és 1.0-t. Amikor a TLS ügyfél való kommunikációhoz a TLS 1.1 és TLS 1.0, az Azure Stack által egyeztetett TLS alacsonyabb verzióra figyelembe veszi a kérelmet. Ha az ügyfél kéri a TLS 1.2, Azure Stack a TLS 1.2 használatával TLS kapcsolatot fog létesíteni.

Mivel a TLS 1.0 és 1.1 növekményes elavulttá válnak, vagy a szervezetek és megfelelőségi szabványoknak tiltja, a 1906 frissítéstől kezdődően most konfigurálhatja a TLS-szabályzat az Azure Stackben. A TLS 1.2 egyetlen házirend ahol bármilyen kísérlet egy TLS-munkamenet az alacsonyabb, mint az 1.2-es verzióval megadása nem engedélyezett, elutasított kényszeríthető.

> [!IMPORTANT]
> A Microsoft javasolja, hogy a TLS 1.2 csak csoportházirend használatával az Azure Stack éles környezetekhez.

## <a name="get-tls-policy"></a>A TLS-szabályzat lekérése

Használja a [kiemelt végponthoz (EGP)](azure-stack-privileged-endpoint.md) a TLS-szabályzat az összes Azure Stack-végpontok megtekintése:

```powershell
Get-TLSPolicy
```

Példa a kimenetre:

    TLS_1.2

## <a name="set-tls-policy"></a>A TLS-szabály beállítása

Használja a [kiemelt végponthoz (EGP)](azure-stack-privileged-endpoint.md) TLS házirend az összes Azure Stack-végpontok beállítása:

```powershell
Set-TLSPolicy -Version <String>
```

A paraméterek *Set-TLSPolicy* parancsmagot:

| Paraméter | Leírás | Típus | Kötelező |
|---------|---------|---------|---------|
| *Verzió* | A TLS verzióinak engedélyezett az Azure Stackben | String | igen|

Használja az engedélyezett TLS-verziók az összes Azure Stack-végpontok konfigurálása a következő értékek egyikét:

| Version értéke | Leírás |
|---------|---------|
| *TLS_All* | Azure Stack TLS-végpontok támogatja a TLS 1.2-es, de egyeztetést a TLS 1.1 le, és a TLS 1.0 engedélyezve van. |
| *TLS_1.2* | Azure Stack TLS-végpontok csak támogatja a TLS 1.2. | 

A TLS-házirend frissítése néhány percet vesz igénybe.

### <a name="enforce-tls-12-configuration-example"></a>A TLS 1.2 példakonfigurációt kényszerítése

Ebben a példában csak a TLS 1.2 kényszerítése a TLS szabályzat beállítása.

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

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Engedélyezze a TLS (1.2-es, 1.1-es és 1.0) példakonfigurációt összes verziója

Ebben a példában, hogy a TLS (1.2-es, 1.1-es és 1.0) összes verzióját a TLS szabályzat beállítása.

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

## <a name="next-steps"></a>További lépések

- [További tudnivalók az Azure Stack infrastruktúrájának biztonsági rendszer kialakításához](azure-stack-security-foundations.md)
- [Ismerje meg, az Azure Stack titkos kulcsainak rotálása](azure-stack-rotate-secrets.md)
- [Frissítés a Windows Defender víruskereső az Azure Stackben](azure-stack-security-av.md)
