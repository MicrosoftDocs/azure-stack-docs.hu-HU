---
title: Azure-Cognitive Services üzembe helyezése Azure Stack | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure Cognitive Servicest Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 4323993c76019ffa2b3084679b2587e300094e38
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955637"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Az Azure Cognitive Services üzembe helyezése Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!Note]  
> Az Azure Cognitive Services on Azure Stack előzetes verzióban érhető el.

Az Azure Cognitive Services a tárolók támogatásával Azure Stackon is használható. Az Azure Cognitive Services tárolók támogatása lehetővé teszi, hogy ugyanazokat a gazdag API-kat használja, amelyek elérhetők az Azure-ban. A tárolók használata rugalmasságot biztosít a [Docker-tárolókban](https://www.docker.com/what-container)tárolt szolgáltatások üzembe helyezéséhez és üzemeltetéséhez. A tárolók támogatása jelenleg előzetes verzióban érhető el az Azure Cognitive Services egy részhalmaza számára, beleértve a [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), a [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), a [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)és a [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (Luis) részeit.

A tárolókra bontás olyan szoftverterjesztési módszer, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeit és a konfigurációját, tároló képként van csomagolva. Kevés vagy nem módosítható, ha lemezképet telepít egy tároló gazdagépre. Minden tároló el van különítve a többi tárolótól és a mögöttes operációs rendszertől. Maga a rendszer csak a rendszerkép futtatásához szükséges összetevőket tartalmaz. A tároló gazdagépek kisebb helyigénysel rendelkeznek, mint a virtuális gépek. A rövid távú feladatok képeiből is létrehozhat tárolókat, amelyek már nem szükségesek, de eltávolíthatók.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Tárolók használata Cognitive Services Azure Stack

- **Az adatvezérlés**  
  Lehetővé teheti, hogy az alkalmazás a Cognitive Services használata közben szabályozni tudják az adataikat. Cognitive Services biztosíthat olyan alkalmazás-felhasználók számára, akik nem tudnak a globális Azure-ba vagy a nyilvános felhőbe elküldeni az adatküldést.

- **A modell frissítéseinek vezérlése**  
  Adja meg az alkalmazások felhasználói számára a megoldásban üzembe helyezett modellek frissítéseit.

- **Hordozható architektúra**  
  Lehetővé teheti a hordozható alkalmazások architektúrájának létrehozását, hogy a megoldást a nyilvános felhőbe, a helyszíni saját felhőbe vagy a szegélybe telepítse. A tárolót üzembe helyezheti az Azure Kubernetes szolgáltatásban, Azure Container Instances vagy egy Azure Stack Kubernetes-fürtön. További információ: [a Kubernetes telepítése Azure stack](azure-stack-solution-template-kubernetes-deploy.md).

- **Magas átviteli sebesség és kis késleltetés**  
   Adja meg az alkalmazás felhasználóinak, hogy a nagy átviteli sebesség és az alacsony késés érdekében a forgalomban lévő tüskékkel méretezhetők legyenek. A Cognitive Services az Azure Kubernetes szolgáltatásban való futtatásának engedélyezése fizikailag az alkalmazás logikája és az adatkezelés érdekében.

A Azure Stack a magas rendelkezésre állás és a rugalmas skálázás érdekében helyezzen üzembe Cognitive Services tárolókat egy Kubernetes-fürtön, valamint az alkalmazás tárolóit. A kognitív szolgáltatásokat a App Services, a functions, a blob Storage, az SQL vagy a mySQL-adatbázisokra épülő összetevőkkel kombinálva fejlesztheti alkalmazásait.

A Cognitive Services tárolókkal kapcsolatos további információkért nyissa meg a [tárolók támogatása az Azure-ban Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Az Azure Face API üzembe helyezése

Ez a cikk azt ismerteti, hogyan helyezhető üzembe az Azure Face API egy Kubernetes-fürtön Azure Stackon. Ugyanezt a megközelítést használhatja más kognitív szolgáltatások tárolóinak üzembe helyezéséhez Azure Stack Kubernetes-fürtökön.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőket kell tennie:

1.  Kérje a tároló beállításjegyzékének elérését, hogy az Azure Cognitive Services Container Registryról olvassa el a Face Container-lemezképeket. Részletekért lásd: [hozzáférés kérése a Private Container registryhez](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Kubernetes-fürt előkészítése Azure Stackon. A [Kubernetes telepítése Azure Stackra című](azure-stack-solution-template-kubernetes-deploy.md)cikkben olvashat.

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Hozzon létre egy kognitív szolgáltatási erőforrást az Azure-ban a Face, LUIS vagy szövegfelismerés tárolók előzetes verziójának megtekintéséhez. Az erőforrásból az előfizetési kulcs és a végpont URL-címét kell használnia a kognitív szolgáltatás tárolóinak létrehozásához.

1. Hozzon létre egy Azure-erőforrást a Azure Portal. Ha szeretné megtekinteni a Face containers-t, először létre kell hoznia egy megfelelő arc-erőforrást a Azure Portal. További információ: gyors útmutató [: Cognitive Services fiók létrehozása a Azure Portalban](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  Az arc vagy Computer Vision erőforrásnak a F0 díjszabási szintjét kell használnia.

2. Az Azure-erőforrás végponti URL-címének és előfizetési kulcsának beolvasása. Miután létrehozta az Azure-erőforrást, az adott erőforrás előfizetési kulcs és végpont URL-címe alapján hozza létre a megfelelő Face, LUIS vagy szövegfelismerés tárolót az előzetes verzióhoz.

## <a name="create-a-kubernetes-secret"></a>Kubernetes titkos kód létrehozása 

Használja a Kubectl Create Secret parancsot a Private Container Registry eléréséhez. Cserélje le a `<username>` nevet a felhasználónévre, és `<password>` az Azure Cognitive Services csapattól kapott hitelesítő adatokban megadott jelszóval.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>YAML-konfiguráló fájl előkészítése

A YAML-konfiguráló fájl használatával egyszerűsítheti a kognitív szolgáltatás központi telepítését a Kubernetes-fürtön.

Az alábbi példa egy YAML-fájlt konfigurál a Face szolgáltatás üzembe helyezéséhez Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

Ebben a YAML konfigurálja a fájlt, és használja azt a titkot, amellyel beolvasta a kognitív szolgáltatás tárolójának rendszerképeit Azure Container Registryból. A titkos fájl a tároló egy adott replikájának központi telepítéséhez használatos. Létrehozhat egy Load balancert is, amely biztosítja, hogy a felhasználók külsőleg is hozzáférhessenek ehhez a szolgáltatáshoz.

A legfontosabb mezők részletei:

| Mező | Megjegyzések |
| --- | --- |
| replicaNumber | Meghatározza a létrehozandó példányok kezdeti replikáit. A telepítést később is méretezheti. |
| ImageLocation | Azt jelzi, hogy az adott kognitív szolgáltatás tárolójának képe hol található az ACR-ben. Például a Face szolgáltatás: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Az [Azure-erőforrás létrehozása](#create-azure-resources) lépésben feljegyzett végpont URL-címe |
| apiKey | Az [Azure-erőforrás létrehozása](#create-azure-resources) lépésben feljegyzett előfizetési kulcs |
| secretName | A [Kubernetes titkos kulcs létrehozása](#create-a-kubernetes-secret) című lépésben létrehozott titkos név |

## <a name="deploy-the-cognitive-service"></a>A kognitív szolgáltatás üzembe helyezése

A következő parancs használata a kognitív szolgáltatás tárolóinak üzembe helyezéséhez:

```bash  
    Kubectl apply -f <yamlFineName>
```
A következő parancs használata az üzembe helyezésének figyelésére: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>A kognitív szolgáltatás tesztelése

A [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) elérése az adott tárolóhoz tartozó **/Swagger** relatív URI-n keresztül. Ez a specifikáció, korábbi nevén a hencegés specifikáció, leírja a példányos tároló által támogatott műveleteket. A következő URI például hozzáférést biztosít az előző példában a Hangulatelemzés tárolóhoz tartozó OpenAPI-specifikációhoz:

```HTTP  
http:<External IP>:5000/swagger
```

A külső IP-címet a következő paranccsal kérheti le: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>A szolgáltatások kipróbálása a Pythonral

Néhány egyszerű Python-szkript futtatásával megpróbálhatja érvényesíteni a Azure Stack kognitív szolgáltatásait. Az Ön referenciájának [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)és [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (Luis) hivatalos Python gyors példákat tartalmaz.

Két dolgot kell szem előtt tartani, amikor Python-alkalmazásokat használ a tárolókban futó szolgáltatások ellenőrzésére: 
1. A tárolókban a kognitív szolgáltatásoknak nincs szükségük alárendelt kulcsokra a hitelesítéshez, de az SDK-nak megfelelő karakterláncot kell megadnia helyőrzőként. 
2. Cserélje le a base_URLt a tényleges szolgáltatási végpont IP-címére.

Itt látható egy példa Python-szkript a Face Services Python SDK használatával az arcok észleléséhez és a képek keretének megjelenítéséhez:

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Következő lépések

[Computer Vision API tárolók telepítése és futtatása.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Face API tárolók telepítése és futtatása](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Text Analytics API tárolók telepítése és futtatása](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Language Understanding-(LIUS-) tárolók telepítése és futtatása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
