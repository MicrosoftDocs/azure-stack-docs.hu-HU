---
title: A virtuális gép biztonsági mentése Azure Stack-hubhoz a CommVault használatával
description: Ismerje meg, hogyan készíthet biztonsági másolatot a virtuális gépről Azure Stack hub-on a CommVault.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: 61ea70c9a59442547a9b4b73d4a3f9e676cf89c7
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661041"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>A virtuális gép biztonsági mentése Azure Stack hub-on a CommVault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Virtuális gép biztonsági mentésének áttekintése a CommVault-mel

Ez a cikk végigvezeti a CommVault élő szinkronizálás konfigurálásán, és egy külön Azure Stack hub-méretezési egységen található helyreállítási virtuális gép frissítését ismerteti. Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy közös partneri megoldást az Azure Stack hub-ban üzembe helyezett Virtual Machines adatainak és rendszerállapotának védelme és helyreállítása érdekében.

A következő ábra a teljes megoldást mutatja be, amikor a CommVault használatával készít biztonsági mentést a virtuális gépekről.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

Ebben a cikkben a következőket fogja megtekinteni:

1. Hozzon létre egy virtuális gépet, amely a CommVault szoftvert futtatja a forrás Azure Stack hub-példányon.

2. Hozzon létre egy Storage-fiókot egy másodlagos helyen. A cikk feltételezi, hogy egy BLOB-tárolót hoz létre egy Storage-fiókban egy Azure Stack hub-példányban, külön (a cél), és hogy a cél Azure Stack hub elérhető a forrás Azure Stack hub-ból.

3. Konfigurálja a CommVault a forrás Azure Stack hub-példányon, és vegyen fel virtuális gépeket a forrás Azure Stack hubhoz a virtuálisgép-csoportba.

4. Konfigurálja a CommVault LifeSync.

Az Azure Stack hub-beli virtuális gépek Azure-felhőbe vagy más Azure Stack hubhoz való ellátásához letöltheti és felkínálhatja a kompatibilis partneri virtuálisgép-rendszerképeket is. Ez a cikk bemutatja a virtuális gépek védelmét a CommVault Live Sync szolgáltatással.

Ennek a megközelítésnek a topológiája a következő ábrához hasonlóan fog kinézni:

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>A CommVault virtuális gép létrehozása a CommVault Marketplace-elemmel

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza **az erőforrás** > **létrehozása számítási** > **CommVault**lehetőséget.

    > [!Note]  
    > Ha a CommVault nem érhető el, forduljon a felhő üzemeltetőjéhez.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Alapbeállítások konfigurálása a **virtuális gép létrehozása, 1**alapkonfiguráció:

    a. Adjon meg egy **nevet**.

    b. Válassza a **normál HHD**lehetőséget.
    
    c. Adjon meg egy **felhasználónevet**.
    
    d. Adjon meg egy **jelszót**.
    
    e. Erősítse meg a jelszavát.
    
    f. Válasszon egy **előfizetést** a biztonsági mentéshez.
    
    g. Válasszon ki egy **erőforráscsoportot**.
    
    h. Válassza ki az Azure Stack hub **helyét** . Ha ASDK használ, válassza a **helyi**lehetőséget.
    
    i. Kattintson az **OK** gombra.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Válassza ki a CommVault virtuális gép méretét. A biztonsági mentéshez használt virtuális gép méretének legalább 10 GB RAM-nak és 100 GB tárhellyel kell lennie.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Válassza ki a CommVault virtuális gép beállításait.

    a. Állítsa a rendelkezésre állást **nincs**értékre.
    
    b. A felügyelt lemezek használatához válassza az **Igen** lehetőséget.
    
    c. Válassza ki a **virtuális hálózat**alapértelmezett VNet.
    
    d. Válassza ki az alapértelmezett **alhálózatot**.
    
    e. Válassza ki az alapértelmezett **nyilvános IP-címet**.
    
    f. Hagyja meg a virtuális gépet az **Alapszintű** hálózati biztonsági csoportban.
    
    g. Nyissa meg a HTTP (80), HTTPS (443), SSH (22) és RDP (3389) portot.
    
    h. Válassza a **nincsenek kiterjesztések**lehetőséget.
    
    i. Válassza az **engedélyezve** lehetőséget a **rendszerindítási diagnosztika**számára.
    
    j. Hagyja **Letiltva**a **vendég operációs rendszer diagnosztika** beállítását.
    
    k. Hagyja meg az alapértelmezett **diagnosztikai Storage-fiókot**.
    
    l. Kattintson az **OK** gombra.

