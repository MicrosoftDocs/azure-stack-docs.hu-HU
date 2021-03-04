---
title: Install-AksHciArcOnboarding
author: jessicaguan
description: A Install-AksHciArcOnboarding PowerShell-parancs csatlakoztat egy AK-t a Azure Stack HCI munkaterhelés-fürthöz az Azure arc for Kubernetes-hez.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a366e553ac76882a7f45a9f25424cabbb4402319
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873855"
---
# <a name="install-akshciarconboarding"></a>Install-AksHciArcOnboarding

## <a name="synopsis"></a>Áttekintés
Egy AK-t csatlakoztat Azure Stack HCI munkaterhelés-fürthöz az Azure-Kubernetes.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciArcOnboarding -Name <String> 
                            -tenantId <String>
                            -subscriptionId <String> 
                            -resourceGroup <String>
                            -clientId <String>
                            -clientSecret <String>
                            [-location <String>]
```

## <a name="description"></a>Leírás
Egy AK-t csatlakoztat Azure Stack HCI munkaterhelés-fürthöz az Azure-Kubernetes.

## <a name="examples"></a>Példák

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-required-parameters"></a>A szükséges paraméterekkel rendelkező Kubernetes összekapcsolása Azure Stack HCI-fürtön az Azure-ív használatával

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-all-parameters"></a>Azure Stack HCI-fürthöz tartozó AK-ra való kapcsolódás az Azure-Kubernetes az összes paraméterrel

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -location "eastus" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

## <a name="parameters"></a>Paraméterek

### <a name="-name"></a>-Name
A Kubernetes-fürt alfanumerikus neve.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-tenantid"></a>– tenantId
Az Azure-szolgáltatásnév bérlői azonosítója.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-subscriptionid"></a>– subscriptionId
Az Azure-fiók előfizetés-azonosítója.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-resourcegroup"></a>– resourceGroup
Az Azure-erőforráscsoport neve

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clientid"></a>– clientId
Az Azure-szolgáltatás ügyfél-azonosítója vagy alkalmazás-azonosítója

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clientsecret"></a>-clientSecret
Az Azure-szolgáltatásnév ügyfél-titka vagy jelszava

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-location"></a>– hely
Az Azure-erőforrás helye vagy Azure-régiója. Az alapértelmezett érték az Azure-erőforráscsoport helye. A hely csak eastus vagy westeurope lehet az Azure arc for Kubernetes esetében.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Azure resource group's location
Accept pipeline input: False
Accept wildcard characters: False
```
