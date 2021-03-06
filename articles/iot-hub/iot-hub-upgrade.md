---
title: Upgrade für Azure IoT Hub | Microsoft-Dokumentation
description: Ändern Sie den Tarif und die Skalierung für IoT Hub, um weitere Messaging- und Geräteverwaltungsfunktionen zu erhalten.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: d383d26b406c012b6b76225faf89f4b5dbd6bb9c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-upgrade-your-iot-hub"></a>Gewusst wie: Durchführen eines Upgrades für IoT Hub

Wächst Ihre IoT-Lösung und muss zentral hochskaliert werden, ist Azure IoT Hub zur Stelle. Azure IoT Hub bietet zwei Tarife, nämlich Basic (B) und Standard (S), für Kunden, die verschiedene Anforderungen an Features stellen. In jedem Tarif werden drei Größen (1, 2 und 3) angeboten, die die Anzahl der Nachrichten angeben, die jeden Tag gesendet werden können. 

Wenn Sie mehr Geräte haben und weitere Funktionen benötigen, haben Sie drei Möglichkeiten, um IoT Hub hinsichtlich Ihrer Anforderungen anzupassen:

* Fügen Sie Einheiten innerhalb von IoT Hub hinzu. Jede zusätzliche Einheit ermöglicht z.B. in einem IoT Hub im Tarif B1 zusätzlich 400.000 Nachrichten pro Tag. 
* Ändern Sie die Größe von IoT Hub. Migrieren Sie beispielsweise vom Tarif B1 zum Tarif B2, um die Menge der Nachrichten zu erhöhen, die jede Einheit pro Tag unterstützen kann.
* Führen Sie ein Upgrade auf einen höheren Tarif durch. Führen Sie beispielsweise ein Upgrade vom Tarif B1 auf den Tarif S1 durch, der die gleiche Nachrichtenkapazität, jedoch erweiterte Funktionen im Standard-Tarif aufweist.

All diese Änderungen können ohne Unterbrechung bestehender Vorgänge vorgenommen werden.

Wenn Sie Ihren IoT Hub herabstufen möchten, können Sie Einheiten entfernen und die Größe von IoT Hub verringern. Sie können jedoch nicht auf einen niedrigeren Tarif herabstufen. Beispielsweise können Sie vom Tarif S2 zum Tarif S1 wechseln, jedoch nicht vom Tarif S2 zum Tarif B1. 

Diese Beispiele sollen veranschaulichen, wie IoT Hub im Zuge von Änderungen in Ihrer Lösung angepasst werden kann. Spezifische Informationen zu den Funktionen in jedem Tarif finden Sie wie immer unter [Azure IoT Hub-Preise](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Durchführen eines Upgrades für einen vorhandenen IoT Hub 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem IoT Hub. 
2. Wählen Sie **Preise und Skalierung** aus. 

   ![Preise und Skalierung](./media/iot-hub-upgrade/pricing-scale.png)

3. Wählen Sie zum Ändern des Tarifs für Ihren Hub **Preise und Skalierung** aus. Wählen Sie den neuen Tarif aus, und klicken Sie auf **Auswählen**.

   ![Preise und Skalierung](./media/iot-hub-upgrade/select-tier.png)

4. Um die Anzahl der Einheiten in Ihrem Hub zu ändern, geben Sie einen neuen Wert unter **IoT Hub-Einheiten** ein. 
5. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern. 

Ihr IoT Hub ist jetzt angepasst, und Ihre Konfigurationen wurden geändert. 

## <a name="next-steps"></a>Nächste Schritte

Einzelheiten finden Sie unter [Wählen des richtigen IoT Hub-Tarifs für Ihre Lösung](iot-hub-scaling.md). 

