---
title: CustomAdfs rendszerjogosultságú végpontjának regisztrálása Azure Stack hubhoz
description: Hivatkozás a PowerShell Azure Stack privilegizált végpont-Register-CustomAdfs
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 743b8f5d8f1a2a6cb9f6e44fc61510a5da394ef6
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486563"
---
# <a name="register-customadfs"></a>Regisztráció – CustomAdfs

## <a name="synopsis"></a>Áttekintés
Parancsfájl az egyéni Active Directory összevonási szolgáltatás (AD FS) regisztrálásához Azure Stack AD FS

## <a name="syntax"></a>Szintaxis

```
Register-CustomAdfs [-CustomADFSFederationMetadataEndpointUri <Object>]
 [-CustomADFSFederationMetadataFileContent <Object>] [-TimeoutInSecs <Object>] [-CustomAdfsName <Object>]
 [-SigningCertificateRevocationCheck <Object>] [-EncryptionCertificateRevocationCheck <Object>] [-AsJob]
```

## <a name="description"></a>Leírás
Parancsfájl az egyéni Active Directory összevonási szolgáltatás (AD FS) regisztrálásához Azure Stack AD FS

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataEndpointUri "https://adfs.contoso.com/federationmetadata/2007-06/federationmetadata.xml"  -TimeoutInSecs 1000
```

### <a name="example-2"></a>2\. példa
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataFile "c:\temp\FederationMetadata.xml" -TimeoutInSecs 1000
```
## <a name="parameters"></a>Paraméterek

### <a name="-customadfsname"></a>-CustomAdfsName
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-customadfsfederationmetadataendpointuri"></a>-CustomADFSFederationMetadataEndpointUri
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-customadfsfederationmetadatafilecontent"></a>-CustomADFSFederationMetadataFileContent
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-encryptioncertificaterevocationcheck"></a>-EncryptionCertificateRevocationCheck
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-signingcertificaterevocationcheck"></a>-SigningCertificateRevocationCheck
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: CheckChainExcludeRoot
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>– AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Következő lépések

További információ a privilegizált végpont eléréséről és használatáról: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).
