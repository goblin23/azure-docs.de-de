---
title: Azure Application Insights Snapshot-Debugger für .NET-Apps | Microsoft Docs
description: Debugmomentaufnahmen werden automatisch beim Auslösen von Ausnahmen in .NET-Produktions-Apps erfasst.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mbullwin; pharring
ms.openlocfilehash: 66339e5f5d2cc7447df0f8faf70d2d9fd45db738
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159134"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugmomentaufnahmen von Ausnahmen in .NET-Apps

Wenn eine Ausnahme auftritt, können Sie automatisch eine Debugmomentaufnahme von Ihrer aktiven Webanwendung erfassen. Die Momentaufnahme zeigt den Status des Quellcodes und der Variablen in dem Moment, in dem die Ausnahme ausgelöst wurde. Der Momentaufnahmedebugger (Vorschau) in [Azure Application Insights](app-insights-overview.md) überwacht die Ausnahmetelemetrie aus Ihrer Web-App. Er erfasst Momentaufnahmen Ihrer am häufigsten ausgelösten Ausnahmen, damit Sie die erforderlichen Informationen zur Diagnose von Problemen in der Produktion erhalten. Binden Sie das [NuGet-Paket des Momentaufnahmesammlers](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre Anwendung ein, und konfigurieren Sie optional Parameter für die Datensammlung in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Momentaufnahmen finden Sie im Application Insights-Portal unter [Ausnahmen](app-insights-asp-net-exceptions.md).

Sie können Debugmomentaufnahmen im Portal anzeigen, um die Aufrufliste anzuzeigen und die Variablen in jedem Aufruflistenrahmen zu überprüfen. Öffnen Sie zum Verbessern Ihrer Debugleistung mit Quellcode die Momentaufnahmen mit Visual Studio 2017 Enterprise, indem Sie [die Momentaufnahmedebugger-Erweiterung für Visual Studio herunterladen](https://aka.ms/snapshotdebugger). In Visual Studio können Sie auch [Andockpunkte festlegen, um interaktiv Momentaufnahmen zu erstellen](https://aka.ms/snappoint), ohne auf eine Ausnahme zu warten.

Die Momentaufnahmesammlung ist für folgende Anwendungen verfügbar:
* .NET Framework- und ASP.NET-Anwendungen, die mit .NET Framework 4.5 oder höher ausgeführt werden
* .NET Core 2.0- und ASP.NET Core 2.0-Anwendungen, die unter Windows ausgeführt werden

Die folgenden Umgebungen werden unterstützt:
* Azure App Service
* Azure Cloud Service mit Betriebssystemfamilie 4 oder höher
* Azure Service Fabric-Dienste unter Windows Server 2012 R2 oder höher
* Azure Virtual Machines mit Windows Server 2012 R2 oder höher
* Lokale virtuelle oder physische Computer, auf denen Windows Server 2012 R2 oder höher ausgeführt wird

> [!NOTE]
> Clientanwendungen (z.B. WPF, Windows Forms oder UWP) werden nicht unterstützt.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurieren der Momentaufnahmesammlung für ASP.NET-Anwendungen

1. Falls noch nicht geschehen, [aktivieren Sie Application Insights für Ihre Web-App](app-insights-asp-net.md).

2. Binden Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre App ein.

3. Überprüfen Sie die Standardoptionen, die zum Paket [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) hinzugefügt wurden:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Momentaufnahmen werden nur zu Ausnahmen erfasst, die Application Insights gemeldet wurden. In einigen Fällen (z.B. bei älteren Versionen der .NET-Plattform) müssen Sie möglicherweise die [Ausnahmesammlung konfigurieren](app-insights-asp-net-exceptions.md#exceptions), um Ausnahmen mit Momentaufnahmen im Portal zu sehen.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurieren der Momentaufnahmesammlung für ASP.NET Core 2.0-Anwendungen

1. Falls noch nicht geschehen, [aktivieren Sie Application Insights für Ihre ASP.NET Core-Web-App](app-insights-asp-net-core.md).

    > [!NOTE]
    > Stellen Sie sicher, dass Ihre Anwendung mindestens auf Version 2.1.1 des Microsoft.ApplicationInsights.AspNetCore-Pakets verweist.

2. Binden Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihre App ein.

3. Ändern Sie die `Startup`-Klasse Ihrer Anwendung, um den Telemetrieprozessor des Momentaufnahmesammlers hinzuzufügen und zu konfigurieren.

    Fügen Sie `Startup.cs` die folgenden using-Anweisungen hinzu.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Fügen Sie der `Startup`-Klasse die folgende `SnapshotCollectorTelemetryProcessorFactory`-Klasse hinzu.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Fügen Sie der Startpipeline die Dienste `SnapshotCollectorConfiguration` und `SnapshotCollectorTelemetryProcessorFactory` hinzu:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Konfigurieren Sie den Momentaufnahmesammler durch Hinzufügen eines SnapshotCollectorConfiguration-Abschnitts zur Datei „appsettings.json“. Beispiel: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurieren der Momentaufnahmesammlung für andere .NET-Anwendungen

1. Wenn Ihre Anwendung noch nicht mit Application Insights instrumentiert wurde, beginnen Sie mit dem [Aktivieren von Application Insights und Festlegen des Instrumentierungsschlüssels](app-insights-windows-desktop.md).

2. Fügen Sie das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in Ihrer App hinzu.

3. Momentaufnahmen werden nur zu Ausnahmen erfasst, die Application Insights gemeldet wurden. Sie müssen möglicherweise den Code ändern, um diese melden zu können. Der Code zur Behandlung von Ausnahmen hängt von der Struktur Ihrer Anwendung ab. Im Folgenden wird jedoch ein Beispiel gezeigt:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Besitzer des Azure-Abonnements können Momentaufnahmen überprüfen. Anderen Benutzern muss durch einen Besitzer eine entsprechende Berechtigung erteilt werden.

Weisen Sie hierzu den Benutzern, die Momentaufnahmen untersuchen, die Rolle `Application Insights Snapshot Debugger` zu. Abonnementbesitzer können diese Rolle einzelnen Benutzern oder Gruppen für die Application Insights-Zielressource oder für die dazugehörige Ressourcengruppe oder das dazugehörige Abonnement zuweisen.

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource.
1. Klicken Sie auf **Zugriffssteuerung (IAM)**.
1. Klicken Sie auf die Schaltfläche **+Hinzufügen**.
1. Wählen Sie in der Dropdownliste **Rollen** die Option **Application Insights-Momentaufnahmedebugger** aus.
1. Suchen Sie nach dem hinzuzufügenden Benutzer, und geben Sie einen Namen für ihn ein.
1. Klicken Sie auf die Schaltfläche **Speichern**, um den Benutzer der Rolle hinzuzufügen.


> [!IMPORTANT]
> In Variablen- und Parameterwerten von Momentaufnahmen können persönliche und andere vertrauliche Informationen enthalten sein.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Debuggen von Momentaufnahmen im Application Insights-Portal

Wenn eine Momentaufnahme für eine bestimmte Ausnahme oder eine Problem-ID verfügbar ist, wird unter der [Ausnahme](app-insights-asp-net-exceptions.md) im Application Insights-Portal eine Schaltfläche zum **Erstellen einer Debug-Momentaufnahme angezeigt.**

![Schaltfläche zum Erstellen einer Debug-Momentaufnahme für eine Ausnahme](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In der Ansicht der Debug-Momentaufnahme sehen Sie eine Aufrufliste und einen Variablenbereich. Wenn Sie Frames der Aufrufliste im Aufruflistenbereich auswählen, können Sie lokale Variablen und Parameter für diesen Funktionsaufruf im Variablenbereich anzeigen.

![Anzeigen der Debug-Momentaufnahme im Portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Momentaufnahmen enthalten möglicherweise vertrauliche Informationen und können standardmäßig nicht angezeigt werden. Zum Anzeigen von Momentaufnahmen muss Ihnen die Rolle `Application Insights Snapshot Debugger` zugewiesen sein.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Debuggen von Momentaufnahmen mit Visual Studio 2017 Enterprise
1. Klicken Sie auf die Schaltfläche **Momentaufnahme herunterladen**, um eine Datei vom Typ `.diagsession` herunterzuladen, die von Visual Studio 2017 Enterprise geöffnet werden kann.

2. Zum Öffnen der Datei vom Typ `.diagsession` müssen Sie zuerst [die Momentaufnahmedebugger-Erweiterung für Visual Studio herunterladen und installieren](https://aka.ms/snapshotdebugger).

3. Nach dem Öffnen der Momentaufnahmedatei erscheint in Visual Studio die Minidump-Debugging-Seite. Klicken Sie auf **Debug Managed Code** (Verwalteten Code debuggen), um mit dem Debuggen der Momentaufnahme zu beginnen. Die Momentaufnahme wird bei der Codezeile geöffnet, in der die Ausnahme ausgelöst wurde, damit Sie den aktuellen Zustand des Prozesses debuggen können.

    ![Anzeigen der Debugmomentaufnahme in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Die heruntergeladene Momentaufnahme enthält alle Symboldateien, die auf Ihrem Webanwendungsserver gefunden wurden. Diese Symboldateien sind zum Zuordnen von Quellcode und Momentaufnahmedaten erforderlich. Achten Sie bei App Service-Apps darauf, dass die Symbolbereitstellung aktiviert ist, wenn Sie Ihre Web-Apps veröffentlichen.

## <a name="how-snapshots-work"></a>Funktionsweise von Momentaufnahmen

Der Snapshot Collector wird als [Application Insights-Telemetrieprozessor](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet) implementiert. Wenn die Anwendung ausgeführt wird, wird der Snapshot Collector-Telemetrieprozessor der Telemetriepipeline Ihrer Anwendung hinzugefügt.
Bei jedem Aufruf von [TrackException](app-insights-asp-net-exceptions.md#exceptions) durch Ihre Anwendung berechnet der Snapshot Collector auf der Grundlage der Art der ausgelösten Ausnahme und der auslösenden Methode eine Problem-ID.
Bei jedem Aufruf von „TrackException“ durch Ihre Anwendung erhöht sich der Zähler für die entsprechende Problem-ID. Wenn der Zähler den Wert `ThresholdForSnapshotting` erreicht, wird die Problem-ID einem Sammlungsplan hinzugefügt.

Der Snapshot Collector abonniert auch das Ereignis [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception), um ausgelöste Ausnahmen zu überwachen. Wenn dieses Ereignis ausgelöst wird, wird die Problem-ID der Ausnahme berechnet und mit den Problem-IDs im Sammlungsplan verglichen.
Ist eine Entsprechung vorhanden, wird eine Momentaufnahme des ausgeführten Prozesses erstellt. Der Momentaufnahme wird ein eindeutiger Bezeichner zugewiesen, und die Ausnahme wird mit diesem Bezeichner gekennzeichnet. Nach Ausführung des Handlers „FirstChanceException“ wird die ausgelöste Ausnahme ganz normal verarbeitet. Letztendlich erreicht die Ausnahme wieder die Methode „TrackException“ und wird zusammen mit dem Momentaufnahmebezeichner an Application Insights gemeldet.

Der Hauptprozess wird mit minimaler Unterbrechung weiter ausgeführt und stellt weiter Datenverkehr für Benutzer bereit. In der Zwischenzeit wird die Momentaufnahme an den Snapshot Uploader-Prozess übergeben. Der Snapshot Uploader erstellt einen Minidump mit allen relevanten Symboldateien (PDB-Dateien) und lädt ihn in Application Insights hoch.

> [!TIP]
> - Bei einer Prozessmomentaufnahme handelt es sich um einen angehaltenen Klon des ausgeführten Prozesses.
> - Die Erstellung der Momentaufnahme dauert 10 bis 20 Millisekunden.
> - Der Standardwert für `ThresholdForSnapshotting` ist „1“. Das ist gleichzeitig auch der Mindestwert. Ihre App muss die gleiche Ausnahme also **zweimal** auslösen, bevor eine Momentaufnahme erstellt wird.
> - Legen Sie `IsEnabledInDeveloperMode` auf „true“ fest, wenn Sie beim Debuggen in Visual Studio Momentaufnahmen generieren möchten.
> - Die Rate der Momentaufnahmenerstellung wird durch die Einstellung `SnapshotsPerTenMinutesLimit` begrenzt. Standardmäßig ist das Limit auf eine einzelne Momentaufnahme pro zehn Minuten festgelegt.
> - Pro Tag können maximal 50 Momentaufnahmen hochgeladen werden.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

### <a name="publish-symbols"></a>Veröffentlichen von Symbolen
Für den Momentaufnahmedebugger müssen Symboldateien auf dem Produktionsserver vorhanden sein, um Variablen zu decodieren und eine gute Debugleistung in Visual Studio zu erzielen. Die Version 15.2 von Visual Studio 2017 veröffentlicht Symbole für Releasebuilds standardmäßig im Rahmen der Veröffentlichung für App Service. In Vorgängerversionen müssen Sie der Veröffentlichungsprofildatei vom Typ `.pubxml` die folgende Zeile hinzufügen, um Symbole im Releasemodus zu veröffentlichen:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Stellen Sie für Azure Compute und andere Typen sicher, dass die Symboldateien im selben Ordner wie die DLL-Datei der Hauptanwendung (in der Regel `wwwroot/bin`) liegen oder unter dem aktuellen Pfad verfügbar sind.

### <a name="optimized-builds"></a>Optimierte Builds
In einigen Fällen können lokale Variablen aufgrund von Optimierungen, die durch den JIT-Compiler angewendet werden, nicht in Releasebuilds angezeigt werden.
In Azure App Services kann der Snapshot Collector allerdings Auslösemethoden deoptimieren, die Teil des entsprechenden Sammlungsplans sind.

> [!TIP]
> Installieren Sie in Ihrer App Service-Instanz die Application Insights-Websiteerweiterung, um Deoptimierungsunterstützung zu erhalten.

## <a name="troubleshooting"></a>Problembehandlung

Die folgenden Tipps unterstützen bei der Behandlung von Problemen mit dem Momentaufnahmedebugger.

### <a name="use-the-snapshot-health-check"></a>Verwenden der Integritätsprüfung für Momentaufnahmen
Einige verbreitete Probleme führen dazu, dass „Debugmomentaufnahme öffnen“ nicht angezeigt wird. Beispiele wären etwa die Verwendung eines veralteten Snapshot Collectors, das Erreichen des täglichen Uploadlimits oder ein zeitaufwendiger Uploadvorgang für die Momentaufnahme. Verwenden Sie die Integritätsprüfung für Momentaufnahmen, um verbreitete Probleme zu behandeln.

Der Ausnahmenbereich der End-to-End-Überwachungsansicht enthält einen Link, über den Sie zur Integritätsprüfung für Momentaufnahmen gelangen.

![Aufrufen der Integritätsprüfung für Momentaufnahmen](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Die interaktive, chatähnliche Oberfläche sucht nach verbreiteten Problemen und unterstützt Sie bei der Behebung.

![Integritätsprüfung](./media/app-insights-snapshot-debugger/healthcheck.png)

Sollte sich das Problem dadurch nicht beheben lassen, lesen Sie die folgenden Schritte zur manuellen Problembehandlung:

### <a name="verify-the-instrumentation-key"></a>Überprüfen des Instrumentierungsschlüssels

Vergewissern Sie sich, dass Sie in Ihrer veröffentlichten Anwendung den richtigen Instrumentierungsschlüssel verwenden. Der Instrumentierungsschlüssel wird in der Regel aus der Datei „ApplicationInsights.config“ gelesen. Vergewissern Sie sich, dass der Wert dem Instrumentierungsschlüssel für die im Portal angezeigte Application Insights-Ressource entspricht.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgraden auf die neueste Version des NuGet-Pakets

Stellen Sie mithilfe des NuGet-Paket-Managers von Visual Studio sicher, dass Sie die neueste Version von „Microsoft.ApplicationInsights.SnapshotCollector“ verwenden. Versionsanmerkungen finden Sie unter https://github.com/Microsoft/ApplicationInsights-Home/issues/167.

### <a name="check-the-uploader-logs"></a>Überprüfen der Uploaderprotokolle

Nachdem eine Momentaufnahme erstellt wurde, wird eine Minidumpdatei (DMP) auf dem Datenträger erstellt. Diese Minidumpdatei wird durch einen separaten Uploaderprozess erstellt und zusammen mit allen dazugehörigen PDB-Dateien in den Speicher des Application Insights-Momentaufnahmedebuggers hochgeladen. Nachdem die Minidumpdatei erfolgreich hochgeladen wurde, wird sie vom Datenträger gelöscht. Die Protokolldateien für den Uploaderprozess bleiben auf dem Datenträger erhalten. In einer App Service-Umgebung finden Sie diese Protokolle unter `D:\Home\LogFiles`. Verwenden Sie die Verwaltungswebsite von Kudu für App Service, um nach diesen Protokolldateien zu suchen.

1. Öffnen Sie im Azure-Portal Ihre App Service-Anwendung.
2. Klicken Sie auf **Erweiterte Tools**, oder suchen Sie nach **Kudu**.
3. Klicken Sie auf **Start**.
4. Wählen Sie im Dropdown-Listenfeld **Debugging-Konsole** die Option **CMD** aus.
5. Klicken Sie auf **LogFiles**.

Daraufhin sollte mindestens eine Datei mit einem Namen angezeigt werden, der mit `Uploader_` oder `SnapshotUploader_` beginnt und die Erweiterung `.log` aufweist. Klicken Sie auf das entsprechende Symbol, um Protokolldateien herunterzuladen oder in einem Browser zu öffnen.
Der Dateiname enthält ein eindeutiges Suffix, mit dem die App Service-Instanz identifiziert wird. Wenn Ihre App Service-Instanz auf mehreren Computern gehostet wird, werden separate Protokolldateien für jeden Computer erstellt. Wenn der Uploader eine neue Minidumpdatei erkennt, wird diese in der Protokolldatei erfasst. Hier ist ein Beispiel für eine erfolgreiche Momentaufnahme und einen Upload angegeben:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Das obige Beispiel stammt aus Version 1.2.0 des NuGet-Pakets „Microsoft.ApplicationInsights.SnapshotCollector“. In früheren Versionen hatte der Uploader-Prozess die Bezeichnung `MinidumpUploader.exe`, und das Protokoll enthielt weniger Details.

Im vorherigen Beispiel lautet der Instrumentierungsschlüssel `c12a605e73c44346a984e00000000000`. Dieser Wert sollte dem Instrumentierungsschlüssel für Ihre Anwendung entsprechen.
Die Minidumpdatei ist einer Momentaufnahme mit der ID `139e411a23934dc0b9ea08a626db16c5` zugeordnet. Sie können diese ID später verwenden, um nach der zugehörigen Ausnahmetelemetrie in Application Insights Analytics zu suchen.

Der Uploader sucht alle 15 Minuten nach neuen PDB-Dateien. Hier sehen Sie ein Beispiel:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Bei Anwendungen, die _nicht_ in App Service gehostet werden, befinden sich die Uploaderprotokolle im selben Ordner wie die Minidumpdateien: `%TEMP%\Dumps\<ikey>` (`<ikey>` steht hierbei für Ihren Instrumentierungsschlüssel).

### <a name="troubleshooting-cloud-services"></a>Problembehandlung bei Cloud Services
Für Rollen in Cloud Services ist der standardmäßige temporäre Ordner möglicherweise zu klein, um die MiniDump-Dateien zu speichern, was dazu führt, dass Momentaufnahmen verloren gehen.
Der erforderliche Speicherplatz hängt vom gesamten Arbeitssatz Ihrer Anwendung und der Anzahl gleichzeitiger Momentaufnahmen ab.
Der Arbeitssatz einer 32-Bit-ASP.NET-Webrolle liegt in der Regel zwischen 200 MB und 500 MB.
Lassen Sie mindestens zwei gleichzeitige Momentaufnahmen zu.
Wenn Ihre Anwendung beispielsweise 1 GB des gesamten Arbeitssatzes verwendet, sollten Sie sicherstellen, dass ein Speicherplatz von mindestens 2 GB zum Speichern von Momentaufnahmen zur Verfügung steht.
Führen Sie die folgenden Schritte aus, um Ihre Clouddienstrolle mit einer dedizierten lokalen Ressource für Momentaufnahmen zu konfigurieren.

1. Fügen Sie Ihrem Clouddienst eine neue lokale Ressource hinzu, indem Sie die Clouddienst-Definitionsdatei (.csdef) bearbeiten. Im folgenden Beispiel wird eine Ressource namens `SnapshotStore` mit einer Größe von 5 GB definiert.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändern Sie den Startcode Ihrer Rolle, um eine Umgebungsvariable hinzuzufügen, die auf die lokale Ressource `SnapshotStore` zeigt. Für Workerrollen sollte der Code der `OnStart`-Methode Ihrer Rolle hinzugefügt werden:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Für Webrollen (ASP.NET) sollte der Code der `Application_Start`-Methode Ihrer Webanwendung hinzugefügt werden:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aktualisieren Sie die Datei „ApplicationInsights.config“ Ihrer Rolle, um den von `SnapshotCollector` verwendeten Speicherort des temporären Ordners zu überschreiben.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Suchen nach Ausnahmen mit Momentaufnahmen über die Application Insights-Suche

Wenn eine Momentaufnahme erstellt wird, wird die auslösende Ausnahme mit einer Momentaufnahme-ID gekennzeichnet. Diese Momentaufnahme-ID ist als benutzerdefinierte Eigenschaft enthalten, wenn die Ausnahmetelemetrie an Application Insights gemeldet wird. In Application Insights können Sie über die **Suche** nach sämtlicher Telemetrie mit der benutzerdefinierten Eigenschaft `ai.snapshot.id` suchen.

1. Navigieren Sie im Azure-Portal zu Ihrer Application Insights-Ressource.
2. Klicken Sie auf **Suchen**.
3. Geben Sie in das Textfeld „Suchen“ `ai.snapshot.id` ein, und drücken Sie die Eingabetaste.

![Suchen nach Telemetrie im Portal mit einer Momentaufnahme-ID](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Wenn bei dieser Suche keine Ergebnisse zurückgegeben werden, wurden Application Insights im ausgewählten Zeitraum keine Momentaufnahmen für Ihre Anwendung gemeldet.

Um über die Uploaderprotokolle nach einer bestimmten Momentaufnahme-ID zu suchen, geben Sie die jeweilige ID in das Suchfeld ein. Wenn Sie für eine Momentaufnahme, die Ihres Wissens nach hochgeladen wurde, keine Telemetrie finden können, gehen Sie folgendermaßen vor:

1. Vergewissern Sie sich, dass Sie die Suche in der richtigen Application Insights-Ressource durchführen, indem Sie den Instrumentierungsschlüssel überprüfen.

2. Passen Sie mithilfe des Zeitstempels aus dem Uploaderprotokoll den Filter „Zeitbereich“ an, um den jeweiligen Zeitbereich bei der Suche zu berücksichtigen.

Wenn trotzdem keine Ausnahme mit dieser Momentaufnahme-ID angezeigt wird, wurde Application Insights die Ausnahmetelemetrie nicht gemeldet. Diese Situation kann vorkommen, wenn Ihre Anwendung abgestürzt ist, nachdem die Momentaufnahme erfasst wurde, jedoch bevor die Ausnahmetelemetrie gemeldet wurde. Überprüfen Sie in diesem Fall die App Service-Protokolle unter `Diagnose and solve problems`, um festzustellen, ob unerwartete Neustarts oder Ausnahmefehler vorliegen.

### <a name="edit-network-proxy-or-firewall-rules"></a>Bearbeiten von Netzwerkproxy- oder Firewallregeln

Wenn Ihre Anwendung über einen Proxy oder über eine Firewall mit dem Internet verbunden ist, müssen Sie ggf. die Regeln bearbeiten, damit Ihre Anwendung mit dem Momentaufnahmedebugger-Dienst kommunizieren kann. Eine Liste mit IP-Adressen und Ports, die vom Momentaufnahmedebugger verwendet werden, finden Sie [hier](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Nächste Schritte

* [Legen Sie Fangpunkte in Ihrem Code fest](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications), um Momentaufnahmen abzurufen, ohne auf eine Ausnahme warten zu müssen.
* Unter [Diagnostizieren von Ausnahmen in Ihren Web-Apps](app-insights-asp-net-exceptions.md) erfahren Sie, wie Sie weitere Ausnahmen für Application Insights sichtbar machen.
* Die [intelligente Erkennung](app-insights-proactive-diagnostics.md) ermittelt automatisch Leistungsanomalien.
