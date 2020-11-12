---
title: Grafikus feldolgozó egység (GPU) virtuális gép Azure Stack hub-on
description: Az Azure Stack központban található GPU-számítástechnika referenciája.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 10/20/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: 9289b1c2ae3119a03898e2d9c361bde4976a16f7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546922"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Graphics Processing Unit (GPU) virtuális gép (VM) Azure Stack hub-on

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk azt ismerteti, hogy a grafikus processzor-(GPU-) modellek támogatottak-e egy Azure Stack hub többcsomópontos rendszeren. A GPU-k által használt illesztőprogramok telepítésével kapcsolatos utasításokat is megtalálhatja. Az Azure Stack hub GPU-támogatása olyan megoldásokat tesz lehetővé, mint például a mesterséges intelligencia, a képzés, a következtetések és az adatvizualizáció. Az AMD Radeon ösztön MI25 használható olyan grafikus igényű alkalmazások támogatásához, mint az Autodesk AutoCAD.

Három GPU-modell közül választhat a nyilvános előzetes verzióban. Ezek az NVIDIA V100, az NVIDIA T4 és az AMD MI25 GPU-k számára érhetők el. Ezek a fizikai GPU-k az alábbi Azure N sorozatú virtuális gépek (VM) típusaihoz igazodnak:
- [NCv3](/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD MI25)](/azure/virtual-machines/nvv4-series)
- [NCasT4_v3](/azure/virtual-machines/nct4-v3-series)

> [!IMPORTANT]  
> A Azure Stack hub GPU-támogatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzióban való részvételhez fejezze be az űrlapot a következő címen: [aka.MS/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview).
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ncv3"></a>NCv3

Az NCv3 sorozatú virtuális gépeket NVIDIA Tesla V100 GPU-k működtetik. Az ügyfelek igénybe vehetik a frissített GPU-ket a hagyományos HPC-számítási feladatokhoz, mint például a tározó modellezése, a DNS-szekvencia, a protein-elemzés, a Monte Carlo-szimulációk és egyebek. 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

A NVv4 sorozatú virtuális gépeket az [AMD Radeon ösztön MI25](https://www.amd.com/en/products/professional-graphics/instinct-MI25) GPU-k működtetik. A NVv4 sorozatú Azure Stack hub a részleges GPU-val rendelkező virtuális gépeket mutat be. Ez a méret GPU-gyorsított grafikus alkalmazások és virtuális asztalok esetében használható. A NVv4 Virtual Machines jelenleg csak a Windows vendég operációs rendszert támogatja. 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncast4_v3"></a>NCasT4_v3

| Méret | vCPU | Memória: GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |8 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |16 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |64 |448 | 4 | 64 | 32 | 8 |

## <a name="patch-and-update-fru-behavior-of-vms"></a>A virtuális gépek javításának és frissítésének, cserélhető működésének viselkedése 

A GPU-beli virtuális gépek az olyan műveletek során, mint a patch és a Update (PnU), valamint Azure Stack hub hardveres cseréje (CSERÉLHETŐk). Az alábbi táblázat a virtuális gép állapotát tekinti át a tevékenységek során megfigyelt módon, valamint azt a manuális műveletet, amelyet a felhasználó elvégezhet, hogy a virtuális gépek újra elérhetővé tegyék ezeket a műveleteket. 

| Művelet | PnU – expressz frissítés | PnU – teljes frissítés, OEM-frissítés | FRU | 
| --- | --- | --- | --- | 
| Virtuális gép állapota  | Nem érhető el a és a frissítés után a manuális indítási művelet nélkül | Nem érhető el a frissítés során. Elérhető post Update manuális művelettel | Nem érhető el a frissítés során. Elérhető post Update manuális művelettel| 
| Manuális művelet | Ha a virtuális gépet elérhetővé kell tenni a frissítés során, ha rendelkezésre állnak GPU-partíciók, a virtuális gép újraindulhat a portálról az **Újraindítás** gombra kattintva. A frissítés után indítsa újra a virtuális gépet a portálról az **Újraindítás** gomb használatával | A virtuális gép nem tehető elérhetővé a frissítés során. A frissítés befejezését követően a virtuális gépet le kell állítani a **Leállítás** gombbal, és a "Start" gomb használatával kell elindítani a biztonsági mentést. | A virtuális gép nem tehető elérhetővé a frissítés során. A frissítés befejezését követően a virtuális gépet le kell állítani a **Leállítás** gomb használatával, és a **Start** gomb használatával kell elindítani a biztonsági mentést.| 

## <a name="guest-driver-installation"></a>Vendég illesztőprogram telepítése

### <a name="amd-mi25"></a>AMD MI25

A [Windows rendszert futtató N sorozatú virtuális gépeken az AMD GPU-illesztőprogramok telepítése](/azure/virtual-machines/windows/n-series-amd-driver-setup) című cikk útmutatást nyújt az illesztőprogram telepítéséhez az AMD Radeon ösztön-MI25 a NVv4 GPU-P használatára képes virtuális gépen, valamint az illesztőprogramok telepítésének ellenőrzéséhez szükséges lépéseket. Ez a bővítmény csak csatlakoztatott módban működik.

### <a name="nvidia"></a>NVIDIA

Az NVIDIA-illesztőprogramokat a virtuális gépen kell telepíteni a CUDA vagy a GRID számítási feladatokhoz a GPU használatával.

#### <a name="use-case-graphicsvisualization"></a>Használati eset: grafika/vizualizáció

Ehhez a forgatókönyvhöz a GRID-illesztőprogramok használata szükséges. A GRID-illesztőprogramok az NVIDIA Application hub használatával tölthetők le, amennyiben rendelkezik a szükséges licencekkel. A GRID-illesztőprogramokhoz szükség van egy RÁCSos licenckiszolgálóra is a megfelelő hálózati licenccel, mielőtt a virtuális gépen a GRID-illesztőprogramokat kellene használnia. Ennek segítségével megtudhatja, hogyan állíthatja be a licenckiszolgálót.

#### <a name="use-case-computecuda"></a>Használati eset: számítás/CUDA

Az NVIDIA CUDA-szárítókat és a GRID-illesztőprogramokat manuálisan kell telepíteni a virtuális gépre. A Tesla CUDA-illesztőprogramok az NVIDIA [letöltési webhelyéről](https://www.nvidia.com/Download/index.aspx)szerezhetők be. A CUDA-illesztőprogramok nem igényelnek licenckiszolgálót.

## <a name="next-steps"></a>Következő lépések

- [NVIDIA GPU-illesztőprogramok telepítése a Linuxon futó N sorozatú virtuális gépeken](/azure/virtual-machines/linux/n-series-driver-setup)
- [Virtuálisgép-funkciók Azure Stack](azure-stack-vm-considerations.md)