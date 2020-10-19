---
title: GPU-beli virtuális gépek a Azure Stackon | Microsoft Docs
description: A GPU-számítástechnika referenciája Azure Stackban.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: justinha
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: c80817f7fe92916e1d83ae7bc1e83290d25e2906
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183042"
---
# <a name="gpu-vms-on-azure-stack"></a>GPU-beli virtuális gépek Azure Stack 

*A következőkre vonatkozik: Azure Stack integrált rendszerek* 

Ez a témakör bemutatja, hogyan kezelheti a GPU virtuális gépeket Azure Stack hub-on.


## <a name="partitioned-gpu-vm-size"></a>Particionált GPU virtuális gép mérete 

A NVv4 sorozatú virtuális gépeket az [AMD Radeon ösztön MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-k működtetik. A NVv4 sorozatú Azure Stack hub a részleges GPU-val rendelkező virtuális gépeket mutat be. Ez a méret GPU-gyorsított grafikus alkalmazások és virtuális asztalok esetében használható. A NVv4 Virtual Machines jelenleg csak a Windows vendég operációs rendszert támogatja. 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>A virtuális gépek javításának és frissítésének, cserélhető működésének viselkedése 

A GPU-beli virtuális gépek az olyan műveletek során, mint a patch és a Update (PnU), valamint Azure Stack hub hardveres cseréje (CSERÉLHETŐk). Az alábbi táblázat a virtuális gép állapotát tekinti át a tevékenységek során megfigyelt módon, valamint azt a manuális műveletet, amelyet a felhasználó elvégezhet, hogy a virtuális gépek újra elérhetővé tegyék ezeket a műveleteket. 

| Művelet | PnU – expressz frissítés | PnU – teljes frissítés, OEM-frissítés | FRU | 
| --- | --- | --- | --- | 
| Virtuális gép állapota  | Nem érhető el a és a frissítés után a manuális indítási művelet nélkül | Nem érhető el a frissítés során. Elérhető post Update manuális művelettel | Nem érhető el a frissítés során. Elérhető post Update manuális művelettel| 
| Manuális művelet | Ha a virtuális gépet elérhetővé kell tenni a frissítés során, ha rendelkezésre állnak GPU-partíciók, a virtuális gép újraindulhat a portálról a "újraindítás" gombra kattintva. A virtuális gépet a "restart" gomb használatával újra kell indítani a portálról a frissítés után. | A virtuális gép nem tehető elérhetővé a frissítés során. A frissítés befejezését követően a virtuális gépet le kell állítani a "Leállítás" gombbal, és a "Start" gomb használatával el kell indítani a biztonsági mentést. | A virtuális gép nem tehető elérhetővé a frissítés során. A frissítés befejezését követően a virtuális gépet le kell állítani a "Leállítás" gomb használatával, és a "Start" gomb használatával kell elindítani a biztonsági mentést.| 

## <a name="guest-driver-installation"></a>Vendég illesztőprogram telepítése 

[Ez](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) a dokumentum az AMD vendég-illesztőprogram beállítását mutatja be a NVv4 GPU-P-kompatibilis virtuális gépen, valamint az illesztőprogram telepítésének ellenőrzéséhez szükséges lépéseket. 

## <a name="next-steps"></a>Következő lépések 

[Virtuálisgép-funkciók Azure Stack](azure-stack-vm-considerations.md) 
