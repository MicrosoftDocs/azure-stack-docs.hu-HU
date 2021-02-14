---
title: Emelt szintű hozzáférési munkaállomás és privilegizált végponti hozzáférés
description: Tudnivalók a privilegizált hozzáférési munkaállomásról és a privilegizált végponti hozzáférésről
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b17bf72931d5e80f9cfebe6df54655856695dba2
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487986"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>Emelt szintű hozzáférési munkaállomás és privilegizált végponti hozzáférés

## <a name="overview"></a>Áttekintés

Ehhez az eljáráshoz csatlakoznia kell az emelt szintű hozzáférési munkaállomáshoz (PAW). Az ügyfélnek biztosítania kell, hogy a Távoli asztal használatával csatlakozhasson a MANCShoz.

## <a name="configuring-the-winrm"></a>A WinRM konfigurálása

Ha engedélyezni szeretné a Kiemelt végponthoz való kapcsolódást a munkaállomásról, győződjön meg arról, hogy a Azure Stack hub felügyeleti portálon meghatározott, a Kiemelt végpontok IP-címei megbízható gazdagépként vannak beállítva a MANCSon. Ezeknek az IP-címeknek a felügyeleti portálról való beszerzésére vonatkozó utasítások a méretezési egység Node-hozzáférésének és állapotának ellenőrzése a 16. oldalon.

A WinRM megbízható gazdagépek megtekintéséhez vagy szerkesztéséhez nyisson meg egy emelt szintű PowerShell-munkamenetet:

-   Megbízható gazdagépek megtekintése.

Az aktuális megbízható gazdagépek megtekintéséhez futtassa a következőt:

-   Megbízható gazdagépek szerkesztése.

Ha a helyreállítási konzol kiszolgáló-(ERCS-) IP-címei nem találhatók, a következő parancs futtatásával állítson be egy új értéket a megbízható gazdagépekhez, és cserélje le a * \< ERCS01_IP \* , * \< ERCS02_IP \* és * ERCS03_IP parancsot az \< \* Azure stack hub felügyeleti portálon meghatározott három privilegizált végponti IP-címmel:

## <a name="connect-to-the-privileged-endpoint"></a>Kapcsolódás a privilegizált végponthoz

Nyisson meg egy emelt szintű PowerShell-munkamenetet a MANCSon, és futtassa a következő két parancsot. Cserélje le \< a * ERCS_IP az \* egyik privilegizált végponti példány IP-címére, amely az eljárás korábbi részében fel van tüntetve. Amikor a rendszer kéri, adja meg az ügyfél által megadott Privileged Endpoint (PEP) hitelesítő adatokat.

## <a name="close-the-privileged-endpoint"></a>A Kiemelt végpont lezárása

Az emelt szintű végpont munkamenetének bezárásához futtassa a következőt:

## <a name="further-reading"></a>További információ

További információ a privilegizált végponthoz való csatlakozásról és azokkal való együttműködésről: [a privilegizált végpont használata Azure stack](../../operator/azure-stack-privileged-endpoint.md) 
 [központban](../../operator/azure-stack-privileged-endpoint.md).