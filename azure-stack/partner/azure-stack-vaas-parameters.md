---
title: Az Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek |} A Microsoft Docs
description: Az Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0979588e358d1e163a29ab46bdbe99c27fc37649
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299833"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Az Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az általános paraméterek értékeit, például a környezeti változókat és a felhasználói is az összes teszt érvényesítési (VaaS) szolgáltatás szükséges hitelesítő adatokat. Ezeket az értékeket a munkafolyamat szintjén vannak meghatározva létrehozása vagy módosítása egy munkafolyamatot. Tesztek ütemezése, ezeket az értékeket paraméterként alatt a munkafolyamat minden egyes teszteléséhez.

> [!NOTE]
> Minden teszt a saját paraméterek készletét definiálja. Adja meg az időt a teszt előfordulhat, hogy adjon meg egy értéket, függetlenül a következő általános paramétereket, vagy a közös paraméter értéke a felülírását is lehetővé teheti.

## <a name="environment-parameters"></a>Környezet paraméterek

Környezet paramétereket ismertetik a teszt alatt álló Azure Stack-környezet. Ezek az értékek kódjának létrehozásával és a egy Azure Stack-blokk információkat teszteli példány fájl feltöltése kell adni.

> [!NOTE]
> A munkafolyamatokban hivatalos érvényesítési környezet paraméterek munkafolyamat létrehozása után nem módosítható.

### <a name="generate-the-stamp-information-file"></a>A blokk-információs fájl létrehozása

1. Jelentkezzen be a DVM vagy minden olyan gép, amely hozzáfér az Azure Stack-környezet.
2. Futtassa a következő parancsokat egy emelt szintű PowerShell-ablakban:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Keresse meg az értékeket a Dokumentumokat konfigurációs fájlban

Környezeti paraméter értékeit is manuálisan található a **Dokumentumokat konfigurációs fájl** található `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` a DVM a.

## <a name="test-parameters"></a>Vizsgálati paraméter

Általános tesztelési paraméterek közé tartozik a bizalmas adatokat, amelyeket nem lehet tárolni a konfigurációs fájlok. Ezek manuális megadása kötelező.

Paraméter    | Leírás
-------------|-----------------
A bérlői rendszergazda felhasználó                            | Az Azure Active Directory Bérlői rendszergazda az AAD-címtárában található a szolgáltatás-rendszergazda által üzembe helyezett. Ez a felhasználó hajt végre a bérlői szintű műveletekhez, például üzembe helyezése sablonokkal az erőforrásokat (virtuális gépek, tárfiókok, stb.) és számítási feladatok végrehajtása. A bérlői fiók kiépítése a részletekért lásd: [adjon hozzá egy új Azure Stack-bérlő](../operator/azure-stack-add-new-user-aad.md).
Szolgáltatás-rendszergazda felhasználó             | Az Azure Active Directory-rendszergazda az Azure Stack üzembe helyezése során megadott AAD Directory-bérlő. Keresse meg `AADTenant` a Dokumentumokat a konfigurációban fájlt, és válassza ki az értéket a `UniqueName` elemet.
Felhőalapú rendszergazdai felhasználó               | Az Azure Stack tartományi rendszergazdai fiókot (például `contoso\cloudadmin`). Keresse meg `User Role="CloudAdmin"` a Dokumentumokat a konfigurációban fájlt, és válassza ki az értéket a `UserName` elemet.
Diagnosztikai kapcsolati karakterlánc          | A SAS URL-címe egy Azure Storage-fiókot, mely a diagnosztikai naplók lesznek másolva a teszt végrehajtása során. Az SAS URL-cím létrehozása az utasításokért lásd: [a diagnosztikai kapcsolati karakterlánc létrehozása](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> A **diagnosztikai kapcsolati karakterlánc** a folytatás előtt érvényesnek kell lennie.

### <a name="generate-the-diagnostics-connection-string"></a>A diagnosztika kapcsolati karakterlánc létrehozása

A diagnosztika kapcsolati karakterlánc megadása kötelező a diagnosztikai naplók tárolásához teszt végrehajtása során. A telepítés során létrehozott Azure Storage-fiókot használja (lásd: [állítsa be a szolgáltatási erőforrások, az érvényesítési](azure-stack-vaas-set-up-resources.md)) egy közös hozzáférésű jogosultságkód (SAS) URL-cím VaaS hozzáférést naplókat tölthet fel a tárfiók létrehozásához.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Válassza ki **Blob** a **engedélyezett szolgáltatások beállítások**. Kapcsolja ki a fennmaradó beállításokat.

1. Válassza ki **szolgáltatás**, **tároló**, és **objektum** a **engedélyezett erőforrástípusok**.

1. Válassza ki **olvasási**, **írási**, **lista**, **hozzáadása**, **létrehozása** a **engedélyezett engedélyek**. Kapcsolja ki a fennmaradó beállításokat.

1. Állítsa be **kezdési idő** az aktuális idő és **befejezési idő** három hónappal az aktuális időponthoz képest.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> Az SAS URL-címet megadni, ha az URL-cím jött létre a Befejezés időpontjában lejár.  
Tesztek ütemezése, győződjön meg arról, hogy az URL-címet a érvényes legalább 30 nappal, valamint (három hónapig ajánlott) teszt végrehajtásához szükséges időt.

## <a name="next-steps"></a>További lépések

- Ismerje meg [mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)