---
title: Azure Application Insights-Nutzungskohorten | Microsoft-Dokumentation
description: Analysieren unterschiedlicher Gruppen von Benutzern, Sitzungen, Ereignissen oder Vorgängen, die eine Gemeinsamkeit aufweisen
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193797"
---
# <a name="application-insights-cohorts"></a>Application Insights-Kohorten

Eine Kohorte ist eine Gruppe von Benutzern, Sitzungen, Ereignissen oder Vorgängen, die eine Gemeinsamkeit aufweisen. In Azure Application Insights werden Kohorten durch eine Analyseabfrage definiert. Für den Fall, dass Sie eine bestimmte Gruppe von Benutzern oder Ereignissen wiederholt analysieren müssen, bieten Ihnen Kohorten mehr Flexibilität, um genau die gewünschte Gruppe anzugeben.

![Bereich „Kohorten“](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorten im Vergleich zu einfachen Filtern

Kohorten werden auf ähnliche Weise wie Filter verwendet. Aber die Definitionen von Kohorten werden aus benutzerdefinierten Analyseabfragen erstellt, sodass sie viel anpassungsfähiger und komplexer sind. Im Unterschied zu Filtern können Sie Kohorten speichern, sodass andere Mitglieder Ihres Teams sie wiederverwenden können.

Sie können beispielsweise eine Kohorte aus Benutzern definieren, die alle ein neues Feature in Ihrer App getestet haben. Sie können diese Kohorte in Ihrer Application Insights-Ressource speichern. Diese gespeicherte Gruppe bestimmter Benutzer kann später einfach analysiert werden.

> [!NOTE]
> Nach ihrer Erstellung sind Kohorten in den Tools für Benutzer, Sitzungen, Ereignisse und Benutzerabläufe verfügbar.

## <a name="example-engaged-users"></a>Beispiel: aktive Benutzer

Ihr Team definiert jede Person als „aktiven Benutzer“, die Ihre App in einem bestimmten Monat mindestens fünfmal verwendet. In diesem Abschnitt definieren Sie eine Kohorte dieser aktiven Benutzer.

1. Öffnen Sie das Tool „Cohorts“ (Kohorten).

2. Wählen Sie die Registerkarte **Template Gallery** (Vorlagenkatalog) aus. Sie sehen eine Sammlung von Vorlagen für verschiedene Kohorten.

3. Wählen Sie **Engaged Users – by Days Used** (Aktive Benutzer – nach Verwendungstagen) aus.

    Für diese Kohorte sind drei Parameter verfügbar:
    * **Activities** (Aktivitäten): Hier können Sie auswählen, welche Ereignisse und Seitenansichten als „Verwendung“ gezählt werden sollen.
    * **Period** (Zeitraum): Dies ist die Definition eines Monats.
    * **UsedAtleastCustom** (MindestensVerwendetBenutzerdef): Dies ist die Häufigkeit, mit der Benutzer etwas innerhalb eines Zeitraums verwenden müssen, um als aktive Benutzer zu zählen.

4. Ändern Sie **UsedAtleastCustom** in **5+ days** (5+ Tage), und behalten Sie für **Period** (Zeitraum) den Standardwert von 28 Tagen bei.

    ![Aktive Benutzer](.\media\app-insights-usage-cohorts\003.png)

    Diese Kohorte stellt nun alle Benutzer-IDs dar, die mit einem benutzerdefinierten Ereignis oder einer benutzerdefinierten Seitenansicht an 5 verschiedenen Tagen in den letzten 28 Tagen gesendet wurden.

5. Wählen Sie **Speichern**aus.

   > [!TIP]
   >  Geben Sie der Kohorte einen Namen, z. B. „Engaged Users (5+ Days)“. Speichern Sie die Kohorte in „My reports“ (Meine Berichte) oder „Shared reports“ (Freigegebene Berichte), je nachdem, ob andere Personen, die Zugriff auf diese Application Insights-Ressource haben, die Möglichkeit haben sollen, diese Kohorte anzuzeigen.

6. Wählen Sie **Back to Gallery** (Zurück zum Katalog) aus.

### <a name="what-can-you-do-by-using-this-cohort"></a>Wozu können Sie diese Kohorte nutzen?

Öffnen Sie das Tool „Users“ (Benutzer). Wählen Sie in der Dropdownliste **Show** (Anzeigen) die Kohorte aus, die Sie unter **Engaged Users (5+ Days)** erstellt haben.

Das Tool „Benutzer“ wird nach dieser Kohorte von Benutzern gefiltert:

![Bereich „Benutzer“, gefiltert nach einer bestimmten Kohorte](.\media\app-insights-usage-cohorts\004.png)

Folgende wichtige Punkte sind zu berücksichtigen:
* Sie können diese Gruppe nicht über normale Filter erstellen. Die Datenzugriffslogik ist komplexer.
* Sie können diese Kohorte weiter filtern, indem Sie die normalen Filtern im Tool „Benutzer“ verwenden. Obwohl die Kohorte mit Zeitfenstern von 28 Tagen definiert ist, können Sie den Zeitbereich im Tool „Benutzer“ trotzdem auf 30, 60 oder 90 Tage anpassen.

Diese Filter unterstützen komplexere Fragen, die sich nicht mit dem Abfrage-Generator formulieren lassen. Ein Beispiel ist _Personen, die an der letzten 28 Tage aktiv waren. Wie haben sich dieselben Personen in den letzten 60 Tagen verhalten?_

## <a name="example-events-cohort"></a>Beispiel: Ereigniskohorte

Sie können auch Kohorten von Ereignissen erstellen. In diesem Abschnitt definieren Sie eine Kohorte der Ereignisse und Seitenansichten. Anschließend wird gezeigt, wie Sie die Kohorten in den anderen Tools verwenden können. In dieser Kohorte könnte eine Gruppe von Ereignissen, die Ihr Team als _aktive Verwendung_ einstuft, oder eine Gruppe definiert sein, die mit einem bestimmten neuen Feature zu tun hat.

1. Öffnen Sie das Tool „Cohorts“ (Kohorten).

2. Wählen Sie die Registerkarte **Template Gallery** (Vorlagenkatalog) aus. Sie sehen eine Sammlung von Vorlagen für verschiedene Kohorten.

3. Wählen Sie **Events picker** (Ereignisauswahl) aus.

    ![Screenshot von „Events picker“ (Ereignisauswahl)](.\media\app-insights-usage-cohorts\006.png)

4. Wählen Sie in der Dropdownliste **Activities** (Aktivitäten) die Ereignisse aus, die in der Kohorte enthalten sein sollen.

5. Geben Sie der Kohorte einen Namen, und speichern Sie sie.

## <a name="example-active-users-where-you-modify-a-query"></a>Beispiel: Aktive Benutzer, für die Sie eine Abfrage ändern

Die beiden vorherigen Kohorten wurden durch Verwenden von Dropdownlisten definiert. Sie können Kohorten aber auch definieren, indem Sie, um vollkommen flexibel zu sein, Analyseabfragen verwenden. Zur Veranschaulichung erstellen Sie eine Kohorte von Benutzern aus Großbritannien.

![Animierte Grafik zur Verwendung des Tools „Kohorten“](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Öffnen Sie das Tool „Kohorten“, wählen Sie die Registerkarte **Template Gallery** (Vorlagenkatalog) aus, und wählen Sie **Blank Users cohort** (Leere Kohorte für Benutzer) aus.

    ![Blank users cohort (Leere Kohorte für Benutzer)](.\media\app-insights-usage-cohorts\001.png)

    Drei Abschnitte stehen zur Verfügung:
    * Ein Abschnitt für Markdown-Text, in dem Sie die Kohorte ausführlicher für andere Teammitglieder beschreiben.

    * Ein Abschnitt für Parameter, in dem Sie eigene Parameter festlegen können, z. B. **Activities** und andere Dropdownlisten aus den beiden vorherigen Beispielen.

    * Ein Abschnitt für Abfragen, in dem Sie die Kohorte definieren, indem Sie eine Analyseabfrage verwenden.

    Im Abschnitt für Abfragen [schreiben Sie eine Analyseabfrage](https://docs.loganalytics.io/index). In der Abfrage wird genau die Gruppe von Zeilen ausgewählt, die die Kohorte beschreiben, die Sie definieren möchten. Im Tool „Kohorten“ wird dann der Abfrage implizit die Klausel „| summarize by user_Id“ hinzugefügt. Diese Daten werden als Vorschau in einer Tabelle unterhalb der Abfrage angezeigt, sodass Sie überprüfen können, ob die Abfrage Ergebnisse zurückgibt.

    > [!NOTE]
    > Wenn die Abfrage nicht zu sehen ist, vergrößern Sie den Abschnitt, sodass die Abfrage angezeigt wird. In der animierten GIF-Datei am Anfang dieses Abschnitts wird das Verhalten bei der Größenanpassung veranschaulicht.

2. Kopieren Sie den folgenden Text, und fügen Sie ihn im Abfrage-Editor ein:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Wählen Sie **Run Query** (Abfrage ausführen) aus. Werden in der Tabelle keine Benutzer-IDs angezeigt, wechseln Sie zu einem Land, in dem Ihre Anwendung Benutzer hat.

4. Geben Sie der Kohorte einen Namen, und speichern Sie sie.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

_Ich habe eine Kohorte von Benutzern aus einem bestimmten Land definiert. Wenn ich diese Kohorte im Tool „Benutzer“ mit dem Ergebnis eines einfachen Festlegen eines Filters für dieses Land vergleiche, werden unterschiedliche Ergebnisse angezeigt. Warum?_

Kohorten und Filter unterscheiden sich. Angenommen, Sie haben eine Kohorte von Benutzern aus Großbritannien (definiert wie im vorherigen Beispiel), und Sie vergleichen deren Ergebnisse mit dem Festlegen des Filters „Country or region = United Kingdom“.

* Für die Kohortenversion werden alle Ereignisse von Benutzern angezeigt, die im aktuellen Zeitbereich mindestens ein Ereignis aus Großbritannien gesendet haben. Wenn Sie nach Land oder Region aufteilen, werden wahrscheinlich viele Länder und Regionen angezeigt.
* Für die Filterversion werden nur Ereignisse aus Großbritannien angezeigt. Selbst wenn Sie nach Land oder Region aufteilen, wird ausschließlich Großbritannien angezeigt.

## <a name="learn-more"></a>Weitere Informationen
- [Analytics-Abfragesprache](https://go.microsoft.com/fwlink/?linkid=856587)
- [Benutzer, Sitzungen, Ereignisse](app-insights-usage-segmentation.md)
- [Benutzerabläufe](app-insights-usage-flows.md)
- [Nutzungsübersicht](app-insights-usage-overview.md)