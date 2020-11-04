---
title: Azure Stack Windows Serverrel kapcsolatos gyakori kérdések | Microsoft Docs
description: A Windows Serverhez készült Azure Stack Marketplace – gyakori kérdések listája
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 3570eeb9edadd359081c429f821a09e3d9954439
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329116"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server Azure Stack piactéren – gyakori kérdések

Ez a cikk az [Azure stack hub piactéren](../../operator/azure-stack-marketplace.md)elérhető Windows Server-rendszerképekkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="faqs"></a>Gyakori kérdések

**Mik a Windows Server Marketplace rendszerképeinek licencelési lehetőségei a Azure Stack-on?**

A Azure Stack hub robusztus és MDC felhasználói a Windows Server ingyenes használatát használják vendég operációs rendszerként.

A Microsoft a Windows Server rendszerképeinek két verzióját kínálja a Azure Stack piactéren. A rendszerképnek csak egy verziója használható egyszerre egy adott Azure Stack környezetben.

- **Fizetés a használat** során: ezeket a rendszerképeket nem szabad használni a Azure stack hub ROBUSZTUS vagy MDC.

- **Saját licenc használata (BYOL)** : ezek a rendszerképek használhatók Azure stack hub ROBUSZTUS és MDC.

**Mi a helyzet a Windows Servert használó más virtuális gépekkel, például a SQL Server?**

A Windows Server szoftverlicenc csak a Windows operációs rendszerre érvényes, nem pedig a rétegzett termékekhez, például az SQL-hez, amelyekhez saját licenc szükséges.

**Hogyan frissíteni egy újabb Windows-rendszerképre?**

Először határozza meg, hogy a Azure Resource Manager-sablonok adott verzióra vonatkoznak-e. Ha igen, frissítse ezeket a sablonokat, vagy tartsa meg a régebbi rendszerkép-verziókat. A legjobb a következő **verzió használata: legújabb**.

Ezután, ha bármelyik virtuálisgép-méretezési csoport egy adott verzióra hivatkozik, gondolja át, hogy a méretezés később történik-e, és döntse el, hogy megtartja-e a régebbi verziókat. Ha egyik feltétel sem érvényes, törölje a régebbi lemezképeket a piactéren az újabb verziók letöltése előtt. A piactér-kezelés használatával törölje azokat, ha az eredeti Letöltés módja. Ezután töltse le az újabb verziót.

**Mi a teendő, ha nem megfelelő verziót Letöltöttem a felhasználók számára?**

Először törölje a helytelen verziót a piactér kezelése szolgáltatásban. Várjon, amíg befejeződik (tekintse meg az értesítéseket, és ne a **piactér-kezelő** panelt). Ezután töltse le a megfelelő verziót.

Ha a lemezkép mindkét verzióját letölti, a piactér-katalógusban csak a legújabb verzió látható a végfelhasználók számára.

**Hogyan kapcsolhatom be a Windows Server rendszerű virtuális gépet?**

A Windows Server rendszerű virtuális gépek Azure Stackon való aktiválásához a következő feltételnek igaznak kell lennie:

- A Windows Server 2012 R2 és a Windows Server 2016 rendszernek a [virtuális gépek automatikus aktiválását](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))kell használnia. A kulcskezelő szolgáltatás (KMS) és más aktiválási szolgáltatások nem támogatottak Azure Stackon.

**Hogyan lehet ellenőrizni, hogy a virtuális gép aktiválva van-e?**

Futtassa a következő parancssort egy rendszergazda jogú parancssorból:

```shell
slmgr /dlv
```

Ha helyesen van aktiválva, a rendszer ezt világosan jelezte, és az állomásnév kimenetében megjelenő állomásnevet fogja látni. Nem függnek a kijelzőn látható vízjelektől, mivel előfordulhat, hogy nem naprakészek, vagy egy másik, a tiéd mögötti virtuális gépről mutatnak.

**A virtuális gép nincs beállítva a AVMA használatára, Hogyan javíthatom?**

Futtassa a következő parancssort egy rendszergazda jogú parancssorból:

```shell
slmgr /ipk <AVMA key>
```

A rendszerképhez használandó kulcsok [automatikus virtuálisgép-aktiválásáról](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) szóló cikkben talál.

**Létrehozom a saját Windows Server-lemezképeket, Hogyan biztosíthatom, hogy a AVMA használják?**

Javasoljuk, hogy a parancs `slmgr /ipk` futtatása előtt futtassa a parancsot a megfelelő kulccsal `sysprep` . Vagy adja meg a AVMA kulcsot bármilyen Unattend.exe telepítési fájlban.

**Megpróbálom használni az Azure-ban létrehozott Windows Server 2016-es rendszerképet, és nem aktiválja vagy nem használ KMS-aktiválást.**

Futtassa a következő parancsot: `slmgr /ipk`. Előfordulhat, hogy az Azure-lemezképek nem fognak megfelelően visszaesni a AVMA, de ha elérik az Azure KMS-rendszerét, aktiválva lesznek. Javasoljuk, hogy a virtuális gépek AVMA használatára legyenek beállítva.

**Elvégeztem ezeket a lépéseket, de a virtuális gépek még nem aktiválva vannak.**

Forduljon a Microsoft támogatási szolgálatához, és ellenőrizze, hogy a megfelelő BIOS-jelölők telepítve vannak-e.

**Mi a helyzet a Windows Server korábbi verzióival?**

Az [automatikus virtuálisgép-aktiválás](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) nem támogatott a Windows Server 2012 R2-nél korábbi verzióiban. A virtuális gépeket a MAK kulcsok használatával manuálisan kell aktiválni.

## <a name="next-steps"></a>Következő lépések

További információkat az következő cikkekben talál:

- [Azure Stack Marketplace – áttekintés](../../operator/azure-stack-marketplace.md)
- [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item-tca.md)
