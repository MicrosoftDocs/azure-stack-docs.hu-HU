---
title: A szolgáltatás érvényesítésének megoldása
titleSuffix: Azure Stack Hub
description: Azure Stack hub-szolgáltatás érvényesítésének hibája.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f9a3b4b3ac61447a3dca567ad9ebcb636c8f9bc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874625"
---
# <a name="troubleshoot-validation-as-a-service"></a>A szolgáltatás érvényesítésének megoldása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A következő gyakori problémák a szoftveres kiadásokkal és azok megoldásaival kapcsolatosak.

## <a name="local-agent"></a>Helyi ügynök

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>A portál megjeleníti a helyi ügynököt hibakeresési módban

Ez a probléma valószínűleg azért valószínű, mert az ügynök nem tud szívveréseket küldeni a szolgáltatásnak instabil hálózati kapcsolatok miatt. A szívverést öt percenként küldik el. Ha a szolgáltatás 15 percen belül nem kap szívverést, akkor a szolgáltatás úgy tekinti, hogy az ügynök inaktív, és a tesztek már nem lesznek ütemezve. Keresse meg a *Agenthost. log* fájlban található hibaüzenetet abban a könyvtárban, ahol az ügynököt elindították.

> [!Note]
> Az ügynökön már futó tesztek továbbra is futni fognak, de ha a szívverés nem állítható vissza a teszt befejezése előtt, az ügynök nem fogja tudni frissíteni a teszt állapotát, vagy feltölti a naplókat. A teszt mindig **fut** állapotba kerül, és meg kell szüntetni.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>A teszt végrehajtása közben leállt az ügynök folyamata a gépen. Mi várható?

Ha az ügynök folyamata nem megfelelően van leállítva, akkor a rajta futó teszt továbbra is **futként** fog megjelenni. Példa egy nem megfelelő leállításra: a számítógép újraindult, és a folyamat megszakadt (a CTRL + C az ügynök ablakában kecses leállításnak számít). Ha az ügynök újraindul, az ügynök a teszt állapotát **megszakítva** fogja frissíteni. Ha nem indítja újra az ügynököt, a teszt **fut** állapottal jelenik meg, és manuálisan kell megszüntetnie a tesztet.

> [!Note]
> A munkafolyamaton belüli tesztek futtatása ütemezett sorrendben történik. A **függőben lévő** tesztek nem lesznek végrehajtva, amíg a tesztek nem **futnak** ugyanabban a munkafolyamatban.

## <a name="vm-images"></a>VM-lemezképek

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Hiba történik a virtuálisgép-rendszerkép parancsfájlban való feltöltésekor `VaaSPreReq`
Tekintse meg az alábbi szakaszt a **lassú hálózati kapcsolat kezeléséhez**. Manuális lépéseket biztosít a virtuálisgép-lemezképek Azure Stack stampre való feltöltéséhez.

### <a name="handle-slow-network-connectivity"></a>Lassú hálózati kapcsolat kezelése

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. Ellenőrizze, hogy a környezet kifogástalan állapotú-e

1. A DVM/Jump (ugrás) mezőben győződjön meg róla, hogy sikeresen bejelentkezhet a felügyeleti portálra a rendszergazdai hitelesítő adatok használatával.

2. Győződjön meg arról, hogy nincsenek riasztások vagy figyelmeztetések.

3. Ha a környezet kifogástalan állapotban van, az alábbi szakasz lépéseinek megfelelően töltse fel manuálisan a szükséges virtuálisgép-lemezképeket.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. a PIR-rendszerkép letöltése a helyi megosztásra lassú hálózati forgalom esetén

1. Töltse le a AzCopy a következő címről: [vaasexternaldependencies (AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Bontsa ki a AzCopy.zip és váltson át a-t tartalmazó könyvtárba `AzCopy.exe` .

3. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorból. Futtassa az alábbi parancsot:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> A LocalFileShare a megosztás elérési útja vagy a helyi elérési út.

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. a PIR képfájl kivonatoló értékének ellenőrzése

A **Get-HashFile** parancsmag használatával lekérheti a letöltött nyilvános rendszerkép-tárházhoz tartozó képfájlok kivonatának értékét a rendszerképek integritásának ellenőrzése érdekében.

| Fájlnév | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL. vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL. vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL. vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS. vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1. vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105. vhd | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest. vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. virtuálisgép-lemezképek feltöltése egy Storage-fiókba

1. Használjon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot az Azure-ban.

2. Hozzon létre egy tárolót a lemezképek feltöltéséhez.

3. A Azcopy eszközzel töltse fel a virtuálisgép-lemezképeket a fenti [*LocalFileShare*] fájlból (ahol letöltötte a virtuálisgép-lemezképeket) az imént létrehozott tárolóba.
    > [!IMPORTANT]
    > A tároló "nyilvános hozzáférési szintjének" módosítása a Blobra (névtelen olvasási hozzáférés csak blobokhoz)

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. virtuálisgép-rendszerképek feltöltése Azure Stack környezetbe

1. Jelentkezzen be a felügyeleti portálon a szolgáltatás-rendszergazdaként. A felügyeleti portál URL-címét az ECE áruházból vagy a Stamp-információs fájlból találja. Útmutatásért lásd: [környezeti paraméterek](azure-stack-vaas-parameters.md#environment-parameters).

2. Válassza a **További szolgáltatások**  >  **erőforrás-szolgáltatók**  >  **számítási** virtuálisgép-  >  **lemezképek** lehetőséget.

3. A virtuálisgép- **lemezképek** panel felső részén kattintson a **+ Hozzáadás** gombra.

4. Módosítsa vagy vizsgálja meg a következő mezők értékeit az első virtuálisgép-rendszerképnél:

    > [!IMPORTANT]
    > Nem minden alapértelmezett érték helyes a piactér meglévő eleménél.

    | Mező  | Érték  |
    |---------|---------|
    | Publisher | MicrosoftWindowsServer |
    | Ajánlat | WindowsServer |
    | Operációs rendszer típusa | Windows |
    | Termékváltozat | 2012-R2-Datacenter |
    | Verzió | 1.0.0 |
    | OPERÁCIÓSRENDSZER-lemez blob URI-ja | https://<*a Storage-fiók* >/< *tárolójának neve*>/WindowsServer2012R2DatacenterBYOL.vhd |


5. Válassza a **Létrehozás** gombot.

6. Ismételje meg a többi virtuálisgép-lemezképet.

Az összes szükséges virtuálisgép-rendszerkép tulajdonságai a következők:

| Publisher  | Ajánlat  | Operációs rendszer típusa | Termékváltozat | Verzió | OPERÁCIÓSRENDSZER-lemez blob URI-ja |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter – Server-Core | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3 – LTS | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*a Storage-fiók*]/[*tároló neve*]/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/OpenLogic-CentOS-69-20180105.vhd |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*a Storage-fiók*]/[*tároló neve*]/Debian8_latest. vhd |

## <a name="next-steps"></a>További lépések

- Tekintse át a [kibocsátási megjegyzéseket a szolgáltatásként való érvényesítéshez](azure-stack-vaas-release-notes.md) a legújabb kiadásokban való változásokhoz.