---
title: Az Azure CLI engedélyezése Azure Stack hub-felhasználók számára
description: Megtudhatja, hogyan engedélyezheti a többplatformos parancssori felületet (CLI) a Azure Stack hub erőforrásainak kezeléséhez és üzembe helyezéséhez.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 39cb4e97c0a73b1299df3f84ce45d7f4db820262
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544258"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>Az Azure CLI engedélyezése Azure Stack hub-felhasználók számára

Megadhatja a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt Azure Stack hub felhasználói számára, hogy az Azure CLI-t a fejlesztői számítógépükön is engedélyezzék. A felhasználóknak szükségük van a tanúsítványra az erőforrások CLI-n keresztüli kezeléséhez.

 - **A Azure stack hub hitelesítésszolgáltatói főtanúsítványának** megadása kötelező, ha a felhasználók a CLI-t használják a Azure stack Development Kit kívüli munkaállomásról (ASDK).  

 - **A virtuális gép (VM) alias végpontja** olyan aliast biztosít, mint például a "UbuntuLTS" vagy a "Win2012Datacenter", amely egy képközzétevőre, ajánlatra, SKU-ra és verzióra hivatkozik, ha virtuális gépeket helyez üzembe.  

Az alábbi szakaszok azt ismertetik, hogyan kérheti le ezeket az értékeket.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának exportálása

Ha integrált rendszer használatával dolgozik, nem kell exportálnia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt. Exportálnia kell a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt a ASDK.

A ASDK-főtanúsítvány PEM formátumban való exportálásához jelentkezzen be, és futtassa a következő szkriptet:

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

## <a name="set-up-the-vm-aliases-endpoint"></a>A VM-aliasok végpontjának beállítása

Azure Stack hub-operátoroknak olyan nyilvánosan elérhető végpontot kell beállítaniuk, amely virtuálisgép-alias-fájlt üzemeltet. A VM-alias fájl egy olyan JSON-fájl, amely a rendszerkép köznapi nevét adja meg. A nevet akkor használja, ha a virtuális gépet Azure CLI-paraméterként telepíti.  

Mielőtt hozzáad egy bejegyzést egy alias-fájlhoz, győződjön meg róla, hogy [letöltötte a lemezképeket az Azure Marketplace-ről](azure-stack-download-azure-marketplace-item.md) , vagy [közzétette saját egyéni rendszerképét](azure-stack-add-vm-image.md). Ha egyéni rendszerképet tesz közzé, jegyezze fel a közzététel során megadott kiadói, ajánlati, SKU-és verziószám-információkat. Ha ez egy rendszerkép a piactéren, megtekintheti az adatokat a `Get-AzureVMImage` parancsmag használatával.  

Elérhető egy [minta alias-fájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) , amelyben számos gyakori rendszerkép-alias található. Ezt kiindulási pontként is használhatja. Ezt a fájlt egy olyan térben tárolja, ahol a CLI-ügyfelek el tudják érni. Ennek egyik módja, hogy a fájlt egy blob Storage-fiókban tárolja, és megosztja az URL-címet a felhasználókkal:

1. Töltse le a [mintát](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a githubról.
2. Hozzon létre egy Storage-fiókot Azure Stack központban. Ha elkészült, hozzon létre egy BLOB-tárolót. Állítsa be a hozzáférési házirendet a "Public" értékre.  
3. Töltse fel a JSON-fájlt az új tárolóba. Ha elkészült, megtekintheti a blob URL-címét. Válassza ki a blob nevét, majd válassza ki az URL-címet a blob tulajdonságai között.

## <a name="next-steps"></a>Következő lépések

- [Sablonok üzembe helyezése az Azure CLI-vel](../user/azure-stack-deploy-template-command-line.md )
- [Kapcsolódás a PowerShell-lel](powershell-install-az-module.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
