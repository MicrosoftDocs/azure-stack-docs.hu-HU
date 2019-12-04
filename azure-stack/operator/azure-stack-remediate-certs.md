---
title: A PKI-tanúsítványokkal kapcsolatos gyakori problémák elhárítása
titleSuffix: Azure Stack
description: Azure Stack PKI-tanúsítványokkal kapcsolatos gyakori problémák elhárítása az Azure Stack Readiness-ellenőrző használatával.
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
ms.openlocfilehash: 449a734f2b98328d0a5ed046f0c387e6cfdf8a97
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780864"
---
# <a name="fix-common-issues-with-azure-stack-pki-certificates"></a>Azure Stack PKI-tanúsítványok gyakori problémáinak elhárítása

A cikkben található információk segítségével megismerheti és elháríthatja Azure Stack PKI-tanúsítványokkal kapcsolatos gyakori problémákat. Ha a Azure Stack Readiness-ellenőrző eszközt használja a [PKI-tanúsítványok Azure stack ellenőrzéséhez](azure-stack-validate-pki-certs.md), problémákat tapasztalhat. Az eszköz ellenőrzi, hogy a tanúsítványok megfelelnek-e egy Azure Stack üzemelő példány PKI-követelményeinek, és Azure Stack a titkos kulcs elforgatását, majd az eredményeket egy [report. JSON fájlba](azure-stack-validation-report.md)naplózza.  

## <a name="pfx-encryption"></a>PFX-titkosítás

**Probléma** – a pfx titkosítás nem TRIPLEDES – SHA1.

A PFX-fájlok **TripleDES-SHA1** titkosítással való exportálásának **javítása** . Ez az alapértelmezett titkosítás minden Windows 10-ügyfélnél a tanúsítvány beépülő modulból való exportáláskor vagy `Export-PFXCertificate`használatával.

## <a name="read-pfx"></a>PFX olvasása

**Figyelmeztetés** – a jelszó csak a tanúsítványban található személyes adatokat védi.  

**Javítsa** a pfx-fájlok exportálását a tanúsítvány- **Adatvédelem engedélyezésének**opcionális beállításával.  

**Probléma** – a pfx-fájl érvénytelen.  

**Javítsa** újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md)című cikkben ismertetett lépések segítségével.

## <a name="signature-algorithm"></a>Aláírási algoritmus

**Probléma** – az aláírási algoritmus SHA1.

**Javítás** – a sha256 aláírási algoritmusával a tanúsítvány-aláírási kérelem (CSR) újbóli létrehozásához használja a Azure stack-tanúsítványok aláírási kérelmének létrehozását ismertető témakör lépéseit. Ezután küldje el újra a CSR-t a hitelesítésszolgáltatónak a tanúsítvány újbóli kikibocsátásához.

## <a name="private-key"></a>Titkos kulcs

**Probléma** – a titkos kulcs hiányzik, vagy nem tartalmazza a helyi gép attribútumát.  

**Javítás** – az a számítógép, amely a CSR-t generálta, majd exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése a telepítéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)című témakör lépéseit követve. Ezek a lépések a helyi számítógép tanúsítványtárolóból történő exportálást is tartalmazzák.

## <a name="certificate-chain"></a>Tanúsítványlánc

**Probléma** – a tanúsítványlánc nem fejeződött be.  

**Javítás** – a tanúsítványoknak teljes tanúsítványláncot kell tartalmazniuk. Exportálja újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit követve, és válassza az **összes tanúsítvány belefoglalása a minősítési útvonalon lehetőséget, ha lehetséges**.

## <a name="dns-names"></a>DNS-nevek

**Probléma** – a tanúsítvány **DNSNameList** nem tartalmazza a Azure stack szolgáltatási végpontjának nevét vagy egy érvényes helyettesítő karaktert. A helyettesítő karakteres egyezések csak a DNS-név bal szélső névterére érvényesek. A `*.region.domain.com` például csak `portal.region.domain.com`esetében érvényes, nem `*.table.region.domain.com`.

**Javítás** – a Azure stack tanúsítványok aláírási kérelmének létrehozása című témakör lépéseit követve újragenerálja a CSR-t a megfelelő DNS-nevekkel a Azure stack végpontok támogatásához. Küldje el újra a CSR-t egy hitelesítésszolgáltatótól. Ezután kövesse a [Azure stack PKI-tanúsítványok előkészítése a telepítéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit, hogy exportálja a tanúsítványt a CSR-t létrehozó gépről.  

## <a name="key-usage"></a>Kulcshasználat

**Probléma** – a kulcshasználat hiányzik a digitális aláírás vagy a kulcs titkosítási, vagy a kibővített kulcshasználat hiányzik a kiszolgálói hitelesítés vagy az ügyfél-hitelesítés.  

**Javítás** – a [Azure stack-tanúsítványok aláírási kérelmének létrehozása](azure-stack-get-pki-certs.md) című témakörben ismertetett lépéseket követve újragenerálja a CSR-t a helyes kulcshasználat attribútumokkal. Küldje el újra a CSR-t a hitelesítésszolgáltatóhoz, és győződjön meg arról, hogy a tanúsítványsablon nem írja felül a kérelemben szereplő kulcshasználat felülírását.

## <a name="key-size"></a>Kulcs mérete

**Probléma** – a kulcs mérete kisebb, mint 2048.

**Javítás** – a [Azure stack tanúsítvány-aláírási kérések](azure-stack-get-pki-certs.md) létrehozásához szükséges lépéseket követve ÚJRAgenerálja a CSR-t a megfelelő kulcs hosszával (2048), majd küldje el újra a CSR-t a hitelesítésszolgáltatóhoz.

## <a name="chain-order"></a>Lánc sorrendje

**Probléma** – a tanúsítványlánc sorrendje helytelen.  

**Javítsa** újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) című témakör lépéseit követve, és válassza az **összes tanúsítvány belefoglalása a minősítési útvonalon lehetőséget, ha lehetséges**. Győződjön meg arról, hogy csak a levél tanúsítvány van kiválasztva exportáláshoz.

## <a name="other-certificates"></a>Egyéb tanúsítványok

**Probléma** – a pfx-csomag olyan tanúsítványokat tartalmaz, amelyek nem a levél tanúsítványát vagy a tanúsítványlánc részét képezik.  

**Javítsa** újra a tanúsítványt a [Azure stack PKI-tanúsítványok előkészítése az üzembe helyezéshez](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)című témakör lépéseit követve, és válassza az **összes tanúsítvány belefoglalása a minősítési útvonalon lehetőséget, ha lehetséges**. Győződjön meg arról, hogy csak a levél tanúsítvány van kiválasztva exportáláshoz.

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
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, majd tekintse át a **fő** **-és** alverziókat:

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

2. A PowerShell-parancssorból futtassa a következő parancsmagot a PFX-jelszó megadásához. Cserélje le a `PFXpassword`t a tényleges jelszóra:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. A PowerShell-parancssorból futtassa a következő parancsot egy új PFX-fájl exportálásához:

   - A `-PfxPath`mezőben határozza meg a PFX-fájl elérési útját. A következő példában az elérési út `.\certificates\ssl.pfx`.
   - `-ExportPFXPath`esetében adja meg az exportálandó PFX-fájl helyét és nevét. A következő példában az elérési út `.\certificates\ssl_new.pfx`:

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

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók az Azure Stack biztonságáról](azure-stack-rotate-secrets.md)
