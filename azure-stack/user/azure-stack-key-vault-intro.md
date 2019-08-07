---
title: A Azure Stack Key Vault bemutatása | Microsoft Docs
description: Ismerje meg, hogyan kezeli a Key Vault a kulcsokat és a titkokat a Azure Stack.
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
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: fc68f80688f6b8cbe0376d332d706c9dc7b6dd92
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842896"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>A Azure Stack Key Vault bemutatása

## <a name="prerequisites"></a>Előfeltételek

* Fizessen elő a Azure Key Vault szolgáltatást tartalmazó ajánlatra.  
* A [PowerShell konfigurálva van Azure Stackhoz való használatra](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Key Vault alapjai

A Azure Stack Key Vault segíti a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kódok védelmét. A Key Vault használatával titkosíthatja a kulcsokat és a titkos kulcsokat, például a következőket:

* Hitelesítési kulcsok
* Tárfiókkulcsok
* Adattitkosítási kulcsok
* . pfx fájlok
* Jelszavak

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és visszavonhatják) a kulcsokra vonatkozó engedélyeket.

Azure Stack-előfizetéssel bárki létrehozhat és használhat kulcstartókat. Bár a Key Vault a fejlesztők és a biztonsági rendszergazdák számára, a szervezet más Azure Stack szolgáltatásait kezelő kezelők is végrehajthatják és kezelhetik. Például a Azure Stack operátor bejelentkezhet egy Azure Stack-előfizetéssel, és létrehozhat egy tárolót azon szervezet számára, amelyben a kulcsokat tárolni szeretné. Ha elkészült, a következőket tehetik:

* Kulcs vagy titkos kód létrehozása vagy importálása.
* Kulcs vagy titkos kód visszavonása vagy törlése.
* Engedélyezze, hogy a felhasználók vagy alkalmazások hozzáférjenek a kulcstartóhoz, így a kulcsok és a titkos kódok is kezelhetők vagy felhasználhatók.
* A kulcshasználat konfigurálása (például aláírás vagy titkosítás).

A kezelő ezt követően egységes erőforrás-azonosítókat (URI-ket) biztosít a fejlesztők számára az alkalmazásokból való meghíváshoz. A kezelők a kulcshasználat naplózási adataival is biztosíthatják a biztonsági rendszergazdákat.

A fejlesztők a kulcsokat API-k használatával közvetlenül is kezelhetik. További információ: [Key Vault fejlesztői útmutatója](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Forgatókönyvek

A következő forgatókönyvek azt írják le, hogyan segíthetnek a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítésében.

### <a name="developer-for-an-azure-stack-app"></a>Azure Stack-alkalmazás fejlesztője

**Probléma** Olyan alkalmazást szeretnék írni Azure Stack, amely kulcsokat használ az aláíráshoz és a titkosításhoz. Szeretném, hogy ezek a kulcsok kívülről legyenek az alkalmazáson, hogy a megoldás megfelelő legyen a földrajzilag elosztott alkalmazásokhoz.

**Nyilatkozat** A kulcsokat tárolóban tárolják, és szükség esetén egy URI hívja meg.

### <a name="developer-for-software-as-a-service-saas"></a>Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő)

**Probléma** Nem szeretném, hogy az ügyfelem kulcsainak és titkos kulcsainak felelőssége vagy lehetséges felelőssége legyen. Azt szeretném, hogy az ügyfelek saját maguk kezeljék a kulcsaikat, így koncentrálhat a legjobb megoldásokhoz, ami a szoftver alapvető funkcióit biztosítja.

**Nyilatkozat** Az ügyfelek a saját kulcsaikat a Azure Stack importálják és kezelhetik.

### <a name="chief-security-officer-cso"></a>Biztonsági igazgató (CSO)

**Probléma** Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot.

**Nyilatkozat** A Key Vault úgy lett kialakítva, hogy a Microsoft ne láthassa vagy kibontsa a kulcsokat. Ha egy alkalmazásnak az ügyféladatok használatával kell titkosítási műveleteket végeznie, Key Vault a kulcsokat használja az alkalmazás nevében. Az alkalmazás nem látja az ügyfél kulcsait. Habár több Azure Stack-szolgáltatást és-erőforrást is használunk, a kulcsokat egyetlen helyről kezelheti Azure Stackban. A tároló egyetlen felületet biztosít, függetlenül attól, hogy hány tárolóban van Azure Stack, mely régiókat támogatják, és mely alkalmazások használják azokat.

## <a name="next-steps"></a>További lépések

* [Azure Stack Key Vault kezelése a portálon](azure-stack-key-vault-manage-portal.md)  
* [A Azure Stack Key Vault kezelése a PowerShell használatával](azure-stack-key-vault-manage-powershell.md)
