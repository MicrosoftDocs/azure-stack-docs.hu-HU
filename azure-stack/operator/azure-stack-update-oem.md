---
title: Az eredeti berendezésgyártó (OEM) frissítésének alkalmazása Azure Stack hubhoz
description: Útmutató az eredeti berendezésgyártó (OEM) frissítésének Azure Stack hub-ra való alkalmazásához.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/15/2019
ms.author: inhenkel
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 8b8334b4a835952c9122e71ac764c3f1c52d99c6
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696702"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Azure Stack hub eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása

Az eredeti berendezésgyártó (OEM) frissítéseit alkalmazhatja az Azure Stack hub hardveres összetevőire, hogy az illesztőprogram-és belső vezérlőprogram-javításokat, valamint a biztonsági javításokat is alkalmazza, miközben minimalizálja a felhasználókra gyakorolt hatást. Ebből a cikkből megtudhatja, hogyan telepítheti az OEM-frissítéseket, az OEM kapcsolattartási adatokat, és hogyan alkalmazhat egy OEM-frissítést.

## <a name="overview-of-oem-updates"></a>Az OEM-frissítések áttekintése

Microsoft Azure Stack hub frissítései mellett számos számítógépgyártó is kiadja az Azure Stack hub-hardver, például az illesztőprogram-és a belső vezérlőprogram frissítéseinek rendszeres frissítéseit. Ezeket az **OEM-csomagok frissítéseinek**nevezzük. Ha szeretné megismerni, hogy a SZÁMÍTÓGÉPGYÁRTÓ az OEM-csomagok frissítéseit, tekintse [meg az oem Azure stack hub dokumentációját](#oem-contact-information).

A rendszer feltölti ezeket az OEM-csomagok frissítéseit a **updateadminaccount** Storage-fiókjába, és az Azure stack hub felügyeleti portálján keresztül alkalmazza őket. További információ: OEM- [frissítések alkalmazása](#apply-oem-updates).

Kérje meg az eredeti berendezésgyártó (OEM) adatait az adott értesítési folyamatról, hogy biztosítsa az OEM-csomagok frissítési értesítéseinek elérését a szervezet számára.

Egyes hardvergyártók esetében szükség lehet a belső vezérlőprogram frissítési folyamatát kezelő *hardveres szállító virtuális gépre* . További információ: [hardveres virtuális gép konfigurálása](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>OEM kapcsolattartási adatok 

Ez a szakasz az OEM kapcsolattartási adatokat és az OEM Azure Stack hub-segédanyagokra mutató hivatkozásokat tartalmazza.

| Hardveres partner | Régió | URL-cím |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Összes | [Cisco integrált rendszer Microsoft Azure Stack hub üzemeltetési útmutatóhoz](https://aka.ms/aa708e2)<br><br>[FKR C sorozatú rack-Mount FKR – felügyelt kiszolgáló szoftver](https://aka.ms/aa700rq) |
| Dell EMC | Összes | [Cloud for Microsoft Azure Stack hub 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack hub 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japán | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA & Egyesült Államok | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Összes | [Microsoft Azure Stack hub HPE-ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Összes | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/belső vezérlőprogram csomagja](https://aka.ms/AA6z600)<br>[a Terra Azure Stack hub dokumentációja (beleértve a cserélhető adatközpontot)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>OEM-frissítések alkalmazása

Alkalmazza az OEM-csomagokat a következő lépésekkel:

> [!IMPORTANT]
> Az Azure Stack hub frissítéseinek alkalmazása előtt győződjön meg róla, hogy végrehajtotta a [frissítés előtti ellenőrzőlista](release-notes-checklist.md) **összes** lépését, és ütemezte a megfelelő karbantartási időszakot az alkalmazott frissítési típushoz.

1. A következőkre kell felvennie a kapcsolatot az OEM-vel:
      - Határozza meg az OEM-csomag aktuális verzióját.  
      - Az OEM-csomag letöltésére szolgáló legjobb módszer megkeresése.  
2. Az OEM-csomagok frissítésének alkalmazása előtt mindig alkalmazza a rendszer aktuális Azure Stack hub-verziójában elérhető legújabb Azure Stack hub gyorsjavítást. További információ a gyorsjavításokról: [Azure stack hub-gyorsjavítások](azure-stack-servicing-policy.md).
3. Készítse elő az OEM-csomagot az [integrált rendszerek frissítési csomagjainak letöltése](azure-stack-servicing-policy.md)című témakörben leírtak szerint.
4. Alkalmazza a frissítéseket az [Azure stack hub frissítések alkalmazása](azure-stack-apply-updates.md)című témakörében ismertetett lépésekkel.

## <a name="configure-hardware-vendor-vm"></a>Hardveres gyártó virtuális gép konfigurálása

Egyes hardvergyártók esetében előfordulhat, hogy egy virtuális gépnek segítségre van szüksége az OEM-frissítési folyamathoz. Ha a **set-OEMExternalVM** parancsmag futtatásához, `HardwareManager` illetve a **hitelesítő**adatokhoz is használni `ProxyVM` szeretné a- **VMType** -t, a hardvergyártó felelős a virtuális gépek létrehozásához és a dokumentálás elvégzéséhez. A virtuális gépek létrehozása után konfigurálja azokat a **set-OEMExternalVM** a privilegizált végponton.

További információ a Azure Stack hub privilegizált végpontján: [a privilegizált végpont használata Azure stack hub-ban](azure-stack-privileged-endpoint.md).

1.  Hozzáférés a Kiemelt végponthoz.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurálja a hardveres gyártó virtuális gépet a **set-OEMExternalVM** parancsmag használatával. A parancsmag ellenőrzi a **-VMType** `ProxyVM`IP-címét és hitelesítő adatait. A **-VMType** `HardwareManager` a parancsmag nem ellenőrzi a bemenetet. A **set-OEMExternalVM** számára megadott **hitelesítő adat** paraméter az egyik, amelyet a hardver gyártójától származó dokumentáció egyértelműen dokumentál.  NEM a jogosultsági szintű végponttal vagy más meglévő Azure Stack hub hitelesítő adatokkal használt CloudAdmin hitelesítő adat.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub frissítései](azure-stack-updates.md)
