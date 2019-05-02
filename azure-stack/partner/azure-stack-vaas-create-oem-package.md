---
title: Az Azure Stack érvényesítési gyakorlati tanácsok |} A Microsoft Docs
description: Ez a cikk ismerteti az ajánlott eljárások a érvényesítési szolgáltatásként.
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
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298667"
---
# <a name="create-an-oem-package"></a>OEM-csomag létrehozása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack OEM-kiterjesztési csomag olyan mechanizmusok, mely OEM által meghatározott tartalom hozzáadódik Azure Stack-infrastruktúra üzembe helyezés, valamint a működési folyamatok például frissítése, bővítése és a mező helyettesítő használatra.

## <a name="creating-the-package"></a>A csomag létrehozása

Miután létrehozott és érvényesítve, az OEM-kiterjesztés csomagjának VaaS is használható.  A folytatás előtt győződjön meg arról, hogy végrehajtotta a lépéseket a [az OEM-csomag létrehozása](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). A csomag majd együtt VaaS terhelésiteszt-eredményei jelentkezik be a csomag érvényesítése munkafolyamat elküldése a Microsoftnak. A következő lépések bemutatják, hogyan szeretné a létrehozott fájlokat, amelyet felhasználhat VaaS egyetlen zip-fájlban.

1. Határozza meg a csomag a következő tartalommal:
    - A csomag tartalma tartalmazó zip-fájlt
    - A jegyzékfájlt nevű `oemMetadata.xml`, amely meg kell egyezniük a tartalmat a gyökérkönyvtárában található a csomag tartalmát a metadata.xml fájlt.

2. Válassza ki azokat a tartalomfájlokat, és létrehozhat egy zip-fájl tartalma:

    ![Zip-fájl tartalmának](media/vaas-create-oem-package-1.png) ![elem tartalom tömörítése](media/vaas-create-oem-package-2.png)

3. Nevezze át az eredményül kapott fájl, úgy, hogy azonosítható legyen elég beszédes legyen.

## <a name="verifying-the-contents"></a>A tartalom ellenőrzése

A zip-fájl szerkezete ellenőrzéséhez azt vizsgálja, és ellenőrizheti, hogy vannak-e almappák nélkül. A TLD tartalmaz a ZIP tartalmát. Egy érvényes csomag szerkezete alább látható.
> [!IMPORTANT]
> A mappa tartalma helyett a becsomagolás sikertelen csomagaláíró miatt.

![Megfelelően tömörített csomag tartalma](media/vaas-create-oem-package-3.png)

A zip-fájl most VaaS feltöltött és a csomag érvényesítése munkafolyamat a Microsoft aláírásával.

## <a name="next-steps"></a>További lépések

- [Az OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md)
