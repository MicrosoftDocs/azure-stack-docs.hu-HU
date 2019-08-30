---
title: Azure Stack felhasználók hozzáadása a AD FSban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá Azure Stack felhasználókat Active Directory összevonási szolgáltatások (AD FS) (AD FS) központi telepítésekhez.
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
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118631"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Azure Stack felhasználók hozzáadása AD FS
A **Active Directory felhasználók és számítógépek** beépülő modullal további felhasználókat adhat hozzá egy Azure stack-környezethez Active Directory összevonási szolgáltatások (AD FS) (AD FS) identitás-szolgáltató használatával.

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory-felhasználók hozzáadása
> [!TIP]
> Ez a példa az alapértelmezett azurestack. local ASDK Active Directoryt használja. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely hozzáférést biztosít a Windows felügyeleti eszközökhöz, és nyisson meg egy új Microsoft Management Console (MMC) eszközt.
2. Válassza a **fájl > beépülő modul hozzáadása vagy eltávolítása**lehetőséget.
3. Válassza **Active Directory felhasználók és számítógépek** > **AzureStack. local** > **felhasználók**lehetőséget.
4. Válassza > azújfelhasználó > művelet lehetőséget.
5. Az új objektum – felhasználó területen adja meg a felhasználói adatokat. Kattintson a **Tovább** gombra.
6. Adja meg és erősítse meg a jelszót.
7. Az értékek befejezéséhez kattintson a **tovább** gombra. A felhasználó létrehozásához válassza a **Befejezés** lehetőséget.


## <a name="next-steps"></a>További lépések
[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)