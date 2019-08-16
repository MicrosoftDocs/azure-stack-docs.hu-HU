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
ms.date: 08/14/2019
ms.author: mabrigg
ms.lastreviewed: 08/14/2019
ms.reviewer: ppacent
ms.openlocfilehash: 92b33603ee75560d66b6604188c2ae103a1d10a3
ms.sourcegitcommit: 6284fd52a61680ee4ba3a73ce8d13c9c5496d838
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69519770"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Azure Stack eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása

*Vonatkozik: Integrált rendszerek Azure Stack*

Az eredeti berendezésgyártó (OEM) frissítései a Azure Stack hardveres összetevőkre alkalmazhatók az illesztőprogramok és a belső vezérlőprogram-javítások, valamint a biztonsági javítások fogadására, miközben minimalizálják a felhasználókra gyakorolt hatást. Ebből a cikkből megtudhatja, hogyan telepítheti az OEM-frissítéseket, az OEM kapcsolattartási adatokat, és hogyan alkalmazhat egy OEM-frissítést.

## <a name="overview-of-oem-updates"></a>Az OEM-frissítések áttekintése

A frissítések Microsoft Azure Stack mellett számos számítógépgyártó is kiadja a Azure Stack hardver, például az illesztőprogram és a belső vezérlőprogram frissítéseinek rendszeres frissítéseit. Ezeket az **OEM-csomagok frissítéseinek**nevezzük. Ha meg szeretné tudni, hogy az OEM-kiadás OEM-csomagok frissítéseit tartalmazza-e, tekintse [meg az oem Azure stack dokumentációját](#oem-contact-information).

A Azure Stack Update 1905-es frissítéstől kezdve ezek az OEM-csomagok frissítései a **updateadminaccount** Storage-fiókba kerülnek, és a Azure stack felügyeleti portálon keresztül lesznek végrehajtva. További információ: OEM- [frissítések alkalmazása](#apply-oem-updates).

Kérje meg az eredeti berendezésgyártó (OEM) adatait az adott értesítési folyamatról, hogy biztosítsa az OEM-csomagok frissítési értesítéseinek elérését a szervezet számára.

Egyes hardvergyártók esetében szükség lehet a belső vezérlőprogram frissítési folyamatát kezelő *hardveres szállító virtuális gépre* . További információ: hardveres [virtuális gép konfigurálása](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>OEM kapcsolattartási adatok 

Ez a szakasz OEM kapcsolattartási adatokat és az OEM Azure Stack referenciaanyagok hivatkozásait tartalmazza.

| Hardveres partner | Régió | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Összes | [A Cisco integrált rendszer Microsoft Azure Stack üzemeltetési útmutatóhoz](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Kibocsátási megjegyzések a Microsoft Azure Stack Cisco integrált rendszeréhez](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Összes | [Felhő Microsoft Azure Stack 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Felhő Microsoft Azure Stack 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÁN | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EURÓPA, KÖZEL-KELET ÉS AFRIKA | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (fiók és bejelentkezés szükséges)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Összes | [Microsoft Azure Stack HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Összes | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/belső vezérlőprogram csomagja](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[a Terra Azure Stack dokumentációja (beleértve a cserélhető adattartománnyal együtt)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>OEM-frissítések alkalmazása

Alkalmazza az OEM-csomagokat a következő lépésekkel:

1. Az OEM-csomag letöltéséhez vegye fel a kapcsolatot az OEM-vel a legjobb módszerrel.
2. Készítse elő az OEM-csomagot az [integrált rendszerek frissítési csomagjainak letöltése](azure-stack-servicing-policy.md#download-update-packages-for-integrated-systems)című témakörben leírtak szerint.
3. Alkalmazza a frissítéseket a következő témakörben ismertetett lépésekkel: a [frissítések alkalmazása Azure stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Hardveres gyártó virtuális gép konfigurálása

Egyes hardvergyártók esetében előfordulhat, hogy egy virtuális gépnek segítségre van szüksége az OEM-frissítési folyamathoz. Ezeknek a virtuális gépeknek a létrehozása a hardvergyártó feladata lesz. A virtuális gépek létrehozása után beállíthatja őket a **set-OEMExternalVM** parancsmaggal a privilegizált végponton.

További információ a Azure Stack rendszerjogosultságú végpontról: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).

1.  Hozzáférés a Kiemelt végponthoz.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurálja a hardveres gyártó virtuális gépet a **set-OEMExternalVM** parancsmag használatával. A parancsmag ellenőrzi a **-VMTYPE** `ProxyVM`IP-címét és hitelesítő adatait. A **-VMType** `HardwareManager` esetében a parancsmag nem ellenőrzi a bemenetet.

    ```powershell  
    $VMCred = Get-Credential
    
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -credential $using:VMCred
    ```

## <a name="next-steps"></a>További lépések

[Azure Stack frissítések](azure-stack-updates.md)