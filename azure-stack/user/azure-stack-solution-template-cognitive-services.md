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
ms.openlocfilehash: c642a3eeb07ef2ce94ca8dc338a781256c5b9f37
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691234"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Az Azure Stack üzembe helyezése az Azure Cognitive Services

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stack az Azure Cognitive Services előzetes verzióban van.

Használhatja az Azure Cognitive Services-tároló támogatása az Azure Stacken. Tároló támogatása az Azure Cognitive Services lehetővé teszi, hogy az azonos API-k gazdag elérhető az Azure-ban. A tárolók használatát lehetővé teszi, hogy a rugalmasság a verziókban elérhető szolgáltatásokat üzembe helyezése és hol [Docker-tárolók](https://www.docker.com/what-container). Tárolótámogatás jelenleg érhető el az Azure Cognitive Services, beleértve egy részhalmazát előzetes verziójában [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), és [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), és [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Bontás megközelítés a szoftverek terjesztéséhez, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeket és a konfigurációt egy tárolórendszerképet vannak csomagolva. Kis vagy semmilyen módosítással telepíthet egy képet tároló gazdagéphez. A tárolók el különítve más tárolók pedig az alapul szolgáló operációs rendszerektől. Maga a rendszer csak a a a lemezkép futtatásához szükséges összetevőket tartalmazza. Egy tároló-gazdagép rendelkezik egy kisebb, mint a virtuális gép üzembe helyezésének előkészítése. Rövid távú feladatok rendszerképekből is létrehozhat tárolókat, és eltávolíthatók, ha már nincs szükség.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Az Azure Stacken Cognitive Services-tárolók használata

- **Adatok felett ellenőrzést**  
  Lehetővé teszi a felhasználók számára a szabályozhatják a Cognitive Services használata során az adatokat. Cognitive Services közvetíti azt felhasználói számára, akik nem lehet elküldeni az adatokat globális Azure-ban vagy a nyilvános felhőben.

- **Modell frissítése**  
  Adja meg. alkalmazás felhasználóinak a modellek üzembe helyezett megoldás verzió frissítései.

- **Hordozható architektúra**  
  Egy hordozható alkalmazásarchitektúra létrehozásának engedélyezése, hogy a megoldás a nyilvános felhőben, magánfelhőben telepítheti a felhő helyszíni vagy a peremhálózaton. Telepítheti a tároló az Azure Kubernetes Service, Azure Container Instances- vagy Kubernetes-fürthöz az Azure Stackben. További információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](azure-stack-solution-template-kubernetes-deploy.md).

- **Nagy átviteli sebességű és kis késése**  
   Adja meg az alkalmazás felhasználói adatforgalmának nagy átviteli sebességet és közel valós idejű a méretezhető. Engedélyezze a Cognitive Services futtatásához az Azure Kubernetes Service fizikailag közel az alkalmazás és az adatokat.

Az Azure Stack üzembe helyezése a Cognitive Services-tárolók egy Kubernetes-fürt az alkalmazástárolókat magas rendelkezésre állás és rugalmas méretezés mellett. Az alkalmazás az App Services, Functions, a Blob storage, SQL vagy mySQL-adatbázisok-összetevői és a Cognitive services együttes fejleszthet.

