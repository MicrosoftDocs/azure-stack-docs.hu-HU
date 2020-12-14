---
title: Emelt szintű hozzáférési munkaállomás és privilegizált végponti hozzáférés
description: Tudnivalók a privilegizált hozzáférési munkaállomásról és a privilegizált végponti hozzáférésről
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a186ef96c0ce35457fd9871a97ef4b5533813933
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391024"
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

További információ a privilegizált végponthoz való csatlakozásról és azokkal való együttműködésről: [a privilegizált végpont használata Azure stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) 
 [központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
