---
title: A Azure Portal használata a Azure Stack HCI használatával
description: Azure Stack HCI-fürtök megtekintése és kezelése a Azure Portal használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560800"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>A Azure Portal használata a Azure Stack HCI használatával

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör azt ismerteti, hogyan csatlakozhat a Azure Portal Azure Stack HCI részéhez az Azure Stack HCI-fürtök globális nézetéhez. A Azure Portal használatával felügyelheti és figyelheti a fürtöket, még akkor is, ha a fizikai infrastruktúrát a helyszínen üzemelteti. A felhőalapú figyelés szükségtelenné teszi a helyszíni figyelési rendszer és az adatbázis fenntartását, az infrastruktúra összetettségének csökkentése érdekében. Emellett a riasztások és egyéb információk közvetlenül az Azure-ba való feltöltésével növeli a méretezhetőséget, amely minden nap több millió objektumot kezel.

## <a name="view-your-clusters-in-the-azure-portal"></a>Megtekintheti a fürtöket a Azure Portal

Azure Stack HCI-fürt regisztrálása után az Azure-erőforrás látható a Azure Portalban. A megtekintéshez először jelentkezzen be a [Azure Portalba](https://portal.azure.com). Ha már [regisztrálta a fürtöt az Azure](../deploy/register-with-azure.md)-ban, egy új erőforráscsoportot kell látnia a fürt nevével, amely a "-RG" utótaggal van hozzáfűzve. Ha a Azure Stack HCI-erőforráscsoport nem jelenik meg, keressen rá a "HCI" kifejezésre, és válassza ki a fürtöt a legördülő menüből:

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="A HCI Azure Portal keresése a Azure Stack HCI-erőforrás megkereséséhez":::

Az Azure Stack HCI szolgáltatás kezdőlapja felsorolja az összes fürtöt, valamint az erőforráscsoportot, a helyet és a társított előfizetést.

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Azure Stack HCI szolgáltatás kezdőlapja Azure Portal":::

Kattintson egy Azure Stack HCI-erőforrásra az erőforrás áttekintő oldalának megtekintéséhez, amely a fürt és a kiszolgáló csomópontjainak magas szintű összegzését jeleníti meg.

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Azure Stack HCI-erőforrás áttekintő összefoglaló lapja Azure Portal":::

## <a name="view-the-activity-log"></a>A műveletnapló megtekintése

A tevékenység naplója felsorolja a fürt legutóbbi műveleteit és eseményeit, valamint az állapotukat, az időt, a társított előfizetést, valamint a felhasználó kezdeményezését. Az eseményeket előfizetéssel, súlyossággal, időkerettel, erőforráscsoport és erőforrás alapján szűrheti.

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Tevékenység napló képernyője Azure Stack HCI-erőforráshoz Azure Portal":::

## <a name="configure-access-control"></a>A hozzáférés-vezérlés konfigurálása

A hozzáférés-vezérléssel ellenőrizheti a felhasználók hozzáférését, kezelheti a szerepköröket, és megtekintheti a szerepkör-hozzárendeléseket és megtagadhatja a hozzárendeléseket

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Hozzáférés-vezérlési képernyő Azure Stack HCI-erőforráshoz Azure Portal":::

## <a name="add-and-edit-tags"></a>Címkék hozzáadása és szerkesztése

A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. A címkék nevei megkülönböztetik a kis-és nagybetűket, és a címke értéke kis-és nagybetűk megkülönböztetése. [További információ a címkékről](/azure/azure-resource-manager/management/tag-resources).

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="Címkék hozzáadása vagy szerkesztése Azure Stack HCI-erőforráshoz Azure Portal":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>A Azure Portal és a Windows felügyeleti központ összehasonlítása

A Windows felügyeleti központtól eltérően a Azure Stack HCI-hez készült Azure Portal-élmény globálisan méretezhető többfürtes figyeléshez lett tervezve. Az alábbi táblázat segítségével meghatározhatja, hogy melyik felügyeleti eszköz az Ön igényeinek megfelelő. Együttesen egységes kialakítást biztosítanak, és kiegészítő helyzetekben hasznosak.

| Windows felügyeleti központ | Azure Portal |
| --------------- | --------------- |
| A helyi hardver és a virtuális gép (VM) felügyelete mindig elérhető | Méretezési felügyelet, további funkciók |
| Azure Stack HCI-infrastruktúra kezelése | Más Azure-szolgáltatások kezelése |
| Egyes fürtök figyelése és frissítése | Nagy léptékű monitorozás és frissítés |
| VIRTUÁLIS gépek kézi üzembe helyezése és kezelése | Az Azure arc önkiszolgáló virtuális gépei |

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Azure Stack HCI összekötése az Azure-ba](../deploy/register-with-azure.md)
- [Azure Stack HCI figyelése Azure Monitor](azure-monitor.md)