A Cognitive Services-tárolókra vonatkozóan további részleteket keresse [tároló támogatása az Azure Cognitive Servicesben](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Az Azure Face API üzembe helyezése

Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Face API, a Kubernetes-fürtön az Azure Stacken. Ugyanezzel a módszerrel használhatja más Azure Stack-beli Kubernetes-fürtökön a cognitive services-tárolók üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kell:

1.  Kérjen hozzáférést a Face tárolórendszerképek lekéréshez Azure Cognitive Services Container Registry tárolóregisztrációs adatbázisba. További információkért lásd: [kérjen hozzáférést a privát tárolóregisztrációs adatbázisba](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Készítsen elő egy Kubernetes-fürtöt az Azure Stacken. Kövesse a cikk [Kubernetes üzembe helyezése az Azure Stackhez](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

A Cognitive Services erőforrás létrehozása az Azure-ban az előzetes verzióra az Arcfelismerés, LUIS vagy szöveg felismerése tárolókat. Az előfizetési kulcs és a végpont URL-címet az erőforrás használatával hozza létre a cognitive Services-tárolók kell.

1. Hozzon létre egy Azure-erőforrást az Azure Portalon. Ha szeretné megtekinteni a Face tárolók, akkor először létre kell hoznia egy megfelelő Face erőforrást az Azure Portalon. További információkért lásd: [a rövid útmutató: Cognitive Services-fiók létrehozása az Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  A Face vagy a Computer Vision erőforrás tarifacsomagja F0 kell használnia.

2. A végpont URL-cím és egy előfizetési kulcsra az Azure-beli erőforráshoz kaphat. Miután létrehozott egy Azure-erőforrásokra, az előfizetés kulcsának és végpontjának URL-cím ennek az erőforrásnak a használatával hozza létre a megfelelő Face, LUIS vagy szöveg felismerése tároló az előzetes verzióra.

## <a name="create-a-kubernetes-secret"></a>A Kubernetes titkos kódjának létrehozása 

Győződjön meg arról, használja a kubectl create titkos parancs a privát tárolóregisztrációs adatbázis eléréséhez. Cserélje le `<username>` felhasználónévvel és `<password>` az Azure Cognitive Services csapatától kapott hitelesítő adatokat a megadott jelszóval.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Készítse elő egy YAML-fájl konfigurálása

Használja a YAML-fájl a kognitív szolgáltatás a Kubernetes-fürtön az üzembe helyezés egyszerűsítéséhez konfigurálása.

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

A YAML-fájl konfigurálása, az Azure Container Registry a cognitive Services-szolgáltatás tárolórendszerképeket beszerzéséhez használt titkos kulcsot használja. A titkos kód fájlját egy adott replika, a tároló üzembe helyezéséhez használatos. Egy terheléselosztót, hogy a felhasználók ezt a szolgáltatást kívülről is létrehozhat.

Kulcsmezők részleteit:

| Mező | Megjegyzések |
| --- | --- |
| replicaNumber | Meghatározza a kezdeti replika-példányok létrehozásához. Később az üzembe helyezés után át lehet méretezni. |
| ImageLocation | Azt jelzi, hogy a cognitive Services-szolgáltatás adott tárolórendszerképet az ACR helyét. Ha például az arcok szolgáltatás: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |A végpont URL-címe. a lépés [Azure-erőforrás létrehozása](#create-azure-resources) |
| ApiKey | Az előfizetési kulcsot, a lépésben feljegyzett [Azure-erőforrás létrehozása](#create-azure-resources) |
| SecretName | A [Create a Kubernetes secret(#create-a-kubernetes-secret) lépésben létrehozott titkos neve |

## <a name="deploy-the-cognitive-service"></a>A cognitive Services-szolgáltatás üzembe helyezése

A cognitive Services-tárolók üzembe helyezése a következő parancs használható:

```bash  
    Kubectl apply -f <yamlFineName>
```
Figyelheti, hogyan helyezi üzembe a következő parancsot a használata: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>A cognitive Services-szolgáltatás tesztelése

Hozzáférés a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) származó a **/swagger** tárolóban tartozó relatív URI. Ez az meghatározás, korábbi nevén Swagger-specifikációra, egy példányosított tároló által támogatott műveleteket ismerteti. Például a következő URI Azonosítót tartalmaz Hangulatelemzés tárolót, amelyet az előző példában volt példányosítani az OpenAPI-specifikáció való hozzáférést:

```HTTP  
http:<External IP>:5000/swagger
```

A külső IP-cím is kérhet a következő parancsot: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Próbálja ki a szolgáltatásokat a Pythonnal

Próbálkozzon a Cognitive services az Azure Stack ellenőrzése néhány egyszerű Python-szkriptek futtatásával. A hivatalos Python rövid minták [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), és [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) () A LUIS) referenciaként.

Két szempontot szem előtt tartani a tárolók rajta futó szolgáltatások ellenőrzése a Python-alkalmazások használatakor: 
1. A cognitive services-tárolókban alkulcsok nem szükséges a hitelesítéshez, de attól még igényelik bármilyen karakterlánc az SDK-val való helyettesíti. 
2. A base_URL cserélje le tényleges szolgáltatási végpont IP-címét.

Íme egy példa Python-szkriptet Face services Python SDK használatával, és alkalmas keretet biztosítanak az arcok a képen:

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
