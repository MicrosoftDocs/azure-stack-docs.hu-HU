---
title: Azure Stack HCI ismert problémái
description: Ez a témakör a Azure Stack HCI ismert problémáit részletezi.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688320"
---
# <a name="known-issues-for-azure-stack-hci"></a>Azure Stack HCI ismert problémái

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör a Azure Stack HCI ismert problémáit részletezi

- Az október 20., 2020-os előzetes frissítés (KB4580388) miatt előfordulhat, hogy a fürt Aware frissítési (CAU) művelete meghiúsul, ha a virtuális gépek (VM-EK) elvégzése a CAU során várhatóan Élő áttelepítés. Ennek elkerüléséhez módosítsa az alapértelmezett viselkedést átmenetileg, hogy a CAU Élő áttelepítés helyett gyors áttelepítést használjon. További információért tekintse meg a [kibocsátási megjegyzéseket](release-notes.md#october-20-2020-preview-update-kb4580388) .
- A helyszíni fürt és a Azure Stack HCI Cloud Service közötti kapcsolat még nem támogatja az [Azure privát hivatkozását](https://azure.microsoft.com/services/private-link).
- A Azure Stack HCI Cloud Service jelenleg csak a Select régiókban érhető el.
- Ha interaktív módon jelentkezik be az operációs rendszerbe, a "Welcome to Azure Stack HCI" nevű üdvözlő képernyő még nem lett honosítva a Cseh, a magyar, a Nederland, a lengyel, a svéd és a török nyelveken (területi beállítás cs-cz, hu-hu, nl-nl, pl-pl, PT-PT, SV-se, TR-TR). Emellett az angoltól különböző nyelveken a bemeneti kérések (például "[Y] es/[N] o") csak az "Y" és az "N" értékeket fogadják el, még akkor is, ha a kérés félrevezető állapotba kerül, például: [O] UI/[N] a franciában vagy [J] a/[N] Ein in German.
- Ha a Azure Stack a HCI-t beágyazott virtualizálás használatával értékeli ki, akkor előfordulhat, hogy a "Hyper-V nem telepíthető, mert a virtualizációs támogatás nem engedélyezett", mert Azure Stack HCI-függőség a virtualizált alapú biztonság miatt. Két lehetséges Áthidaló megoldás létezik: (1) Ehelyett használja a Hyper-V 1. generációs virtuális gépeket; vagy (2) adja a Hyper-V szolgáltatást a virtuális gép VHDX offline állapotba. A gazdagépen futtassa a következő PowerShell-parancsot minden olyan virtuális gépen, amely Azure Stack HCI-csomópontként fog működni, amíg ki van kapcsolva: **install-WindowsFeature-VHD \<path> -Name Hyper-v, RSAT-Hyper-v-Tools, Hyper-v-PowerShell** .
