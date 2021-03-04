---
title: AksHci PowerShell-modul
description: Ismerje meg, hogyan kezelheti a AksHci modul parancsait az AK Azure Stack HCI-vel való kezeléséhez
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 8d6b3410fb6fe6a00d602eeda97e172f1bac4062
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873815"
---
# <a name="akshci"></a>AksHci 

Parancsok az Azure Kubernetes szolgáltatással való interakcióhoz Azure Stack HCI-on.

## <a name="akshci-cmdlets"></a>AksHci-parancsmagok

|         |            |
| ------- | ---------- |
| [Get-akshcicluster](get-akshcicluster.md) | Sorolja fel a telepített fürtöket, beleértve az Azure Kubernetes Service hostt is. |
| [Get-akshciclusterupgrades](get-akshciclusterupgrades.md) | Az Azure Kubernetes Service-fürt elérhető frissítéseinek beszerzése. |
| [Get-akshciconfig](get-akshciconfig.md) | Az Azure Kubernetes Service Host aktuális konfigurációs beállításainak listázása. |
| [Get-akshcicredential](get-akshcicredential.md) | A fürt elérése a kubectl használatával. |
| [Get-akshcieventlog](get-akshcieventlog.md) | Lekéri az összes eseménynaplót az AK HCI PowerShell-modulból. |
| [Get-akshcikubernetesversion](get-akshcikubernetesversion.md) | A felügyelt Kubernetes-fürt létrehozásához elérhető verzió listázása. |
| [Get-akshcilogs](get-akshcilogs.md) | Hozzon létre egy tömörített mappát az összes hüvelyből származó naplókkal. |
| [Get-akshciupdates](get-akshciupdates.md) | Az Azure Kubernetes szolgáltatás elérhető frissítéseinek listázása Azure Stack HCI-on. |
| [Get-akshciversion](get-akshciversion.md) | Szerezze be az Azure Kubernetes Service aktuális verzióját Azure Stack HCI-on. |
| [Get-akshcivmsize](get-akshcivmsize.md) | A virtuális gépek támogatott méretének listázása. |
| [inicializálás – akshcinode](initialize-akshcinode.md) | Futtasson ellenőrzéseket minden fizikai csomóponton, és ellenőrizze, hogy az összes követelmény teljesül-e az Azure Kubernetes Service Azure Stack HCI-on való telepítéséhez. |
| [install-akshci](install-akshci.md) | Telepítse az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökre/-szolgáltatásokra és-gazdagépre. |
| [install-akshciadauth](install-akshciadauth.md) | Active Directory hitelesítés telepítése. |
| [install-akshciarconboarding](install-akshciarconboarding.md) | Töltse le és telepítse a kubectl, a Kubernetes parancssori eszközt. |
| [új – akshcicluster](new-akshcicluster.md) | Hozzon létre egy új felügyelt Kubernetes-fürtöt. |
| [új – akshcinetworksetting](new-akshcinetworksetting.md) | Hozzon létre egy objektumot egy új virtuális hálózat számára. |
| [Remove-akshcicluster](remove-akshcicluster.md) | Felügyelt Kubernetes-fürt törlése. |
| [újraindítás – akshci](restart-akshci.md) | Indítsa újra az Azure Kubernetes szolgáltatást a Azure Stack HCI-ben, és távolítsa el az összes telepített Kubernetes-fürtöt. |
| [set-akshciclusternodecount](set-akshciclusternodecount.md) | A vezérlő sík csomópontjainak vagy a munkavégző csomópontok számának méretezése a fürtben. |
| [set-akshciconfig](Sset-akshciconfig.md) | Az Azure Kubernetes Service Host konfigurációs beállításainak megadása vagy frissítése. |
| [eltávolítás – akshci](uninstall-akshci.md) | Távolítsa el az Azure Kubernetes szolgáltatást Azure Stack HCI-on. |
| [eltávolítás – akshciadauth](uninstall-akshciadauth.md) | Active Directory hitelesítés eltávolítása. |
| [frissítés – akshci](update-akshci.md) | Frissítse az Azure Kubernetes Service hostt a legújabb Kubernetes-verzióra. |
| [frissítés – akshcicluster](update-akshcicluster.md) | Felügyelt Kubernetes-fürt frissítése újabb Kubernetes vagy operációsrendszer-verzióra. |

