---
title: A Machine learning-modell tanítása a peremhálózati mintán
description: Ismerje meg, hogyan végezheti el a gépi tanulási modellek betanítását az Azure-ral és Azure Stack hub-val.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: da1012cc8847f221de6bb540ba4e191e43920f41
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847707"
---
# <a name="train-machine-learning-model-at-the-edge-pattern"></a>A Machine learning-modell tanítása a peremhálózati mintán

Hordozható gépi tanulási (ML) modellek készítése csak a helyszínen található adatokból.

## <a name="context-and-problem"></a>Kontextus és probléma

Számos szervezet szeretné feloldani a helyszíni vagy örökölt adatokból származó elemzéseket az adatszakértők által megértett eszközök használatával. A [Azure Machine learning](/azure/machine-learning/) a felhőben natív eszközöket biztosít az ml-és mély tanulási modellek betanításához, finomhangolásához és üzembe helyezéséhez.  

Bizonyos adatmennyiségek azonban túl nagy mennyiségű küldést biztosítanak a felhőnek, vagy szabályozási okokból nem küldhetők el a felhőbe. Ennek a mintának a használatával az adatszakértők a Azure Machine Learning használatával betanítják a modelleket a helyszíni adatok és a számítások használatával.

## <a name="solution"></a>Megoldás

Az Edge-minta betanítása Azure Stack hub-on futó virtuális gépet (VM) használ. A virtuális gép számítási célként van regisztrálva az Azure ML-ben, és csak a helyszínen elérhető adatokhoz férhet hozzá. Ebben az esetben a rendszer a Azure Stack hub blob Storage-tárolójában tárolja az adattárakat.

A modell betanítása után regisztrálva van az Azure ML-ben, a tárolóban, és hozzáadva egy Azure Container Registryhoz az üzembe helyezéshez. A minta ezen iterációjában a Azure Stack hub betanítási virtuális gépnek elérhetőnek kell lennie a nyilvános interneten.

[![ML modell betanítása a peremhálózati architektúrában](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

A minta működése:

1. Az Azure Stack hub virtuális gép üzembe helyezése és számítási célként való regisztrálása az Azure ML-ben történik.
2. Az Azure ML-ben olyan kísérlet jön létre, amely a Azure Stack hub virtuális gépet számítási célként használja.
3. A modell betanítása után regisztrálva és tárolóban van.
4. A modell mostantól a helyszínen vagy a felhőben is üzembe helyezhető.

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Összetevő | Description |
|----------|-----------|-------------|
| Azure | Azure Machine Learning | [Azure Machine learning](/azure/machine-learning/) összehangolja a ml modell betanítását. |
| | Azure Container Registry | Az Azure ML becsomagolja a modellt egy tárolóba, és tárolja [Azure Container Registry](/azure/container-registry/) a telepítéshez.|
| Azure Stack hub | App Service | A [Azure stack hub és a app Service](/azure-stack/operator/azure-stack-app-service-overview) biztosítja az összetevők alapját az Edge-ben. |
| | Compute | Az Ubuntut és a Docker-t futtató Azure Stack hub-beli virtuális gép a ML modell betanítására szolgál. |
| | Storage | A magánhálózati adattárak Azure Stack hub blob Storage-ban is tárolhatók. |

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Méretezhetőség

A megoldás méretezésének engedélyezéséhez létre kell hoznia egy megfelelő méretű virtuális gépet a Azure Stack hub-on a betanításhoz.

### <a name="availability"></a>Rendelkezésre állás

Győződjön meg arról, hogy a betanítási szkriptek és a Azure Stack hub virtuális gép hozzáfér a betanításhoz használt helyszíni adatszolgáltatásokhoz.

### <a name="manageability"></a>Kezelhetőség

Győződjön meg arról, hogy a modellek és kísérletek megfelelően vannak regisztrálva, verziószámozással és címkézve, hogy elkerülje a modell üzembe helyezése során felmerülő zavart.

### <a name="security"></a>Biztonság

Ez a minta lehetővé teszi, hogy az Azure ML hozzáférhessen a lehetséges bizalmas adatokhoz a helyszínen. Győződjön meg arról, hogy az SSH-ba Azure Stack hub virtuális géphez használt fiók erős jelszóval és betanítási szkriptekkel nem őrzi meg az adatok felhőbe való feltöltését

## <a name="next-steps"></a>További lépések

További információ a cikkben bemutatott témakörökről:

- A ML és a kapcsolódó témakörök áttekintéséhez tekintse meg a [Azure Machine learning dokumentációját](/azure/machine-learning) .
- Tekintse meg a [Azure Container Registry](/azure/container-registry/) a lemezképek létrehozásához, tárolásához és kezeléséhez a tárolók üzembe helyezéséhez című témakört.
- Az erőforrás-szolgáltatóval és a telepítésével kapcsolatos további tudnivalókért tekintse [meg a app Service on Azure stack hub](/azure-stack/operator/azure-stack-app-service-overview) című témakört.
- Tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásához.
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack) .

Ha készen áll a megoldás tesztelésére, folytassa az [éles üzembe helyezési útmutatóban található "ml" betanítási modellel](https://aka.ms/edgetrainingdeploy). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.
