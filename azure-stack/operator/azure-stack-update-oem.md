---
title: Az eredeti berendezésgyártó (OEM) frissítésének alkalmazása Azure Stackra | Microsoft Docs
description: Megtudhatja, hogyan alkalmazhat egy eredeti berendezésgyártó (OEM) frissítést Azure Stackra.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 73671431c70960fa517ee83c68945f14e2621b46
ms.sourcegitcommit: 9cb82df1eccb0486bcabec0bd674162d4820c00c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060188"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Azure Stack eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása

*Vonatkozik: Integrált rendszerek Azure Stack*

Az eredeti berendezésgyártó (OEM) frissítései a Azure Stack hardveres összetevőkre alkalmazhatók az illesztőprogramok és a belső vezérlőprogram-javítások, valamint a biztonsági javítások fogadására, miközben minimalizálják a felhasználókra gyakorolt hatást. Ebből a cikkből megtudhatja, hogyan telepítheti az OEM-frissítéseket, az OEM kapcsolattartási adatokat, és hogyan alkalmazhat egy OEM-frissítést.

## <a name="overview-of-oem-updates"></a>Az OEM-frissítések áttekintése

A frissítések Microsoft Azure Stack mellett számos számítógépgyártó is kiadja a Azure Stack hardver, például az illesztőprogram és a belső vezérlőprogram frissítéseinek rendszeres frissítéseit. Ezeket az **OEM-csomagok frissítéseinek**nevezzük. Ha meg szeretné tudni, hogy az OEM-kiadás OEM-csomagok frissítéseit tartalmazza-e, tekintse [meg az oem Azure stack dokumentációját](#oem-contact-information).

A rendszer feltölti ezeket az OEM-csomagok frissítéseit a **updateadminaccount** Storage-fiókjába, és a Azure stack felügyeleti portálon keresztül alkalmazza őket. További információ: OEM- [frissítések alkalmazása](#apply-oem-updates).

Kérje meg az eredeti berendezésgyártó (OEM) adatait az adott értesítési folyamatról, hogy biztosítsa az OEM-csomagok frissítési értesítéseinek elérését a szervezet számára.

Egyes hardvergyártók esetében szükség lehet a belső vezérlőprogram frissítési folyamatát kezelő *hardveres szállító virtuális gépre* . További információ: hardveres [virtuális gép konfigurálása](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>OEM kapcsolattartási adatok 

Ez a szakasz OEM kapcsolattartási adatokat és az OEM Azure Stack referenciaanyagok hivatkozásait tartalmazza.

| Hardveres partner | Régió | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Összes | [Cisco Azure Stack támogatás/belső vezérlőprogram frissítései – automatikus értesítés (fiók/bejelentkezés szükséges)](https://software.cisco.com/download/redirect?i=!y&mdfid=283862063&softwareid=286320368&release=1.0(0)&os=)<br><br>[Kibocsátási megjegyzések a Microsoft Azure Stack Cisco integrált rendszeréhez](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Összes | [Felhő Microsoft Azure Stack 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Felhő Microsoft Azure Stack 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÁN | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EURÓPA, KÖZEL-KELET ÉS AFRIKA | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (fiók és bejelentkezés szükséges)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Összes | [Microsoft Azure Stack HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Összes | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/belső vezérlőprogram csomagja](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[a Terra Azure Stack dokumentációja (beleértve a cserélhető adattartománnyal együtt)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>OEM-frissítések alkalmazása

Alkalmazza az OEM-csomagokat a következő lépésekkel:

1. A következőkre kell felvennie a kapcsolatot az OEM-vel:
      - Határozza meg az OEM-csomag aktuális verzióját.  
      - Az OEM-csomag letöltésére szolgáló legjobb módszer megkeresése.  
2. Készítse elő az OEM-csomagot az [integrált rendszerek frissítési csomagjainak letöltése](azure-stack-servicing-policy.md)című témakörben leírtak szerint.
3. Alkalmazza a frissítéseket a következő témakörben ismertetett lépésekkel: a [frissítések alkalmazása Azure stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Hardveres gyártó virtuális gép konfigurálása

Egyes hardvergyártók esetében előfordulhat, hogy egy virtuális gépnek segítségre van szüksége az OEM-frissítési folyamathoz. A hardvergyártó felelős a virtuális gépek létrehozásához és a dokumentálás elvégzéséhez, `ProxyVM` ha `HardwareManager` a **set-OEMExternalVM** parancsmag futtatásához szükséges vagy a **-VMType** , valamint hogy melyik hitelesítő adatot kell használni a **- Hitelesítő adatok**. A virtuális gépek létrehozása után konfigurálja azokat a **set-OEMExternalVM** a privilegizált végponton.

További információ a Azure Stack rendszerjogosultságú végpontról: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).

1.  Hozzáférés a Kiemelt végponthoz.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurálja a hardveres gyártó virtuális gépet a **set-OEMExternalVM** parancsmag használatával. A parancsmag ellenőrzi a **-VMTYPE** `ProxyVM`IP-címét és hitelesítő adatait. A **-VMType** `HardwareManager` esetében a parancsmag nem ellenőrzi a bemenetet. A **set-OEMExternalVM** számára megadott **hitelesítő adat** paraméter az egyik, amelyet a hardver gyártójától származó dokumentáció egyértelműen dokumentál.  NEM a jogosultsági szintű végponttal vagy más meglévő Azure Stack hitelesítő adatokkal használt CloudAdmin hitelesítő adat.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>További lépések

[Azure Stack frissítések](azure-stack-updates.md)
