---
title: A Windows Defender víruskereső frissítése
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan frissítheti a Windows Defender víruskeresőt Azure Stack hub-on
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 31ef55554646065a5d61cc21abf4e72b7fcb4eda
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488093"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>A Windows Defender víruskereső frissítése Azure Stack hub-on

A [Windows Defender Antivirus](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) egy antimalware-megoldás, amely biztonságot és vírusvédelmet biztosít. Minden Azure Stack hub infrastruktúra-összetevő (Hyper-V-gazdagépek és virtuális gépek) védett a Windows Defender víruskeresővel. A naprakész védelem érdekében rendszeres frissítések szükségesek a Windows Defender víruskereső-definíciók, a motor és a platform számára. A frissítések alkalmazása a konfigurációtól függ.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

Az Azure Stack hub [Update erőforrás-szolgáltató](azure-stack-updates.md#the-update-resource-provider) naponta többször letölti a kártevő-definíciókat és a motor-frissítéseket. Minden Azure Stack hub infrastruktúra-összetevő beolvassa a frissítést a frissítési erőforrás-szolgáltatótól, és automatikusan alkalmazza a frissítést.

A nyilvános internethez csatlakozó Azure Stack központi telepítések esetében alkalmazza a [havi Azure stack hub-frissítést](azure-stack-apply-updates.md). A havi Azure Stack hub-frissítés tartalmazza a Windows Defender víruskereső platform frissítéseit a hónapra vonatkozóan.

## <a name="disconnected-scenario"></a>Leválasztott forgatókönyv

Azoknál a Azure Stack hub-telepítések esetében, amelyek nem csatlakoznak a nyilvános internethez (pl. gapped adatközpontok), a 1910-es kiadástól kezdve az ügyfeleknek lehetősége van a kártevő-definíciók és a motor frissítéseinek alkalmazására a közzétételük során. 

Ha a frissítéseket a Azure Stack hub-megoldásra szeretné alkalmazni, először le kell töltenie őket a Microsoft webhelyéről (az alábbi hivatkozásokat), majd ezt követően importálnia kell őket a *updateadminaccount*tartozó Storage blob-tárolóba. Egy ütemezett feladat 30 percenként megvizsgálja a BLOB tárolót, és az új Defender-definíciók és a motor frissítései a Azure Stack hub-infrastruktúrára vonatkoznak. 

Azon leválasztott központi telepítések esetében, amelyek még nem 1910 vagy újabb verziójúak, vagy amelyek nem képesek napi szinten letölteni a Defender-definíciókat és a motor frissítéseit, a havi Azure Stack hub-frissítés tartalmazza a Windows Defender víruskereső-definíciókat, a motort és a havi platform-frissítéseket. 


### <a name="set-up-windows-defender-for-manual-updates"></a>A Windows Defender manuális frissítésének beállítása 

Az 1910-es kiadással két új parancsmagot adott hozzá a Kiemelt végponthoz a Windows Defender manuális frissítésének konfigurálásához Azure Stack központban. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

Az alábbi eljárás bemutatja, hogyan telepítheti a Windows Defender manuális frissítését. 

1. Kapcsolódjon a privilegizált végponthoz, és futtassa a következő parancsmagot a Storage blob-tároló nevének megadásához, ahol a Defender frissítései fel lesznek töltve. 

   > [!NOTE] 
   > Az alább ismertetett manuális frissítési folyamat csak olyan leválasztott környezetekben működik, ahol a "go.microsoft.com" nem engedélyezett. Ha a set-AzsDefenderManualUpdate parancsmagot a csatlakoztatott környezetekben szeretné futtatni, hibaüzenetet fog eredményezni. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Töltse le a két Windows Defender-frissítési csomagot, és mentse őket egy olyan helyre, amely elérhető az Azure Stack hub felügyeleti portálján.  

   * mpam-fe.exe innen: [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full.exe innen: [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > **Minden alkalommal** le kell töltenie ezt a két fájlt, amikor frissíteni szeretné a Defender-aláírásokat. 

3. Az adminisztrációs portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **adat + tárolás** kategóriában válassza a Storage- **fiókok**lehetőséget. (Vagy a szűrő mezőbe írja be a Storage- **fiókok**kifejezést, majd válassza ki.) 

   ![Azure Stack hub Defender – minden szolgáltatás](./media/azure-stack-security-av/image1.png)  

4. A szűrő mezőbe írja be a **frissítés**értéket, majd válassza ki a **updateadminaccount** . 

5. A Storage-fiók adatai alatt, a **szolgáltatások**területen válassza a **Blobok**elemet. 

   ![Azure Stack hub Defender – blob](./media/azure-stack-security-av/image2.png) 

6. A tároló létrehozásához a **blob Service**alatt válassza a **+ tároló** elemet. Adja meg a set-AzsDefenderManualUpdate megadott nevet (ebben a példában a *defenderupdates*), majd kattintson **az OK gombra**. 

   ![Azure Stack hub Defender – tároló](./media/azure-stack-security-av/image3.png) 

7. A tároló létrehozása után kattintson a tároló nevére, majd kattintson a **feltöltés** gombra a csomagfájl tárolóba való feltöltéséhez. 

   ![Azure Stack hub Defender – feltöltés](./media/azure-stack-security-av/image4.png) 

8. A **blob feltöltése**lapon kattintson a mappa ikonra, keresse meg a Windows Defender Update *mpam-fe.exe* fájlokat, majd kattintson a **Megnyitás** gombra a fájlkezelő ablakban. 

9. A **blob feltöltése**területen kattintson a **feltöltés**elemre. 

   ![Azure Stack hub Defender – blob1 feltöltése](./media/azure-stack-security-av/image5.png) 

1. Ismételje meg a 8. és a 9. lépést a *nis_full.exe* fájl esetében. 

   ![Azure Stack hub Defender – blob2 feltöltése](./media/azure-stack-security-av/image6.png)

Egy ütemezett feladat 30 percenként megvizsgálja a BLOB tárolót, és minden új Windows Defender-csomagot alkalmaz.  

## <a name="next-steps"></a>Következő lépések

[További információ a Azure Stack hub biztonságáról](azure-stack-security-foundations.md)
