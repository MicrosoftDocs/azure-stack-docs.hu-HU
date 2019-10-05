---
title: A Azure Stack tanúsítványával kapcsolatos problémák elhárítása | Microsoft Docs
description: A tanúsítványokkal kapcsolatos problémák áttekintéséhez és szervizeléséhez használja a Azure Stack Readiness-ellenőrzőt.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 6e8adbc0d84c7816a081e751473764aab79cfcf2
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961914"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>A PKI-tanúsítványok Azure Stack gyakori problémáinak elhárítása

A cikkben található információk segítenek megérteni és megoldani a Azure Stack PKI-tanúsítványokkal kapcsolatos gyakori problémákat. Ha a Azure Stack Readiness-ellenőrző eszközt használja a [PKI-tanúsítványok Azure stack ellenőrzéséhez](azure-stack-validate-pki-certs.md), problémákat tapasztalhat. Az eszköz ellenőrzi, hogy a tanúsítványok megfelelnek-e egy Azure Stack üzemelő példány PKI-követelményeinek, és Azure Stack a titkos kulcs rotációját, és naplózza az eredményeket egy [report. JSON fájlba](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>PFX-titkosítás

**Hiba** – a pfx-titkosítás nem TRIPLEDES-SHA1.

**Szervizelés** – pfx-fájlok exportálása **TripleDES-SHA1** titkosítással. Ez az alapértelmezett érték minden Windows 10-es ügyfélnél, ha a tanúsítvány beépülő modulból vagy a `Export-PFXCertificate` használatával végez exportálást.

## <a name="read-pfx"></a>PFX olvasása

**Figyelmeztetés** – a jelszó csak a tanúsítványban található személyes adatokat védi.  

**Szervizelés** – pfx-fájlok exportálása a **tanúsítvány-adatvédelem engedélyezésének**opcionális beállításával.  

**Hiba** – a pfx-fájl érvénytelen.  

**Szervizelés** – exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md)című témakör lépéseit követve.

## <a name="signature-algorithm"></a>Aláírási algoritmus

**Hiba** – az aláírási algoritmus SHA1.

**Szervizelés** – a tanúsítvány-aláírási kérelem létrehozásával Azure stack tanúsítványok aláírása a sha256 aláírási algoritmusával újragenerálja a tanúsítvány-aláírási kérést (CSR). Ezután küldje el újra a CSR-t a hitelesítésszolgáltatónak a tanúsítvány újbóli kikibocsátásához.

## <a name="private-key"></a>Titkos kulcs

**Hiba** – a titkos kulcs hiányzik, vagy nem tartalmazza a helyi gép attribútumát.  

