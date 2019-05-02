---
title: Felhasználók hozzáadása az Azure Stack AD FS |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá felhasználókat az Azure Stack az AD FS-telepítések
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 93541cd02c3d4110e008e5f3f7011f53be897475
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986236"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Azure Stack-felhasználók hozzáadása az AD FS-ben
Használhatja a **Active Directory – felhasználók és számítógépek** beépülő modul segítségével adhat hozzá további felhasználókat az Azure Stack-környezet az identitás-szolgáltatóként az AD FS kihasználva.

## <a name="add-windows-server-active-directory-users"></a>A Windows Server Active Directory-felhasználók hozzáadása
> [!TIP]
> Ebben a példában az alapértelmezett azurestack.local ASDK active Directoryt használja. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, hogy hozzáférést biztosíthat a Windows felügyeleti eszközök, és nyisson meg egy új Microsoft Management Console (MMC).
2. Válassza ki **fájl > hozzáadása vagy eltávolítása a beépülő modul**.
3. Válassza ki **az Active Directory – felhasználók és számítógépek** > **AzureStack.local** > **felhasználók**.
4. Válassza ki **művelet** > **új** > **felhasználói**.
5. Új objektum – felhasználó, adja meg felhasználói információk adatokat. Kattintson a **Tovább** gombra.
6. Adja meg és erősítsen meg egy jelszót.
7. Válassza ki **tovább** értékek véglegesítéséhez. Válassza ki **Befejezés** a felhasználó létrehozásához.


## <a name="next-steps"></a>További lépések
[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)