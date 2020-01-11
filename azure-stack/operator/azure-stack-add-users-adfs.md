---
title: Azure Stack hub-felhasználók hozzáadása a AD FS
description: Megtudhatja, hogyan adhat hozzá Azure Stack hub-felhasználókat Active Directory összevonási szolgáltatások (AD FS) (AD FS) központi telepítésekhez.
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 2c81d8ccceb4fa2f1e05b006232e3b6513ace469
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880957"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Új Azure Stack hub felhasználói fiók hozzáadása Active Directory összevonási szolgáltatások (AD FS) (AD FS)

A **Active Directory felhasználók és számítógépek** beépülő modullal további felhasználókat adhat hozzá egy Azure stack hub-környezethez, AD FS identitás-szolgáltatóként használva.

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory-felhasználók hozzáadása

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely hozzáférést biztosít a Windows felügyeleti eszközökhöz, és nyisson meg egy új Microsoft Management Console (MMC) eszközt.
2. Válassza a **fájl > beépülő modul hozzáadása vagy eltávolítása**lehetőséget.

   > [!TIP]
   > Cserélje le a *könyvtárat a tartományra* a címtárral egyező tartományra. 

3. Válassza **Active Directory felhasználók és számítógépek** > *Directory-tartomány* > **felhasználók**lehetőséget.
4. Válassza a **művelet** > **új** > **felhasználó**lehetőséget.
5. Az új objektum – felhasználó területen adja meg a felhasználói adatokat. Kattintson a **Tovább** gombra.
6. Adja meg és erősítse meg a jelszót.
7. Az értékek befejezéséhez kattintson a **tovább** gombra. A felhasználó létrehozásához válassza a **Befejezés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

[Alkalmazás identitásának létrehozása Azure Stack hub-erőforrások eléréséhez](azure-stack-create-service-principals.md)