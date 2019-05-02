---
title: Az Azure Stack a Key Vault bemutatása |} A Microsoft Docs
description: Ismerje meg, hogyan kezeli az Azure Stack Key Vault a kulcsok és titkok
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 87c93f77011082d3e43b1c7d238999441f1b90c1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298535"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>A Key Vault az Azure Stack bemutatása

## <a name="prerequisites"></a>Előfeltételek

* Meg kell előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.  
* [PowerShell van konfigurálva, az Azure Stackkel való használathoz](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>A Key Vault alapjai

Az Azure Stack a Key Vault segít a kriptográfiai kulcsokat, és a titkos kódok, amelyek felhőalapú alkalmazásokat és szolgáltatásokat használja. A Key Vault használata esetén, titkosítsa a kulcsokat és titkos kódokat, például:

* Hitelesítési kulcsok
* Tárfiókkulcsok
* Az adattitkosítási kulcsokat
* PFX-fájlokat
* Jelszavak

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. Biztonsági rendszergazdák megadása (és visszavonásához) engedélyeket a kulcsokhoz, igény szerint.

Az Azure Stack-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a biztonsági rendszergazdák, a szervezet számára más Azure Stack-szolgáltatásokat felügyelő operátor végrehajtása, és felügyelheti azokat. Például az Azure Stack operátori bejelentkezhet az Azure Stack-előfizetéshez, hozzon létre egy tárolót a szervezet kulcsainak tárolásához és felel, a üzemeltetési feladatokhoz:

* Hozzon létre, vagy importáljon egy kulcs vagy titkos kód.
* Visszavonása, illetve egy kulcs vagy titkos kód törlése.
* Engedélyezze a felhasználók vagy alkalmazások, így azok kezeléséhez, vagy használja a kulcsok és titkos kulcsok a key vault eléréséhez.
* Kulcshasználat konfigurálása (például jelentkezzen vagy titkosítása).

Az üzemeltető majd rendelkező egységes erőforrás-azonosítók (URI) meghívhatnak fejlesztőknek biztosít. Operátorok is lehetővé teszi biztonsági rendszergazdák kulcshasználat naplózás adatokkal.

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információ a Key Vault fejlesztői útmutatójában talál.

## <a name="scenarios"></a>Forgatókönyvek

A következő forgatókönyveket ismertetik, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek.

### <a name="developer-for-an-azure-stack-application"></a>Fejlesztői az Azure Stack-alkalmazáshoz

**Probléma:** Szeretnék írni egy alkalmazást az Azure Stack, amely kulcsokat használ az aláíráshoz és titkosításhoz. Ezek a kulcsok az alkalmazáson, lehet, így, hogy a megoldás egy földrajzilag elosztott alkalmazás szeretnék.

**Utasítás:** Kulcsok tárolása tárolóban történik, és szükség esetén egy URI-t hív.

### <a name="developer-for-software-as-a-service-saas"></a>Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő)

**Probléma:** Nem szeretnék felelősséget és esetleges felelősségre saját ügyfél a kulcsok és titkos kulcsok. Ügyfelek kezelhetik a kulcsokat, és szeretném, hogy szeretnék végezni, mi a teendő, leginkább, amely biztosítja az alapvető szoftverfunkciók összpontosíthat.

**Utasítás:** Ügyfelek importálhatják a saját kulcsaikat az Azure Stack, és felügyeli őket.

### <a name="chief-security-officer-cso"></a>Biztonsági vezető (CSO)

**Probléma:** Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot.

**Utasítás:** A Key Vault kialakításából adódóan a Microsoft nem tekintheti meg, és nem fejtheti vissza a kulcsokat. Ha egy alkalmazásnak titkosítási műveleteket ügyfél kulcsok használatával kell, a Key Vault használja a kulcsok az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait. Bár több Azure Stack-szolgáltatásokat és erőforrásokat, az Azure Stack egyetlen helyről kezelheti a kulcsokat. A tároló egyetlen felületet, hány tárolóval rendelkezik az Azure Stackben, mely régiókban, függetlenül biztosít, támogatás, és mely alkalmazások használják őket.

## <a name="next-steps"></a>További lépések

* [A portál által kezelése a Key Vault az Azure Stackben](azure-stack-key-vault-manage-portal.md)  
* [A Key Vault az Azure Stackben kezelése a PowerShell használatával](azure-stack-key-vault-manage-powershell.md)

