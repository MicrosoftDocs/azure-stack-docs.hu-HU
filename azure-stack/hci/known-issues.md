---
title: Azure Stack HCI ismert problémái
description: Ez a témakör a Azure Stack HCI ismert problémáit részletezi.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.openlocfilehash: 21e56fbb34c89446b0dd0e395046a9c851f391dc
ms.sourcegitcommit: f2d16c3148da50d679940e024267995b85ce6332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91407752"
---
# <a name="known-issues-for-azure-stack-hci"></a>Azure Stack HCI ismert problémái

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör a Azure Stack HCI ismert problémáit részletezi

- A helyszíni fürt és a Azure Stack HCI Cloud Service közötti kapcsolat még nem támogatja az [Azure privát hivatkozását](https://azure.microsoft.com/services/private-link).
- A Azure Stack HCI Cloud Service jelenleg csak a Select régiókban érhető el.
- A Azure Stack HCI 2020. július 21-én jelent meg. Több napot is igénybe vehet, hogy Azure Portal felhasználói felületének bővítménye világszerte láthatóvá váljon. Ebben az időszakban előfordulhat, hogy a fürt a Azure Portal alapértelmezett erőforrás-lapjaként jelenik meg. Köszönjük türelmét.
- Ha interaktív módon jelentkezik be az operációs rendszerbe, a "Welcome to Azure Stack HCI" nevű üdvözlő képernyő még nem lett honosítva a Cseh, a magyar, a Nederland, a lengyel, a svéd és a török nyelveken (területi beállítás cs-cz, hu-hu, nl-nl, pl-pl, PT-PT, SV-se, TR-TR). Emellett az angoltól különböző nyelveken a bemeneti kérések (például "[Y] es/[N] o") csak az "Y" és az "N" értékeket fogadják el, még akkor is, ha a kérés félrevezető állapotba kerül, például: [O] UI/[N] a franciában vagy [J] a/[N] Ein in German.
- Ha a Azure Stack a HCI-t beágyazott virtualizálás használatával értékeli ki, akkor előfordulhat, hogy a "Hyper-V nem telepíthető, mert a virtualizációs támogatás nem engedélyezett", mert Azure Stack HCI-függőség a virtualizált alapú biztonság miatt. Két lehetséges Áthidaló megoldás létezik: (1) a Hyper-V 1. generációs virtuális gépek használata helyett; vagy (2) adja a Hyper-V szolgáltatást a virtuális gép VHDX offline állapotba. A gazdagépen futtassa a következő PowerShell-parancsot minden olyan virtuális gépen, amely Azure Stack HCI-csomópontként fog működni, amíg ki van kapcsolva: **install-WindowsFeature-VHD \<path> -Name Hyper-v, RSAT-Hyper-v-Tools, Hyper-v-PowerShell**.
