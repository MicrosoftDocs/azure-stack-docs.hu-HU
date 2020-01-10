---
title: Az Azure Stack hub Key Vault bemutatása | Microsoft Docs
description: Ismerje meg, hogyan kezeli a Key Vault a kulcsokat és a titkokat Azure Stack hub-ban.
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
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 4c2c2c2c514949924204f448c04079176b982ff3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820372"
---
# <a name="introduction-to-key-vault-in-azure-stack-hub"></a>Az Azure Stack hub Key Vault bemutatása

## <a name="prerequisites"></a>Előfeltételek

* Fizessen elő a Azure Key Vault szolgáltatást tartalmazó ajánlatra.  
* A PowerShell telepítve van és [konfigurálva van Azure stack hubhoz való használatra](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Key Vault alapjai

Az Azure Stack hub Key Vault a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kódok védelmét segíti. A Key Vault használatával titkosíthatja a kulcsokat és a titkos kulcsokat, például a következőket:

* Hitelesítési kulcsok
* Tárfiókkulcsok
* Adattitkosítási kulcsok
* . pfx fájlok
* Jelszavak

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és visszavonhatják) a kulcsokra vonatkozó engedélyeket.

Azure Stack hub-előfizetéssel bárki létrehozhat és használhat kulcstartókat. Bár a Key Vault a fejlesztők és a biztonsági rendszergazdák számára, a szervezet más Azure Stack hub szolgáltatásait kezelő operátor implementálhatja és felügyelheti. Az Azure Stack hub operátor például Azure Stack hub-előfizetéssel tud bejelentkezni, és létrehoz egy tárolót a kulcsok tárolására szolgáló szervezet számára. Ha elkészült, a következőket tehetik:

* Kulcs vagy titkos kód létrehozása vagy importálása.
* Kulcs vagy titkos kód visszavonása vagy törlése.
* Engedélyezze, hogy a felhasználók vagy alkalmazások hozzáférjenek a kulcstartóhoz, így a kulcsok és a titkos kódok is kezelhetők vagy felhasználhatók.
* A kulcshasználat konfigurálása (például aláírás vagy titkosítás).

A kezelő ezt követően egységes erőforrás-azonosítókat (URI-ket) biztosít a fejlesztők számára az alkalmazásokból való meghíváshoz. A kezelők a kulcshasználat naplózási adataival is biztosíthatják a biztonsági rendszergazdákat.

A fejlesztők a kulcsokat API-k használatával közvetlenül is kezelhetik. További információ: [Key Vault fejlesztői útmutatója](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Alkalmazási helyzetek

A következő forgatókönyvek azt írják le, hogyan segíthetnek a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítésében.

### <a name="developer-for-an-azure-stack-hub-app"></a>Azure Stack hub-alkalmazás fejlesztője

**Probléma:** Olyan alkalmazást szeretnék írni Azure Stack hubhoz, amely kulcsokat használ az aláíráshoz és a titkosításhoz. Szeretném, hogy ezek a kulcsok kívülről legyenek az alkalmazáson, hogy a megoldás megfelelő legyen a földrajzilag elosztott alkalmazásokhoz.

**Utasítás:** A kulcsokat tárolóban tárolják, és szükség esetén egy URI hívja meg.

### <a name="developer-for-software-as-a-service-saas"></a>Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő)

**Probléma:** Nem szeretném, hogy az ügyfelem kulcsainak és titkos kulcsainak felelőssége vagy lehetséges felelőssége legyen. Azt szeretném, hogy az ügyfelek saját maguk kezeljék a kulcsaikat, így koncentrálhat a legjobb megoldásokhoz, ami a szoftver alapvető funkcióit biztosítja.

**Utasítás:** Az ügyfelek a saját kulcsaikat az Azure Stack központban is importálhatók és kezelhetik.

### <a name="chief-security-officer-cso"></a>Biztonsági igazgató (CSO)

**Probléma:** Szeretném meggyőződni arról, hogy a szervezetem a kulcs életciklusa felett van, és nyomon követheti a kulcshasználat.

**Utasítás:** A Key Vault úgy lett kialakítva, hogy a Microsoft ne tekintse meg vagy ne bontsa ki a kulcsokat. Ha egy alkalmazásnak az ügyféladatok használatával kell titkosítási műveleteket végeznie, Key Vault a kulcsokat használja az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait. Habár több Azure Stack hub-szolgáltatást és-erőforrást is használunk, a kulcsokat egyetlen helyről kezelheti Azure Stack hub-ban. A tároló egyetlen felületet biztosít, függetlenül attól, hogy hány tárolóval rendelkezik Azure Stack hub-ban, mely régiókat támogatják, és mely alkalmazások használják azokat.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Stack hub Key Vault kezelése a portálon](azure-stack-key-vault-manage-portal.md)  
* [Azure Stack hub Key Vault kezelése a PowerShell használatával](azure-stack-key-vault-manage-powershell.md)
