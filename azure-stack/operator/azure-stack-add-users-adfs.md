---
title: Azure Stack hub-felhasználók hozzáadása a AD FSban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá Azure Stack hub-felhasználókat Active Directory összevonási szolgáltatások (AD FS) (AD FS) központi telepítésekhez.
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
ms.openlocfilehash: 93e2e60e235ae26016f52a94916f429c38286511
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804877"
---
# <a name="add-azure-stack-hub-users-in-ad-fs"></a>Azure Stack hub-felhasználók hozzáadása a AD FS
A **Active Directory felhasználók és számítógépek** beépülő modullal további felhasználókat adhat hozzá egy Azure stack hub-környezethez Active Directory összevonási szolgáltatások (AD FS) (AD FS) identitás-szolgáltató használatával.

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory-felhasználók hozzáadása
> [!TIP]
> Ez a példa az alapértelmezett azurestack. local ASDK Active Directoryt használja. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely hozzáférést biztosít a Windows felügyeleti eszközökhöz, és nyisson meg egy új Microsoft Management Console (MMC) eszközt.
2. Válassza a **fájl > beépülő modul hozzáadása vagy eltávolítása**lehetőséget.
3. Válassza **Active Directory felhasználók és számítógépek** > **AzureStack. local** > **felhasználókat**.
4. Válassza a **művelet** > **új** > **felhasználó**lehetőséget.
5. Az új objektum – felhasználó területen adja meg a felhasználói adatokat. Kattintson a **Tovább** gombra.
6. Adja meg és erősítse meg a jelszót.
7. Az értékek befejezéséhez kattintson a **tovább** gombra. A felhasználó létrehozásához válassza a **Befejezés** lehetőséget.


## <a name="next-steps"></a>Következő lépések
[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)