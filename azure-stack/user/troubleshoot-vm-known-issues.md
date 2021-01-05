---
title: Azure Stack hub ismert problémáinak elhárítása
description: Ismerje meg, hogyan lehet elhárítani a virtuális gépek ismert problémáit Azure Stack hub-on
author: mattbriggs
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: d752b31ac5076255ae4368904145a50d5a3c09ff
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867162"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>Ismert problémák: Azure Stack hub-beli virtuális gépek

Az Azure Stack hub számítási erőforrás-szolgáltatójának hibaelhárításával kapcsolatos ismert problémák az ebben a cikkben található virtuális gépek (VM) s és méretezési csoportok használatakor találhatók.

## <a name="portal-doesnt-show-correct-vm-name"></a>A portál nem jeleníti meg a helyes virtuális gép nevét
- **Alkalmazható**  
    Ez a probléma minden kiadásra vonatkozik.  
- **Ok**  
    Amikor egy virtuális gép adatait tekinti meg az Áttekintés panelen, a számítógép neve (nem érhető el) jelenik meg. A Megjelenítés a speciális lemezekről/lemez-pillanatképekről létrehozott virtuális gépek kialakításán alapul.  
- **Szervizelés**  
    A portálon válassza a **Beállítások**  >  **Tulajdonságok** lehetőséget.
- **Előfordulása**  
    Közös  

## <a name="nvv4-vm-size-on-portal"></a>NVv4 VM-méret a portálon
- **Alkalmazható**  
    Ez a probléma a Azure Stack hub 2002-es és újabb kiadására vonatkozik.  
- **Ok**  
    Ha a virtuális gép létrehozási élményét választja, megjelenik a virtuális gép mérete: NV4as_v4. Azok az ügyfelek, akik rendelkeznek az AMD MI25-alapú Azure Stack hub GPU előzetes verziójához szükséges hardverrel, sikeresen üzembe helyezhetik a virtuális gépeket. Az összes többi ügyfélnél a virtuálisgép-mérettel rendelkező virtuális gép üzembe helyezése sikertelen lesz.  
- **Szervizelés**  
    Nincsenek.  
- **Előfordulása**  
    Közös  

## <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika
- **Alkalmazható**  
    Ez a probléma az összes támogatott kiadásra vonatkozik.  
- **Ok**  
    Új virtuális gép (VM) létrehozásakor a következő hiba jelenhet meg: nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.  
- **Szervizelés**  
    Hozza létre újra a Storage-fiókot ugyanazzal a névvel, amelyet korábban használt.
- **Előfordulása**  
    Közös  

## <a name="vm-diagnostics-storage-account-not-found"></a>A VM Diagnostics Storage-fiók nem található
- **Alkalmazható**  
    Ez a probléma az összes támogatott kiadásra vonatkozik.  
- **Ok**  
    Ha egy leállított virtuális gépet próbál elindítani, a következő hibaüzenet jelenhet meg: a VM Diagnostics Storage-fiókja (diagnosticstorageaccount) nem található. Győződjön meg arról, hogy a Storage-fiók nincs törölve. A hiba akkor fordul elő, ha olyan virtuális gépet próbál elindítani, amelyen engedélyezve van a rendszerindítási diagnosztika, de a rendszer törli a hivatkozott rendszerindítási diagnosztika Storage-fiókját.  
- **Szervizelés**  
    Hozza létre újra a Storage-fiókot ugyanazzal a névvel, amelyet korábban használt.  
- **Előfordulás** Közös  

## <a name="consumed-compute-quota"></a>Felhasznált számítási kvóta
- **Alkalmazható**  
    Ez a probléma az összes támogatott kiadásra vonatkozik.  
- **Okozhat**   
    Új virtuális gép létrehozásakor előfordulhat, hogy ez az előfizetés az ezen a helyen lévő összes regionális vCPU kapacitását kapja. Ez az előfizetés a rendelkezésre álló összes 50 teljes regionális vCPU használja. Ez azt jelzi, hogy elérte az összes elérhető magok kvótáját.  
- **Szervizelés**  
    További kvótával megkérheti az operátort egy kiegészítő csomagra. Az aktuális csomag kvótájának módosítása nem fog működni, és nem tükrözi a megnövekedett kvótát.
- **Előfordulása**  
    Ritka  

## <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

-  **Alkalmazható**  
    Ez a probléma az összes támogatott kiadásra vonatkozik.  
- **Ok**  
    Hibák létrehozása a javítás és a frissítés során négy csomópontos Azure Stack hub környezetekben. A virtuális gépeket három tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozásakor a rendszer FabricVmPlacementErrorUnsupportedFaultDomainSize hibával hajtja végre egy négy csomópontos Azure Stack hub-környezet frissítési folyamata során.  
- **Szervizelés**  
    Létrehozhat egyetlen virtuális gépet egy rendelkezésre állási csoportba két tartalék tartománnyal. A méretezési csoport példányainak létrehozása azonban még nem érhető el a frissítési folyamat során egy négy csomópontos Azure Stack hub üzemelő példányon.  
- **Előfordulása**  
    Ritka  

## <a name="next-steps"></a>További lépések

További információ a [Azure stack hub virtuális gépek funkcióival](azure-stack-vm-considerations.md)kapcsolatban.
