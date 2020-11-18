---
title: Az adatközpont tűzfala Azure Stack HCI-ben – áttekintés
description: Ez a témakör az adatközpont tűzfala Azure Stack HCI-ben való megismerésére használható.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 34efe18aee4a62481d81a5e9a810a0a71d97b063
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874957"
---
# <a name="what-is-datacenter-firewall"></a>Mi az az adatközpont tűzfala?

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Az adatközpont tűzfala egy hálózati réteg, 5 rekord (protokoll, forrás és cél portszámok, forrás és cél IP-címek), állapot-nyilvántartó, több-bérlős szoftverek által definiált hálózatkezelési (SDN) tűzfal. Az adatközpont tűzfala védi a kelet-nyugati és az észak-déli forgalmat a virtuális hálózatok és a hagyományos VLAN-hálózatok hálózati rétegében.

## <a name="how-datacenter-firewall-works"></a>Az adatközpont tűzfala működése

Az adatközpont tűzfalát az alhálózatra vagy hálózati adapterre alkalmazott hozzáférés-vezérlési listák (ACL-ek) létrehozásával engedélyezheti és konfigurálhatja. A tűzfalszabályok az egyes bérlői virtuális gépek (VM) vSwitch-portján vannak kikényszerítve. A házirendeket a bérlői portálon keresztül küldi el a rendszer, és a [hálózati vezérlő](network-controller-overview.md) az összes érintett gazdagépre osztja el azokat.

A bérlői rendszergazdák telepíthetik és konfigurálhatják a tűzfalakat, hogy megvédik a hálózatokat az internetről és az intranetes hálózatokból származó nemkívánatos forgalomtól.

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="Adatközpont tűzfala a hálózati veremben" border="false":::

A szolgáltatói rendszergazda vagy a bérlő rendszergazdája a hálózati vezérlőn és a irányú API-kon keresztül felügyelheti az adatközponti tűzfal házirendjeit. A Windows felügyeleti központban is konfigurálhatja és kezelheti az adatközpont tűzfal házirendjeit.

## <a name="advantages-for-cloud-service-providers"></a>Előnyök a Cloud Service-szolgáltatók számára

Az adatközpont tűzfala a következő előnyöket kínálja a kriptográfiai szolgáltatásokhoz:

- A bérlők számára elérhető, rugalmasan méretezhető, felügyelhető és diagnosztizálható, szoftveres tűzfal megoldás

- A bérlői virtuális gépek különböző számítási gazdagépekre való áthelyezése a bérlői tűzfal házirendjeinek megszakítása nélkül

    - Üzembe helyezve vSwitch port ügynökének tűzfala

    - Bérlői virtuális gépek megkapják a vSwitch-gazdagép ügynökének tűzfalához rendelt szabályzatokat

    - A tűzfalszabályok minden vSwitch-porton konfigurálva vannak, a virtuális gépet futtató tényleges gazdagéptől függetlenül

- A bérlői vendég operációs rendszertől független, bérlői virtuális gépek védelmét nyújtja

## <a name="advantages-for-tenants"></a>A bérlők előnyei

Az adatközpont tűzfala a következő előnyöket kínálja a bérlők számára:

- A tűzfalszabályok definiálása az internetre irányuló munkaterhelések és a hálózatok belső munkaterhelése elleni védelem érdekében

- A tűzfalszabályok definiálása a virtuális gépek közötti, ugyanazon a 2. rétegbeli (L2) alhálózaton és a különböző L2-alhálózatokon lévő virtuális gépek közötti adatforgalom védelmének biztosítása érdekében

- Tűzfalszabályok definiálása a helyszíni hálózatok és a szolgáltatói virtuális hálózatok közötti hálózati forgalom védelméhez és elkülönítéséhez

- Tűzfal-házirendek alkalmazása a hagyományos VLAN-hálózatokra és az átfedésre épülő virtuális hálózatokra

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Adatközponti tűzfal használata Software-Defined hálózatkezeléshez Azure Stack HCI-ben](../manage/use-datacenter-firewall.md)
- [SDN Azure Stack HCI-ben](software-defined-networking.md)