---
title: Rövid útmutató Azure Stack HCI-fürt létrehozásához és az Azure-ban való regisztrálásához
description: Megtudhatja, hogyan helyezhet üzembe Azure Stack HCI-t, hogyan hozhat létre fürtöt a Windows felügyeleti központban, és hogyan regisztrálhatja azt az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: a6e725eee638b0afd2dddbe2f382cade729f9873
ms.sourcegitcommit: d91d44762383790a0bcfc4a85f43050c8528d5d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97069784"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>Gyors útmutató: Azure Stack HCI-fürt létrehozása és regisztrálása az Azure-ban

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy kétkiszolgálós, egyhelyes Azure Stack HCI-fürtöt, és hogyan regisztrálhatja azt az Azure-ban. Többhelyes telepítések esetén lásd a [kiterjesztett fürtök áttekintése](../concepts/stretched-clusters.md)című témakört.

## <a name="before-you-start"></a>Előkészületek

Fürt létrehozása előtt tegye a következőket:

* Vásároljon két kiszolgálót a [Azure stack HCI katalógusból](https://azure.microsoft.com/products/azure-stack/hci/catalog/) az előnyben részesített Microsoft-partneri kapcsolaton keresztül a Azure stack HCI operációs rendszerrel előre telepítve. Tekintse át a [rendszerkövetelményeket](../concepts/system-requirements.md) , és győződjön meg arról, hogy a kiválasztott hardver támogatja majd a fürtön futtatni kívánt munkaterheléseket. Javasoljuk, hogy használjon olyan nagy sebességű hálózati adaptereket használó rendszereket, amelyek egyszerű konfigurációhoz iWARP használnak.
* Hozzon létre egy olyan felhasználói fiókot, amely tagja a helyi Rendszergazdák csoportnak az egyes kiszolgálókon.
* [Szerezze be az Azure-előfizetést](https://azure.microsoft.com/), ha még nem rendelkezik ilyennel.
* [Telepítse a Windows felügyeleti központot](/windows-server/manage/windows-admin-center/deploy/install) egy felügyeleti számítógépre, és [regisztrálja a Windows felügyeleti központot az Azure](../manage/register-windows-admin-center.md)-ban. Vegye figyelembe, hogy a felügyeleti számítógépnek ugyanahhoz a Active Directory tartományhoz kell tartoznia, amelyben létre kívánja hozni a fürtöt, vagy egy teljesen megbízható tartományt.
* Jegyezze fel az üzemelő példány kiszolgálójának nevét, tartománynevét, IP-címét és VLAN-AZONOSÍTÓját.

## <a name="create-the-cluster"></a>A fürt létrehozása

Az alábbi lépéseket követve hozzon létre egy egyszerű, két csomópontos, egyhelyes fürtöt. További részletekért vagy egy kifeszített fürt létrehozásához tekintse meg [az Azure stack HCI-fürt létrehozása a Windows felügyeleti központtal](create-cluster.md)című témakört.

1. A Windows felügyeleti központban a **minden kapcsolat** területen kattintson a **Hozzáadás** elemre.
1. Az **erőforrások hozzáadása** panelen, a **Windows Server-fürt** területen válassza az **új létrehozása** lehetőséget.
1. A **fürt típusának kiválasztása** területen válassza a **Azure stack HCI** lehetőséget.
1. A **kiszolgáló helyeinek kiválasztása** területen válassza **a minden kiszolgáló lehetőséget az egyik helyen**.
1. Kattintson a **Létrehozás** gombra. Ekkor megjelenik a fürt létrehozása varázsló. Ha megjelenik a **hitelesítő adatok biztonsági szolgáltatójának (CredSSP)** előugró ablaka, válassza az **Igen** lehetőséget, hogy ideiglenesen engedélyezze. 

A fürt létrehozása varázsló öt szakaszt tartalmaz, amelyek mindegyike több lépésből áll.

1. **Első lépések.** Ebben a szakaszban megtekintheti az előfeltételeket, kiszolgálókat adhat hozzá, csatlakozhat egy tartományhoz, telepítheti a szükséges szolgáltatásokat és frissítéseket, majd újraindíthatja a kiszolgálókat. 
2. **Hálózati.** A varázsló ezen szakasza ellenőrzi, hogy a megfelelő hálózati adapterek engedélyezve vannak-e, és letiltja a nem használt funkciókat. Válassza ki a felügyeleti adaptereket, állítson be egy virtuális kapcsolót, és adja meg a hálózatot az IP-címek megadásával.
3. **Fürtszolgáltatás.** Ez a szakasz ellenőrzi, hogy a kiszolgálók konzisztens konfigurációval rendelkeznek-e, és alkalmas-e a fürtözésre, és létrehozza a tényleges fürtöt.
4. **Storage.** Ezután tisztítsa és ellenőrizze a meghajtókat, érvényesítse a tárhelyét, és engedélyezze Közvetlen tárolóhelyek.
5. **Sdn.** Kihagyhatja az 5. szakaszt, mert nem használjuk a szoftveresen definiált hálózatkezelést (SDN) ehhez a fürthöz.

Ha a varázslóban engedélyezte a CredSSP protokollt, biztonsági okokból le kell tiltania az egyes kiszolgálókon.

1. A Windows felügyeleti központban az **összes kapcsolat** területen válassza ki az imént létrehozott fürtöt.
1. Az **eszközök** területen válassza a **kiszolgálók** elemet.
1. A jobb oldali ablaktáblában válassza ki az első kiszolgálót a fürtben.
1. Az **Áttekintés** területen válassza a **CredSSP letiltása** lehetőséget. Ekkor láthatja, hogy a piros **CREDSSP engedélyezte** a szalagcímet a felső részen.
1. Ismételje meg a 3. és a 4. lépést a fürt második kiszolgálóján.

## <a name="set-up-a-cluster-witness"></a>Tanúsító fürt beállítása

Szükség van egy tanúsító erőforrás beállítására, hogy ha a fürt egyik kiszolgálója offline állapotba kerül, a másik csomópont is elérhetetlenné válik. Ebben a rövid útmutatóban egy másik kiszolgálón található SMB-fájlmegosztást használjuk tanúsító. Érdemes lehet egy Azure-beli Felhőbeli tanúsító használni, ha a fürt összes kiszolgáló-csomópontja megbízható internetkapcsolattal rendelkezik. További információ a tanúsító lehetőségekről: [a tanúsító fürt beállítása](witness.md).

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai** területen válassza ki a fürtöt.
1. Az **eszközök** területen válassza a **Beállítások** lehetőséget.
1. A jobb oldali ablaktáblán válassza a **tanúsító** elemet.
1. A **tanúsító típusa mezőben** válassza a **tanúsító fájlmegosztás** lehetőséget.
1. Adja meg a fájlmegosztás elérési útját, például **\\ servername. domain. com\Witness $** , és szükség esetén adja meg a hitelesítő adatokat.
1. Kattintson a **Mentés** gombra.

## <a name="register-with-azure"></a>Regisztrálás az Azure-ban

Azure Stack HCI-hez kapcsolódnia kell az Azure-hoz, és Azure Active Directory engedélyekkel kell rendelkeznie a regisztráció befejezéséhez. Ha még nem rendelkezik ezekkel, kérje meg az Azure AD-rendszergazdát, hogy adjon meg engedélyeket vagy delegáljon Önnek. További információért lásd: [Azure stack HCI összekötése az Azure-](register-with-azure.md) ba. A regisztrálás után a fürt automatikusan csatlakozik a háttérben.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Stack HCI-fürtöt, és regisztrálta az Azure-ban. Most már készen áll a [kötetek létrehozására](../manage/create-volumes.md) , majd a [virtuális gépek létrehozására](../manage/vm.md).