**Szervizelés** – a CSR-t létrehozó számítógépről exportálja újra a tanúsítványt a következő témakörben ismertetett módon: [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Ezek a lépések a helyi számítógép tanúsítványtárolóból történő exportálást is tartalmazzák.

## <a name="certificate-chain"></a>Tanúsítványlánc

**Hiba** – a tanúsítványlánc nem fejeződött be.  

**Szervizelés** – a tanúsítványoknak teljes tanúsítványláncot kell tartalmazniuk. Exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit követve, és válassza az **összes tanúsítvány belefoglalása a minősítési útvonalon lehetőséget, ha lehetséges**.

## <a name="dns-names"></a>DNS-nevek

**Hiba** – a tanúsítvány **DNSNameList** nem tartalmazza a Azure stack szolgáltatási végpontjának nevét, vagy egy érvényes helyettesítő karaktert. A helyettesítő karakteres egyezések csak a DNS-név bal szélső névterére érvényesek. A `*.region.domain.com` például csak `portal.region.domain.com` esetén érvényes, nem `*.table.region.domain.com`.

**Szervizelés** – a Azure stack tanúsítvány-aláírási kérések létrehozásának lépéseivel újragenerálja a CSR-t a megfelelő DNS-nevekkel a Azure stack végpontok támogatásához. Küldje el újra a CSR-t egy hitelesítésszolgáltatótól, majd kövesse a [Azure stack PKI-tanúsítványok előkészítése a telepítéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit, és exportálja a tanúsítványt a CSR-t létrehozó gépről.  

## <a name="key-usage"></a>Kulcshasználat

**Hiba** – a kulcs használata hiányzik a digitális aláírás vagy a kulcs titkosítási, vagy a kibővített kulcshasználat hiányzik a kiszolgálói hitelesítés vagy az ügyfél-hitelesítés.  

**Szervizelés** – a [Azure stack tanúsítványok aláírási kérelmének létrehozása](azure-stack-get-pki-certs.md) című témakör lépéseit követve újra generálhatja a CSR-t a helyes kulcshasználat attribútumokkal. Küldje el újra a CSR-t a hitelesítésszolgáltatóhoz, és győződjön meg arról, hogy a tanúsítványsablon nem írja felül a kérésben szereplő kulcshasználat adatait.

## <a name="key-size"></a>Kulcs mérete

**Hiba** – a kulcs mérete kisebb, mint 2048.

**Szervizelés** – a [Azure stack tanúsítvány-aláírási kérelem létrehozásának](azure-stack-get-pki-certs.md) lépéseivel újragenerálja a CSR-t a megfelelő kulcs hosszával (2048), majd küldje el újra a CSR-t a hitelesítésszolgáltatótól.

## <a name="chain-order"></a>Lánc sorrendje

**Hiba** – a tanúsítványlánc sorrendje helytelen.  

**Szervizelés** – exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit követve, és válassza az **összes tanúsítvány belefoglalása a minősítési útvonalon lehetőséget, ha lehetséges**. Győződjön meg arról, hogy csak a levél tanúsítvány van kiválasztva exportáláshoz.

## <a name="other-certificates"></a>Egyéb tanúsítványok

**Hiba** – a pfx-csomag olyan tanúsítványokat tartalmaz, amelyek nem a levél tanúsítványa vagy a tanúsítványlánc része.  

**Szervizelés** – exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése a központi telepítésre](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)című témakör lépéseit követve, és jelölje be az **összes tanúsítvány belefoglalása a tanúsítási útvonalba lehetőséget, ha lehetséges**. Győződjön meg arról, hogy csak a levél tanúsítvány van kiválasztva exportáláshoz.

## <a name="fix-common-packaging-issues"></a>Gyakori csomagolási problémák elhárítása

A **AzsReadinessChecker** eszköz tartalmaz egy **Repair-AzsPfxCertificate**nevű segítő parancsmagot, amely importálhatja, majd exportálhatja a pfx-fájlt a gyakori csomagolási problémák megoldására, beleértve a következőket:

- A **pfx-titkosítás** nem TRIPLEDES-SHA1.
- A **titkos kulcsból** hiányzik a helyi gép attribútuma.
- A **tanúsítványlánc** nem fejeződött be vagy helytelen. Ha a PFX-csomag nem, a helyi gépnek tartalmaznia kell a tanúsítványláncot.
- **Egyéb tanúsítványok**

A **Repair-AzsPfxCertificate** nem tud segíteni, ha új CSR-t kell létrehoznia, és újra ki kell állítania egy tanúsítványt.

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeket kell megadnia azon a számítógépen, amelyen az eszköz fut:

- Windows 10 vagy Windows Server 2016, internetkapcsolattal.
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, majd tekintse át a *főbb** és a **másodlagos** verziókat:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Konfigurálja [a powershellt Azure Stackhoz](azure-stack-powershell-install.md).
- Töltse le az [Azure stack Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verzióját.

### <a name="import-and-export-an-existing-pfx-file"></a>Meglévő PFX-fájl importálása és exportálása

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort az előfeltételeknek megfelelő számítógépen, majd futtassa a következő parancsot a Azure Stack Readiness-ellenőrző telepítéséhez:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell-parancssorból futtassa a következő parancsmagot a PFX-jelszó megadásához. Cserélje le a `PFXpassword` értéket a tényleges jelszóval:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. A PowerShell-parancssorból futtassa a következő parancsot egy új PFX-fájl exportálásához:

   - @No__t – 0 esetében határozza meg a PFX-fájl elérési útját. A következő példában az elérési út `.\certificates\ssl.pfx`.
   - @No__t – 0 esetében adja meg az exportálandó PFX-fájl helyét és nevét. A következő példában az elérési út `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Az eszköz befejeződése után tekintse át a sikeres kimenetet:

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>További lépések

- [További tudnivalók az Azure Stack biztonságáról](azure-stack-rotate-secrets.md)
