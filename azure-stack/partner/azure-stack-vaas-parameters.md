---
title: Gyakori munkafolyamat-paraméterek az Varga-ben
titleSuffix: Azure Stack Hub
description: Ismerkedjen meg a Azure Stack hub ellenőrzésének általános munkafolyamat-paramétereinek szolgáltatásként való megadásával.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a9fce93fb66793ec933d2a8182811bd0577a2588
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79293999"
---
# <a name="workflow-common-parameters-for-azure-stack-hub-validation-as-a-service"></a>Általános munkafolyamat-paraméterek Azure Stack hub érvényesítéséhez szolgáltatásként

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az általános paraméterek közé tartoznak például a környezeti változók és a felhasználói hitelesítő adatok, amelyeket az érvényesítési szolgáltatásként szolgáló összes teszt igényel. A munkafolyamatok létrehozásakor vagy módosításakor ezek az értékek a munkafolyamat szintjén vannak meghatározva. Az ütemezési tesztek során ezeket az értékeket paraméterként adja át a rendszer az egyes tesztekhez a munkafolyamatban.

> [!NOTE]
> Az egyes tesztek saját paramétereket határoznak meg. A tesztelés időpontjában előfordulhat, hogy egy tesztnek a közös paraméterektől függetlenül kell megadnia egy értéket, vagy felülbírálhatja a közös paraméter értékét.

## <a name="environment-parameters"></a>Környezeti paraméterek

A környezeti paraméterek a Azure Stack hub környezetét írják le a test alatt. Ezeket az értékeket úgy kell megadnia, hogy egy Azure Stack hub Stamp információs fájlt hoz létre és tölt fel a tesztelni kívánt példányhoz.

> [!NOTE]
> A hivatalos ellenőrzési munkafolyamatokban a környezeti paraméterek nem módosíthatók a munkafolyamat létrehozása után.

### <a name="generate-the-stamp-information-file"></a>A Stamp információs fájljának előállítása

1. Jelentkezzen be a DVM vagy bármely olyan gépre, amely hozzáfér az Azure Stack hub-környezethez.
2. Futtassa a következő parancsokat egy emelt szintű PowerShell-ablakban:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Értékek megkeresése az ECE konfigurációs fájlban

A környezeti paraméterek értékei manuálisan is megtalálhatók a DVM található `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` **ECE konfigurációs fájlban** .

## <a name="test-parameters"></a>Paraméterek tesztelése

A gyakori tesztelési paraméterek olyan bizalmas információkat tartalmaznak, amelyek nem tárolhatók a konfigurációs fájlokban. Ezeket a paramétereket manuálisan kell megadni.

Paraméter    | Leírás
-------------|-----------------
Bérlői rendszergazda felhasználó                            | Azure Active Directory (Azure AD) bérlői rendszergazda, amelyet a szolgáltatás rendszergazdája telepített a HRE könyvtárban. Ez a felhasználó bérlői szintű műveleteket hajt végre, például sablonokat helyez üzembe az erőforrások (virtuális gépek, Storage-fiókok stb.) beállításához és a számítási feladatok végrehajtásához. A bérlői fiók üzembe helyezésével kapcsolatos részletekért tekintse meg az [új Azure stack hub-bérlő hozzáadása](../operator/azure-stack-add-new-user-aad.md)című témakört.
Szolgáltatás-rendszergazda felhasználó             | Az Azure Stack hub üzembe helyezése során megadott Azure ad-címtár-bérlő Azure AD-rendszergazdája. `AADTenant` Keressen rá az ECE konfigurációs fájljába, és válassza ki az értéket `UniqueName` a elemben.
Felhőbeli rendszergazda felhasználó               | Azure Stack hub tartományi rendszergazdai fiók (például `contoso\cloudadmin`). `User Role="CloudAdmin"` Keressen rá az ECE konfigurációs fájljába, és válassza ki az értéket `UserName` a elemben.
Diagnosztikai kapcsolatok karakterlánca          | Egy Azure Storage-fiókhoz tartozó SAS URL-cím, amelybe a rendszer a diagnosztikai naplókat másolja a tesztek végrehajtása során. A SAS URL-cím létrehozásával kapcsolatos utasításokért lásd: [a diagnosztikai kapcsolatok karakterláncának létrehozása](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> A folytatás előtt a **diagnosztikai kapcsolatok karakterláncának** érvényesnek kell lennie.

### <a name="generate-the-diagnostics-connection-string"></a>A diagnosztikai kapcsolatok karakterláncának létrehozása

Diagnosztikai naplók tárolásához a diagnosztika-kapcsolatok karakterlánca szükséges a tesztelés végrehajtása során. A telepítés során létrehozott Azure Storage-fiókkal (lásd: [az érvényesítés szolgáltatás-erőforrások beállításával](azure-stack-vaas-set-up-resources.md)) hozzon létre egy közös hozzáférési aláírási (SAS-) URL-címet, amely lehetővé tenné, hogy a rendszer a naplókat a Storage-fiókba töltse fel.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Válassza a **blob** lehetőséget az **engedélyezett szolgáltatások beállításai**közül. Törölje a többi beállítást.

1. Válassza ki a **szolgáltatást**, a **tárolót**és az **objektumot** az **engedélyezett erőforrástípusok**közül.

1. Válassza az **olvasás**, **írás**, **lista**, **Hozzáadás**, **Létrehozás** az **engedélyezett engedélyekkel**lehetőséget. Törölje a többi beállítást.

1. Állítsa be a **kezdési időt** az aktuális időpontra, és **fejezze** be az aktuális időpontot három hónapig.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> Az SAS URL-cím az URL-cím létrehozásakor megadott befejezési időpontban lejár. Az ütemezési tesztek során győződjön meg arról, hogy az URL-cím legalább 30 napig érvényes, valamint a tesztelés végrehajtásához szükséges idő (három hónap javasolt).

## <a name="next-steps"></a>További lépések

- Tudnivalók az [érvényesítésről a szolgáltatás kulcsfontosságú fogalmai szerint](azure-stack-vaas-key-concepts.md)