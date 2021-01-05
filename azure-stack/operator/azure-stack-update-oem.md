---
title: OEM-frissítés alkalmazása Azure Stack hubhoz
description: Útmutató az eredeti berendezésgyártó (OEM) frissítésének Azure Stack hub-ra való alkalmazásához.
author: PatAltimore
ms.topic: how-to
ms.date: 10/15/2019
ms.author: patricka
ms.lastreviewed: 03/04/2020
ms.reviewer: ppacent
ms.openlocfilehash: 8b8c3a39f61693cad9904b99bd1f82b175d53a7b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868863"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Azure Stack hub eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása

Az eredeti berendezésgyártó (OEM) frissítéseit alkalmazhatja az Azure Stack hub hardver-összetevőire az illesztőprogram-frissítések, a belső vezérlőprogram frissítései és a biztonsági javítások beszerzéséhez. Ezek a frissítések a felhasználókra gyakorolt hatás minimalizálása mellett vannak végrehajtva. Ebből a cikkből megtudhatja, hogyan telepítheti az OEM-frissítéseket, az OEM kapcsolattartási adatokat, és hogyan alkalmazhat egy OEM-frissítést.

## <a name="overview-of-oem-updates"></a>Az OEM-frissítések áttekintése

Microsoft Azure Stack hub frissítései mellett számos számítógépgyártó is kiadja az Azure Stack hub-hardver, például az illesztőprogram-és a belső vezérlőprogram frissítéseinek rendszeres frissítéseit. Ezeket a frissítéseket **OEM-csomagok frissítéseinek** nevezzük. Ha szeretné megismerni, hogy a SZÁMÍTÓGÉPGYÁRTÓ az OEM-csomagok frissítéseit, tekintse [meg az oem Azure stack hub dokumentációját](#oem-contact-information).

A rendszer feltölti ezeket az OEM-csomagok frissítéseit a **updateadminaccount** Storage-fiókjába, és az Azure stack hub felügyeleti portálján keresztül alkalmazza őket. További információ: OEM- [frissítések alkalmazása](#apply-oem-updates).

Kérdezze meg az OEM-t az adott értesítési folyamatról, hogy az OEM-csomagok frissítési értesítései elérhetők legyenek a szervezet számára.

Egyes hardvergyártók esetében szükség lehet a belső vezérlőprogram frissítési folyamatát kezelő *hardveres szállító virtuális gépre* . További információ: a [hardveres gyártó virtuális gép konfigurálása](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>OEM kapcsolattartási adatok

Ez a szakasz az OEM kapcsolattartási adatokat és az OEM Azure Stack hub-segédanyagokra mutató hivatkozásokat tartalmazza.

| Hardveres partner | Régió | URL-cím |
|-----|----|-----|
| Cisco | Mind | [Cisco integrált rendszer Microsoft Azure Stack hub üzemeltetési útmutatóhoz](https://aka.ms/aa708e2)<br><br>[FKR C sorozat Rack-Mount UCS-Managed Server szoftver](https://aka.ms/aa700rq) |
| Dell EMC | Mind | [Cloud for Microsoft Azure Stack hub 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack hub 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japán | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA & EGYESÜLT ÁLLAMOK | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Mind | [Microsoft Azure Stack hub HPE-ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Mind | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/belső vezérlőprogram csomagja](https://aka.ms/AA6z600)<br>[a Terra Azure Stack hub dokumentációja (beleértve a cserélhető adatközpontot)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>OEM-frissítések alkalmazása

Alkalmazza az OEM-csomagokat a következő lépésekkel:

> [!IMPORTANT]
> Az Azure Stack hub frissítéseinek alkalmazása előtt győződjön meg róla, hogy végrehajtotta a [frissítés előtti ellenőrzőlista](release-notes-checklist.md) **összes** lépését, és ütemezte a megfelelő karbantartási időszakot az alkalmazott frissítési típushoz.

1. Lépjen kapcsolatba az OEM-vel a következővel:
      - Határozza meg az OEM-csomag aktuális verzióját.  
      - Az OEM-csomag letöltésére szolgáló legjobb módszer megkeresése.  
2. Az OEM-csomagok frissítésének alkalmazása előtt mindig alkalmazza a rendszer aktuális Azure Stack hub-verziójában elérhető legújabb Azure Stack hub-gyorsjavítást. A gyorsjavításokkal kapcsolatos további információkért lásd: [Azure stack hub-gyorsjavítások](azure-stack-servicing-policy.md).
3. Készítse elő az OEM-csomagot az [integrált rendszerek frissítési csomagjainak letöltése](azure-stack-servicing-policy.md)című témakörben leírtak szerint.
4. Alkalmazza a frissítéseket az [Azure stack hub frissítések alkalmazása](azure-stack-apply-updates.md)című témakörében ismertetett lépésekkel.

## <a name="configure-hardware-vendor-vm"></a>Hardveres gyártó virtuális gép konfigurálása

Egyes hardvergyártók esetében szükség lehet egy virtuális gépre (VM) az OEM-frissítési folyamat segítésére. A hardvergyártó felelős a virtuális gépek létrehozásában és a dokumentálás `ProxyVM` `HardwareManager` során, ha a **set-OEMExternalVM** parancsmag futtatásához vagy a **-VMType** szükséges, valamint **a hitelesítő** adatokhoz használandó hitelesítő adatokat. A virtuális gépek létrehozása után konfigurálja azokat a **set-OEMExternalVM** a privilegizált végponton.

További információ a Azure Stack hub Kiemelt jogosultságokkal rendelkező végpontján: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

1. Hozzáférés a Kiemelt végponthoz.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurálja a hardveres gyártó virtuális gépet a **set-OEMExternalVM** parancsmag használatával. A parancsmag ellenőrzi a **-VMTYPE** IP-címét és hitelesítő adatait `ProxyVM` . A **-VMType** esetében `HardwareManager` a parancsmag nem ellenőrzi a bemenetet. A **set-OEMExternalVM** számára megadott **hitelesítő adat** paraméter az egyik, amelyet a hardver gyártójától származó dokumentáció egyértelműen dokumentál.  *Nem* a jogosultsági szintű végponttal vagy más meglévő Azure stack hub hitelesítő adatokkal használt CloudAdmin hitelesítő adat.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        {
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>További lépések

- [Azure Stack hub frissítései](azure-stack-updates.md)
