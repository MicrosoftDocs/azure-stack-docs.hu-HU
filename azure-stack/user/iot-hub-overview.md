---
title: Az Azure Stack hub IoT Hub áttekintése
description: Ismerkedjen meg a Azure Stack hub IoT Hub erőforrás-szolgáltatóval.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 2d68f076bf5687279aa5878083e7d007d5bc2e32
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049770"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Az Azure Stack hub IoT Hub áttekintése

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

A IoT Hub on Azure Stack hub lehetővé teszi hibrid IoT-megoldások létrehozását. A IoT Hub felügyelt szolgáltatás, amely központi üzenetsor a IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz. A IoT Hub on Azure Stack hub használatával IoT-megoldásokat hozhat létre a IoT-eszközök és egy helyszíni megoldási háttér közötti megbízható és biztonságos kommunikációhoz.

## <a name="features"></a>Funkciók

| Funkció | IoT Hub az Azure-ban | IoT Hub Azure Stack hub előzetes verziójában (S2/S3) |
|-|-|-|
|Eszközről a felhőbe irányuló telemetria| ✔ | ✔ |
|A felhőből az eszközre irányuló üzenetküldés| ✔ | ✔ |
|Eszközönkénti azonosító| ✔ | ✔ |
|Üzenet-útválasztás <sub>1</sub><sub>, 4</sub>| ✔ | ✔ |
|HTTP, AMQP, MQTT protokollok| ✔ | ✔ |
|Több-bérlős üzemmód| ✔ | ✔ |
|Monitorozás és diagnosztika| ✔ | ✔ |
|A felhőből az eszközre irányuló üzenetküldés| ✔ | ✔ |
|Eszközkezelés, eszköz Twin, modul Twin| ✔ | ✔ |
|Kettős értesítések, eszköz életciklusával kapcsolatos események| ✔ | ✔ |
|Edge rétegzett üzembe helyezés| ✔ | jön |
|Felügyeleti portál <sub>2</sub>| ✘ | ✔ |
|Secret rotációs <sub>2</sub>| ✘ | ✔ |
|Kapacitás-kezelés <sub>2</sub>| ✘ | ✔ |
|Backup & <sub>3</sub> . visszaállítás| ✘ | ✘ |
|DeviceConnected, DeviceDisconnected, ASC <sub>4</sub>| ✔ | ✘ |
|Eszköz moduljának konfigurálása| ✔ | jön |
|Eszközök folyamatos átvitele, IoT Plug and Play, feladatok, fájlfeltöltés <sub>5</sub>| ✔ | ✘ |
|Eszköz csatlakoztatási állapotának figyelése Event Grid <sub>4</sub> használatával| ✔ | ✘ |
|Feladatátvétel <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> a beépített végpontokra, Event Hubsra és tárhelyre korlátozódik. A Service Bus Azure Stack központban nem érhető el.  
<sub>2</sub> az operátorok a hamu IoT hub kezelésére.  
<sub>3</sub> a biztonsági mentés előzetes kiadásban érhető el. A visszaállítás a GA verzióban lesz támogatott.  
<sub>4</sub> a Azure stack hub-on nem elérhető egyéb szolgáltatástól függ.  
<sub>5</sub> a Azure stack hub felé irányuló menetrendben.  
<sub>6</sub> Azure stack hub esetében nem alkalmazható.  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>A Azure Stack hub IoT Hub számára elérhető API

|API-k|IoT Hub Azure Stack központban|
|-|-|
|Konfiguráció alkalmazása az eszközön| ✔ |
| Konfiguráció létrehozása | ✔ |
| Konfiguráció törlése | ✔ |
| Konfiguráció olvasása | ✔ |
|Konfiguráció olvasása sok| ✔ |
|Konfigurációs szolgáltatás alkalmazása|  ✔ |
|Konfiguráció frissítése|  ✔ |
|Közvetlen eszköz hívási metódusa|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Eszköz regisztrációjának törlése| ✔ |
|Eszközök beolvasása| ✔ |
|Modul frissítése – Twin| ✔ |
|D2C Get Twin| ✔ |
|Eszközök importálása| ✔ |
|Get Twin| ✔ |
|Modul regisztrációjának törlése| ✔ |
|Eszköz frissítése| ✔ |
|Modul frissítése| ✔ |
|Eszközök lekérdezése| ✔ |
|Eszközök exportálása| ✔ |
|Biztonsági mentés és visszaállítás – ADM| ✔ |
|Twin cseréje| ✔ |
|Biztonsági mentés és visszaállítás – DSS| ✔ |
|D2C-értesítés DesiredProperties| ✔ |
|D2C-javító ReportedProperties| ✔ |
|Dupla modul beolvasása| ✔ |
|A modul D2C Get Twin| ✔ |
|Modul beolvasása| ✔ |
|Modul D2C – értesítés DesiredProperties| ✔ |
|Modul D2C-javító ReportedProperties| ✔ |
|A modul közvetlen hívási metódusa| ✔ |
|Dupla frissítés| ✔ |
|Tömeges eszköz műveletei| ✔ |
|Eszközről a felhőbe telemetria| ✔ |
|Eszköz regisztrálása| ✔ |
|Modul regisztrálása| ✔ |
|Modul dupla cseréje| ✔ |
|GenericAuthentication| ✔ |
|Eszköz beolvasása| ✔ |
|Partíció áthelyezése/szerepkör módosítása| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>IoT Hub az Azure-felhőben és a IoT Hub a Azure Stack

| Szempont | IoT Hub a felhőben | IoT Hub a stacken |
|-|-|-|
| Üzenetek felhasználása | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |Alapértelmezés szerint az üzenetek a beépített szolgáltatás felé irányuló végponthoz (üzenetek/események) lesznek irányítva, amely kompatibilis a Event Hubsával. Az Azure-felhőben a végponton keresztül érheti el az üzeneteket IoT Hub kapcsolati karakterláncot vagy az Event hub kapcsolati karakterláncát. Azure Stack hub esetében azonban csak az Event hub kapcsolati karakterlánca támogatott. |

## <a name="next-steps"></a>Következő lépések

Az IoT Hub használatának megismeréséhez tekintse meg [Az Azure IoT hub dokumentációját](/azure/iot-hub/).