6. Tekintse át a CommVault virtuális gép összegzését, miután az érvényesítése sikeres volt. Kattintson az **OK** gombra.

## <a name="get-your-service-principal"></a>Az egyszerű szolgáltatás beszerzése

Tudnia kell, hogy az Identity Manager az Azure AD vagy az AD DFS. A következő táblázat tartalmazza azokat az információkat, amelyeknek a CommVault az Azure Stack központban kell beállítania.

| Elem | Leírás | Forrás |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Resource Manager URL-cím | Az Azure Stack hub Resource Manager-végpont. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| Alkalmazásnév |  |  |
| Alkalmazásazonosító | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| Előfizetés azonosítója | Az előfizetés-AZONOSÍTÓval érheti el az ajánlatokat az Azure Stack hub szolgáltatásban. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| Bérlő azonosítója (címtár-azonosító) | Az Azure Stack hub-bérlő azonosítója. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Alkalmazás jelszava | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>A biztonsági mentés konfigurálása a CommVault-konzol használatával

1. Nyissa meg az RDP-ügyfelet, és csatlakozzon a Azure Stack hub Commavult virtuális géphez. Adja meg hitelesítő adatait.

2. Telepítse Azure Stack hub PowerShell-t és Azure Stack hub-eszközöket a CommVault virtuális gépre.

    a. Az Azure Stack hub PowerShell telepítésével kapcsolatos utasításokért lásd: a [PowerShell telepítése Azure stack hubhoz](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Azure Stack hub-eszközök telepítésére vonatkozó utasításokért lásd: [Azure stack hub-eszközök letöltése a githubról](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Miután a CommVault a CommVault virtuális gépre települ, nyissa meg a Commcell-konzolt. A Start menüben válassza a **CommVault** > **CommVault Commcell-konzol**elemet.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Konfigurálja a biztonsági mentési tárházait úgy, hogy a CommVault Commcell-konzolon a Azure Stack hub-on kívül is használják a tárolót. A CommCell böngészőben válassza a tárolási erőforrások > Storage-készletek elemet. Kattintson a jobb gombbal, majd válassza a **tár hozzáadása elemet.** Válassza a **felhő**lehetőséget.

5. Adja hozzá a Storage-készlet nevét. Kattintson a **Tovább** gombra.

6. Válassza a**Felhőbeli tároló** **létrehozása** > lehetőséget.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Válassza ki a felhőalapú szolgáltatót. Ebben az eljárásban egy második Azure Stack hubot fogunk használni egy másik helyen. Válassza a Microsoft Azure Storage lehetőséget.

8. Válassza ki a CommVault virtuális gépet a MediaAgent.

9. Adja meg a Storage-fiókjához tartozó hozzáférési adatokat. Itt megtalálhatja az Azure Storage-fiók beállításával kapcsolatos utasításokat. Hozzáférési információk:

    -  **Service Host**: szerezze be az URL-cím nevét az erőforrás blob-tároló tulajdonságaiban. Az URL-cím például https:\//backuptest.blob.westus.stackpoc.com/mybackups volt, és a blob.westus.stackpoc.com a Service Host szolgáltatásban használták.
    
    -   **Fiók neve**: használja a Storage-fiók nevét. Ez a tárolási erőforrás elérési kulcsok paneljén található.
    
    -   **Hozzáférési kulcs**: szerezze be a hozzáférési kulcsot a tárolási erőforrás hozzáférési kulcsok paneljéről.
    
    -   **Tároló**: a tároló neve. Ebben az esetben a mybackups.
    
    -   **Tárolási osztály**: hagyja meg a felhasználói tároló alapértelmezett tárolási osztályát.

10. Hozzon létre egy Microsoft Azure Stack hub-ügyfelet [egy Microsoft Azure stack hub-ügyfél létrehozásához](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm) szükséges utasítások követésével

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Válassza ki azokat a virtuális gépeket vagy erőforráscsoportokat, amelyeknek védelemmel kell ellátnia a biztonsági mentési szabályzatot.

12. Konfigurálja úgy a biztonsági mentési ütemtervet, hogy az megfeleljen a helyreállítási RPO követelményeinek.

13. Hajtsa végre az első teljes biztonsági mentést.

## <a name="configure-commvault-live-sync"></a>CommVault élő szinkronizálásának konfigurálása 

Két lehetőség érhető el. Dönthet úgy, hogy replikálja a módosításokat a biztonsági másolatok elsődleges másolatából, vagy másodlagos másolatból replikálja a módosításokat a helyreállítási virtuális gépre. Egy biztonságimásolat-készletből való replikáláskor a rendszer kizárja az olvasási IO-hatást a forrásoldali gépen.

1. Az élő szinkronizálás konfigurálása során meg kell adnia a forrás Azure Stack hub-t (Virtual Server Agent) és a cél Azure Stack hub részleteit.

2. Az CommVault Live Sync konfigurálásának lépéseiért lásd: [Microsoft Azure stack hub élő szinkronizálásának replikációja](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Az élő szinkronizálás konfigurálása során meg kell adnia a cél Azure Stack hub és a virtuális kiszolgáló ügynökének részleteit.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Folytassa a konfigurációt, és adja hozzá azt a célként megadott Storage-fiókot, ahol a replika-lemezek lesznek tárolva, a replika virtuális gépeket tartalmazó erőforráscsoport (ok) és a replika virtuális gépekhez csatolni kívánt név.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. A virtuális gép méretét és a hálózati beállításokat úgy is megváltoztathatja, hogy az egyes virtuális gépek mellett a **configure (Konfigurálás** ) lehetőséget választja.

6. A replikálás gyakoriságának beállítása a cél Azure Stack hubhoz

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. A konfiguráció mentéséhez tekintse át a beállításokat. A rendszer ekkor létrehozza a helyreállítási környezetet, és a replikáció a kiválasztott intervallum szerint kezdődik.


## <a name="set-up-failover-behavior-using-live-sync"></a>A feladatátvételi viselkedés beállítása élő szinkronizálással

Az CommVault Live Sync lehetővé teszi, hogy a gépeket az eredeti Azure Stack hub-ban folytatott műveletek folytatása érdekében feladatátvételt hajtson végre az egyik Azure Stack hub-ról egy másikra. A munkafolyamat automatizált és naplózva van.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Válassza ki a helyreállítani kívánt virtuális gépeket a helyreállítási Azure Stack hubhoz, és válasszon egy tervezett vagy nem tervezett feladatátvételt. A tervezett feladatátvétel akkor megfelelő, ha a helyreállítási helyen végzett műveletek folytatása előtt idő van az éles környezet leállítására. A tervezett feladatátvétel leállítja az éles virtuális gépeket, a végső módosításokat replikálja a helyreállítási helyre, és a legújabb értékekkel online állapotba helyezi a helyreállítási virtuális gépeket, és alkalmazza a virtuális gép méretét és a hálózati konfigurációt az élő szinkronizálás konfigurálása során. A nem tervezett feladatátvétel megkísérli leállítani az éles virtuális gépeket, de folytatja, ha az éles környezet nem érhető el, és egyszerűen a helyreállítási virtuális gépeket online állapotba helyezi a virtuális gépre alkalmazott utolsó fogadott replikációs adatkészlettel, valamint a korábban kiválasztott mérettel és hálózati konfigurációval. Az alábbi képek egy nem tervezett feladatátvételt mutatnak be, amelyben a helyreállítási virtuális gépek online állapotba kerültek a CommVault Live Sync használatával.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>További lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  