---
title: Az Azure Stack érvényesítési szolgáltatás hibaelhárítása |} A Microsoft Docs
description: Hárítsa el a szolgáltatást biztosít az Azure Stack érvényesítési.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fedfd7f83a35398586734fa647751e537b850bf8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297820"
---
# <a name="troubleshoot-validation-as-a-service"></a>Érvényesítési szolgáltatás hibaelhárítása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az alábbiakban gyakori problémák elhárítása és a megoldásaikat, egymástól független.

## <a name="local-agent"></a>Helyi ügynök

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>A portál megjeleníti a helyi ügynök hibakeresési módban

Ennek oka valószínűleg az ügynök nem tud szívverést küldeni a szolgáltatás egy nem stabil hálózati kapcsolat miatt nem sikerült. Szívverés küldi át 5 percenként. Ha a szolgáltatás nem kap szívverés 15 percig, a szolgáltatás az ügynök inaktívnak tekinti, és vizsgálatokat, már nem lesz ütemezve. A hibaüzenetben a *Agenthost.log* fájl a könyvtárban található, az ügynök elindítása.

> [!Note]
> Már fut az ügynökön lévő bármely tesztet is tovább fut, a teszt vége előtt visszaállítva, de ha a szívverés nem, akkor az ügynök nem fogja tudni a vizsgálat állapotának frissítése vagy naplók feltöltése. A teszt mindig jelennek meg **futó** és kell szakítható meg.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Az ügynök folyamata a gép le lett állítva, teszt végrehajtása közben. Mi várható?

Az ügynök folyamata leállt ungracefully például, ha a gép újraindul, leállított folyamat (CTRL + C billentyűkombinációt az ügynök ablakban tekinthető szabályos leállítást) a rajta futó vizsgálat folytatódik állapotúként **futó**. Ha az ügynök újraindítása után, akkor az ügynök frissül, hogy a teszt állapota **meg lett szakítva**. Ha nem indítja újra az ügynököt, akkor jelenik meg a teszt **futó** és manuálisan törölnie kell a tesztet.

> [!Note]
> Ütemezett vizsgálatok egy munkafolyamaton belül egymás után futnak. **Függőben lévő** tesztek nem lekérése amíg a tesztek végrehajtása a **futó** állapot teljes ugyanabban a munkafolyamatban.

## <a name="vm-images"></a>VM-lemezképek

### <a name="handle-slow-network-connectivity"></a>Lassú hálózati kapcsolat kezeléséhez

Letöltheti a PIR kép megosztásra a helyi adatközpontban. És a lemezkép ellenőrizze.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Töltse le a PIR rendszerkép helyi megosztás esetén a lassú hálózati forgalom

1. Töltse le az AzCopy: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. AzCopy.zip csomagolja ki, és arra a könyvtárra, amelyben AzCopy.exe módosítása

3. Nyissa meg a Windows Powershellt egy rendszergazda jogú parancssorból. Futtassa az alábbi parancsot:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare, a megosztás elérési útját vagy a helyi elérési útja.

#### <a name="verifying-pir-image-file-hash-value"></a>PIR lemezkép fájl kivonata érték ellenőrzés alatt áll

Használhat **Get-HashFile** parancsmaggal a kivonatolt érték beolvasható a letöltött nyilvános lemezképtárban képfájlok, a képek sértetlenségének ellenőrzéséhez.

| Fájlnév | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Hiba történik, amikor Virtuálisgép-Rendszerkép feltöltése a `VaaSPreReq` parancsfájl

Először ellenőrizze, hogy a környezet nem megfelelő állapotú:

1. A DVM a / jump-box, akkor ellenőrizze, hogy sikeresen bejelentkezhet a felügyeleti portál, a rendszergazdai hitelesítő adataival.
1. Győződjön meg arról, hogy nincsenek-e riasztások vagy figyelmeztetések.

Ha a környezet megfelelő, manuális VaaS tesztelési célú futtatások szükséges 5 Virtuálisgép-rendszerképek feltöltése:

1. Jelentkezzen be a felügyeleti portál szolgáltatás-rendszergazdaként. A felügyeleti portálon található Dokumentumokat tároló vagy a blokk-információs fájl URL-CÍMÉT. Útmutatásért lásd: [környezet paraméterek](azure-stack-vaas-parameters.md#environment-parameters).
1. Válassza ki **további szolgáltatások** > **erőforrás-szolgáltatók** > **számítási** > **Virtuálisgép-rendszerképek**.
1. Válassza ki a **+ Hozzáadás** gombot a felső részén a **Virtuálisgép-rendszerképek** panelen.
1. Módosíthatja, vagy ellenőrizze az első virtuális gép lemezképének a következő mezők értékeit:
    > [!IMPORTANT]
    > Nem minden alapértelmezett megfelelőek-e a meglévő Piactéri elemet.

    | Mező  | Érték  |
    |---------|---------|
    | Közzétevő | MicrosoftWindowsServer |
    | Ajánlat | WindowsServer |
    | Operációs rendszer típusa | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | Operációsrendszer-lemez Blob URI-t | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Válassza a **Létrehozás** gombot.
1. Ismételje meg a többi VM-rendszerképek esetében.

5 Virtuálisgép-rendszerképek tulajdonságai a következők:

| Közzétevő  | Ajánlat  | Operációs rendszer típusa | SKU | Version | Operációsrendszer-lemez Blob URI-t |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [kibocsátási megjegyzései szolgáltatásként érvényesítési](azure-stack-vaas-release-notes.md) változásairól a legújabb kiadásaihoz.