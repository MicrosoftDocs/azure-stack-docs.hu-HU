---
title: Azure CLI-vel engedélyezése az Azure Stack felhasználói |} A Microsoft Docs
description: Az Azure Stacken erőforrásokat üzembe helyezheti és kezelheti a többplatformos parancssori felület (CLI) használata
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: ace99053d9aac4c525e9481e5430ac1f5648f194
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782319"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure CLI-vel engedélyezése az Azure Stack-felhasználók számára

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A legfelső szintű hitelesítésszolgáltató tanúsítványát biztosíthat az Azure Stack felhasználói számára, így az Azure CLI-vel a fejlesztői gépen is használni. A felhasználók a tanúsítványt a CLI-n keresztül-erőforrások kezeléséhez szükséges.

 - **Az Azure Stack hitelesítésszolgáltató főtanúsítványát** van szükség, ha a felhasználók parancssori felület használata az Azure Stack Development Kit kívül munkaállomásokon.  

 - **A virtuális gép aliasok végpontjának** alias, mint a "UbuntuLTS" vagy "Win2012Datacenter," által hivatkozott egy rendszerkép közzétevője, ajánlat, Termékváltozat és verzió egyetlen paramétert, ha a virtuális gépek üzembe helyezéséhez biztosít.  

A következő szakaszok ismertetik ezeket az értékeket beszerzése.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató legfelső szintű tanúsítványának exportálása

Ha egy integrált rendszer használ, nem kell a legfelső szintű hitelesítésszolgáltató tanúsítványának exportálása. Exportálja a hitelesítésszolgáltató főtanúsítványát a egy Azure Stack Development Kit (ASDK) kell.

PEM-formátumú ASDK főtanúsítvány exportálása, jelentkezzen be, és futtassa a következő szkriptet:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának beállítása

Az Azure Stack-operátorok egy nyilvánosan elérhető végponton, amelyen egy virtuális gép alias fájlt kell beállítania. A virtuális gép alias fájl nem egy JSON-fájl által biztosított rendszerképek köznapi neve. A nevet fogja használni, ha telepít egy virtuális Gépet az Azure CLI-vel paraméterként.  

Alias fájlt hozzá egy bejegyzést, előtt győződjön meg arról, hogy Ön [töltse le a rendszerképeket az Azure Marketplace-ről](azure-stack-download-azure-marketplace-item.md) vagy [közzé saját egyéni rendszerképét](azure-stack-add-vm-image.md). Ha egy egyéni rendszerkép közzététele, jegyezze fel a közzétevő, ajánlat, Termékváltozat és verzió közzététele során megadott információk. Ha egy lemezképet a marketplace webhelyről, az információk használatával megtekintheti a `Get-AzureVMImage` parancsmagot.  

A [alias mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) számos gyakori rendszerképpel aliasok érhető el. Kiindulási pontként használhatja azt. A gazdagép egy helyet, ahol a CLI-ügyfelek számára is elérhetővé válik a fájlhoz. Egyik módja a fájlt a blob storage-fiókot üzemeltethet, és az URL-cím megosztása a felhasználókkal:

1. Töltse le a [mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a Githubról.
2. Hozzon létre egy tárfiókot az Azure Stackben. Ha ez megtörtént, hozzon létre egy blobtárolót. A hozzáférési szabályzat a "nyilvános".  
3. Töltse fel az új tároló a JSON-fájlt. Ha ez megtörtént, megtekintheti a blob URL-CÍMÉT. Válassza ki a blob nevét, és válassza az URL-címet a blob tulajdonságai közül.

## <a name="next-steps"></a>További lépések

- [Az Azure CLI-vel sablonok üzembe helyezése](../user/azure-stack-deploy-template-command-line.md )
- [Kapcsolódás a PowerShell-lel](azure-stack-powershell-install.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
