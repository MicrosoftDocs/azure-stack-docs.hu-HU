---
title: Az Azure Stack üzembe helyezése az Azure Cognitive Services |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure Cognitive Services az Azure Stackhez.
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
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1f8991fb25bb0b8c3a8159bc82abcc9b52d871c8
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986026"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Az Azure Stack üzembe helyezése az Azure Cognitive Services

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stack az Azure Cognitive Services előzetes verzióban van.

Használhatja az Azure Cognitive Services-tároló támogatása az Azure Stacken. Tároló támogatása az Azure Cognitive Services lehetővé teszi, hogy az azonos API-k gazdag elérhető az Azure-ban. A tárolók használatát lehetővé teszi, hogy a rugalmasság a verziókban elérhető szolgáltatásokat üzembe helyezése és hol [Docker-tárolók](https://www.docker.com/what-container). Tárolótámogatás jelenleg érhető el az Azure Cognitive Services, beleértve egy részhalmazát előzetes verziójában [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), és [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), és [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Bontás a szoftverek terjesztéséhez, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeket és a konfigurációt, vannak csomagolva, egy tárolórendszerképet egy megközelítést. Kis vagy semmilyen módosítással telepíthet egy képet tároló gazdagéphez. A tárolók el különítve más tárolók pedig az alapul szolgáló operációs rendszerektől. A rendszer, magát, csak a a a lemezkép futtatásához szükséges összetevőket tartalmazza. Egy tároló-gazdagép rendelkezik egy kisebb, mint a virtuális gép üzembe helyezésének előkészítése. Emellett, rövid távú feladatok rendszerképekből tárolókat hozhat létre, és eltávolítja, ha már nincs szükség.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Az Azure Stacken Cognitive Services-tárolók használata

- **Adatok felett ellenőrzést**  
  Lehetővé teszi a felhasználók számára a szabályozhatják a Cognitive Services használata során az adatokat. Cognitive Services közvetíti azt felhasználói számára, akik nem lehet elküldeni az adatokat globális Azure-ban vagy a nyilvános felhőben.

- **Modell frissítése**  
  Adja meg a felhasználók számára a verziót és frissítése, valamint a modellek üzembe helyezett megoldás.

- **Hordozható architektúra**  
  Egy hordozható alkalmazásarchitektúra létrehozásának engedélyezése, hogy a megoldás a nyilvános felhőben, magánfelhőben telepítheti a felhő helyszíni vagy a peremhálózaton. Telepítheti a tároló az Azure Kubernetes Service, Azure Container Instances, vagy az Azure Stack a Kubernetes-fürthöz. További információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](azure-stack-solution-template-kubernetes-deploy.md).

- **Nagy átviteli sebességű és kis késése**  
   Adja meg az alkalmazás felhasználói adatforgalmának nagy átviteli sebességet és közel valós idejű a méretezhető. Engedélyezze a Cognitive Services futtatásához az Azure Kubernetes Service fizikailag közel az alkalmazáslogika és az adatokat.

Az Azure Stack üzembe helyezése a Cognitive Services-tárolók egy Kubernetes-fürt az alkalmazástárolók, magas rendelkezésre állás és rugalmas méretezés mellett. Az alkalmazás-összetevői App Services, Functions, a Blob storage, vagy az SQL vagy mySQL-adatbázisok és a Cognitive services együttes fejleszthet. 

A Cognitive Services-tárolókra vonatkozóan további részleteket keresse [tároló támogatása az Azure Cognitive Servicesben](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Az Azure Face API üzembe helyezése

Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Face API, Kubernetes-fürtön az Azure Stacken. Ugyanezzel a módszerrel használhatja más Azure Stack-beli Kubernetes-fürtön a cognitive services-tárolók üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kell:

1.  Kérjen hozzáférést a Face tárolórendszerképek lekéréshez Azure Cognitive Services Container Registry tárolóregisztrációs adatbázisba. A részleteket szakaszában [kérjen hozzáférést a privát tárolóregisztrációs adatbázisba](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Készítsen elő egy Kubernetes-fürtöt az Azure Stacken. Kövesse a cikk [Kubernetes üzembe helyezése az Azure Stackhez](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Az Azure-ban az előzetes verzióra az Arcfelismerés, LUIS vagy szöveg felismerése tárolók, illetve a Cognitive Services erőforrás létrehozása. Az előfizetési kulcs és a végpont URL-címet az erőforrás használatával hozza létre a cognitive Services-tárolók kell.

1. Hozzon létre egy Azure-erőforrást az Azure Portalon. Ha szeretné megtekinteni a Face tárolók, akkor először létre kell hoznia egy megfelelő Face erőforrást az Azure Portalon. További információkért lásd: [a rövid útmutató: Cognitive Services-fiók létrehozása az Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  A Face vagy a Computer Vision erőforrás tarifacsomagja F0 kell használnia.

2. A végpont URL-cím és egy előfizetési kulcsra az Azure-beli erőforráshoz kaphat. Az Azure-erőforrás létrehozása után az előzetes verzióra a megfelelő Face, LUIS vagy szöveg felismerése tároló példányosítása az előfizetés kulcsának és végpontjának URL-címet adott erőforrást kell használnia.

## <a name="create-a-kubernetes-secret"></a>A Kubernetes titkos kódjának létrehozása 

Végezze el a használatát a Kubectl create titkos parancs a privát tárolóregisztrációs adatbázis eléréséhez. Cserélje le **&lt;felhasználónév&gt;** felhasználónévvel és **&lt;jelszó&gt;** az Azure-tól kapott a hitelesítő adatokban megadott jelszóval A cognitive Service csapatának.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Készítse elő egy YAML-fájl konfigurálása

Akkor fog használni a YAML-fájl a kognitív szolgáltatás a Kubernetes-fürtön az üzembe helyezés egyszerűsítéséhez konfigurálása.

Íme egy minta YAML-fájl Face szolgáltatás üzembe helyezése az Azure Stackhez való konfigurálása:

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

A YAML-fájl konfigurálása, az Azure Container Registry a cognitive Services-szolgáltatás tárolórendszerképeket beszerzéséhez használt titkos kulcsot használja. Ön és a titkos kód fájlját használja egy adott replika, a tároló üzembe helyezéséhez. Is, hogy a felhasználók férhetnek hozzá a szolgáltatás külső load balancer létrehozása.

Kulcsmezők részleteit:

| Mező | Megjegyzések |
| --- | --- |
| replicaNumber | Meghatározza a kezdeti replika-példányok létrehozásához. Minden bizonnyal skálázhatja azt később az üzembe helyezés után. |
| ImageLocation | Azt jelzi, hogy a cognitive Services-szolgáltatás adott tárolórendszerképet az ACR helyét. Ha például az arcok szolgáltatás: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |A végpont URL-címe. lépésében feltüntetett [Azure-erőforrás létrehozása](#create-azure-resources) |
| ApiKey | Az előfizetési kulcsot. lépésében feltüntetett [Azure-erőforrás létrehozása](#create-azure-resources) |
| SecretName | Název tajného kódu létrehozás. lépésében feltüntetett secrete a privát tárolóregisztrációs adatbázis eléréséhez |

## <a name="deploy-the-cognitive-service"></a>A cognitive Services-szolgáltatás üzembe helyezése

Használja a következő parancs a cognitive Services-tárolók üzembe helyezése

```bash  
    Kubectl apply -f <yamlFineName>
```
Figyelheti, hogyan helyezi üzembe a következő parancsot a használata: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>A cognitive Services-szolgáltatás tesztelése

Hozzáférés a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) (korábbi nevén a Swagger-specifikációra), egy példányosított tároló által támogatott műveleteket leíró a **/swagger** tárolóban tartozó relatív URI. Például a következő URI Azonosítót tartalmaz Hangulatelemzés tárolót, amelyet az előző példában volt példányosítani az OpenAPI-specifikáció való hozzáférést:

```HTTP  
http:<External IP>:5000/swagger
```

A külső IP-cím is kérhet a következő parancsot: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Próbálja ki a szolgáltatásokat a Pythonnal

Próbálkozzon a Cognitive services az Azure Stack ellenőrzése néhány egyszerű Python-szkriptek futtatásával. A hivatalos Python rövid minták [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), és [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), és [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) () A LUIS) referenciaként.

Nincsenek két dolgokat kell vegye figyelembe, hogy a Python-alkalmazásban, a tárolók rajta futó szolgáltatások futtatásához: 
1. A cognitive services-tárolóban nem kell sub kulcsokat a hitelesítéshez, de továbbra is kell adjon meg minden olyan karakterláncot az SDK-val való helyettesíti. 
2. A base_URL cserélje le tényleges szolgáltatási végpont IP-címe 

Íme egy példa Python használt parancsfájl Face szolgáltatások Python SDK-val észlelheti és keret arcok a képen. 

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

## <a name="next-steps"></a>További lépések

[Hogyan kell telepíteni, és a Computer Vision API-tárolók futtatásához.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Hogyan telepítheti és futtathatja a Face API-tárolók](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Hogyan telepítheti és futtathatja a Text Analytics API-tárolók](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Telepítése és Language Understanding (LIUS) tárolók futtatása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
