---
title: Azure Queue Storage-Bindungen für Azure Functions
description: Hier erfahren Sie, wie Sie den Azure Queue Storage-Trigger und die entsprechende Ausgabebindung in Azure Functions verwenden.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: tdykstra
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1ca2b821678b48f65fe6ec6e58fa65cd8e4304f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303412"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue Storage-Bindungen für Azure Functions

In diesem Artikel erfahren Sie, wie Sie Azure Queue Storage-Bindungen in Azure Functions verwenden. Azure Functions unterstützt Trigger- und Ausgabebindungen für Warteschlangen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pakete

Die Warteschlangenspeicher-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="trigger"></a>Trigger

Verwenden Sie den Warteschlangentrigger, um eine Funktion zu starten, wenn bei einer Warteschlange ein neues Element eingeht. Die Warteschlangennachricht wird als Eingabe für die Funktion bereitgestellt.

## <a name="trigger---example"></a>Trigger: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#trigger---c-example)
* [C#-Skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger: C#-Beispiel

Die [C#-Funktion](functions-dotnet-class-library.md) des folgenden Beispiels fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein neues Warteschlangenelement verarbeitet wird.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Trigger: C#-Skriptbeispiel

Das folgende Beispiel enthält eine Warteschlangentrigger-Bindung in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem die Bindung verwendet wird. Die Funktion fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein Warteschlangenelement verarbeitet wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#trigger---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

Im Abschnitt [Verwendung](#trigger---usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#trigger---message-metadata) erläutert.

### <a name="trigger---javascript-example"></a>Trigger: JavaScript-Beispiel

Das folgende Beispiel enthält eine Warteschlangentrigger-Bindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion fragt die Warteschlange `myqueue-items` ab und schreibt ein Protokoll, sobald ein Warteschlangenelement verarbeitet wird.

Die Datei *function.json* sieht wie folgt aus:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#trigger---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

Im Abschnitt [Verwendung](#trigger---usage) finden Sie weitere Informationen zu `myQueueItem` (benannt durch die Eigenschaft `name` in „function.json“).  Alle anderen gezeigten Variablen werden im Abschnitt [Nachrichtenmetadaten](#trigger---message-metadata) erläutert.

## <a name="trigger---attributes"></a>Trigger: Attribute
 
Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) die folgenden Attribute, um einen Warteschlangentrigger zu konfigurieren:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Der Konstruktor des Attributs akzeptiert den Namen der zu überwachenden Warteschlange, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Ein vollständiges Beispiel finden Sie unter [Trigger: C#-Beispiel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Eine weitere Möglichkeit zum Angeben des zu verwendenden Speicherkontos. Der Konstruktor akzeptiert den Namen einer App-Einstellung mit einer Speicherverbindungszeichenfolge. Das Attribut kann auf Parameter-, Methoden- oder Klassenebene angewendet werden. Das folgende Beispiel zeigt die Anwendung auf Klassen- und Methodenebene:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Das zu verwendende Speicherkonto wird anhand von Folgendem bestimmt (in der angegebenen Reihenfolge):

* Die Eigenschaft `Connection` des Attributs `QueueTrigger`.
* Das Attribut `StorageAccount`, das auf den gleichen Parameter angewendet wird wie das Attribut `QueueTrigger`.
* Das Attribut `StorageAccount`, das auf die Funktion angewendet wird.
* Das Attribut `StorageAccount`, das auf die Klasse angewendet wird.
* Die App-Einstellung „AzureWebJobsStorage“.

## <a name="trigger---configuration"></a>Trigger: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `QueueTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | –| Muss auf `queueTrigger` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction**| – | Nur in der Datei *function.json*. Muss auf `in` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – |Der Name der Variablen, die die Warteschlange im Funktionscode darstellt.  | 
|**queueName** | **QueueName**| Der Name der abzufragenden Warteschlange. | 
|**Verbindung** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger: Verwendung
 
Verwenden Sie in C# und C#-Skripts einen Methodenparameter wie `string paramName`, um auf die Nachrichtendaten zuzugreifen. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Eine Bindung kann mit folgenden Typen erstellt werden:

* Objekt – Die Functions-Runtime deserialisiert eine JSON-Nutzlast in eine Instanz einer beliebigen Klasse, die in Ihrem Code definiert ist. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Verwenden Sie in JavaScript `context.bindings.<name>`, um auf die Nutzlast des Warteschlangenelements zuzugreifen. Falls es sich um eine JSON-Nutzlast handelt, wird sie in ein Objekt deserialisiert.

## <a name="trigger---message-metadata"></a>Trigger: Nachrichtenmetadaten

Der Warteschlangentrigger stellt mehrere [Metadateneigenschaften](functions-triggers-bindings.md#binding-expressions---trigger-metadata) bereit. Diese Eigenschaften können als Teil der Bindungsausdrücke in anderen Bindungen oder als Parameter im Code verwendet werden. Im Folgenden werden Eigenschaften der [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage)-Klasse aufgeführt:

|Eigenschaft|Typ|BESCHREIBUNG|
|--------|----|-----------|
|`QueueTrigger`|`string`|Die Warteschlangennutzlast (bei einer gültigen Zeichenfolge). Handelt es sich bei der Warteschlangennutzlast um eine Zeichenfolge, hat `QueueTrigger` den gleichen Wert wie die Variable, die durch die Eigenschaft `name` in *function.json* benannt wird.|
|`DequeueCount`|`int`|Gibt an, wie oft diese Nachricht aus der Warteschlange entfernt wurde.|
|`ExpirationTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht abläuft.|
|`Id`|`string`|ID der Warteschlangennachricht|
|`InsertionTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht der Warteschlange hinzugefügt wurde.|
|`NextVisibleTime`|`DateTimeOffset`|Die Zeit, zu der die Nachricht als Nächstes sichtbar wird.|
|`PopReceipt`|`string`|Die POP-Bestätigung der Nachricht.|

## <a name="trigger---poison-messages"></a>Trigger: Nicht verarbeitbare Nachrichten

Falls eine Funktion des Warteschlangentriggers nicht erfolgreich ausgeführt werden kann, versucht Azure Functions für eine bestimmte Warteschlangennachricht bis zu fünf Mal (einschließlich des ersten Versuchs), die Funktion auszuführen. Sind alle fünf Versuche nicht erfolgreich, fügt die Functions-Laufzeit einer Warteschlange namens *&lt;Name der Originalwarteschlange>-poison* eine Nachricht hinzu. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist.

Überprüfen Sie zur manuellen Behandlung nicht verarbeitbarer Nachrichten den Wert [dequeueCount](#trigger---message-metadata) der Warteschlangennachricht.

## <a name="trigger---polling-algorithm"></a>Trigger: Abrufalgorithmus

Der Warteschlangentrigger implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet die Runtime zwei Sekunden und prüft dann, ob eine andere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet sie ungefähr vier Sekunden vor dem erneuten Versuch. Nach aufeinander folgenden fehlgeschlagenen Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit, standardmäßig eine Minute, erreicht ist. Die maximale Wartezeit kann über die `maxPollingInterval`-Eigenschaft in der Datei [host.json](functions-host-json.md#queues) konfiguriert werden.

## <a name="trigger---concurrency"></a>Trigger: Parallelität

Wenn mehrere Warteschlangennachrichten warten, ruft der Warteschlangentrigger einen Batch mit Nachrichten ab und ruft parallel Funktionsinstanzen zur Verarbeitung auf. Standardmäßig ist die Batchgröße 16. Wenn die zu verarbeitende Anzahl 8 erreicht, ruft die Runtime einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion auf einem virtuellen Computer verarbeiteten parallelen Nachrichten 24. Dieser Grenzwert gilt separat für jede durch Warteschlangen ausgelöste Funktion auf jedem virtuellen Computer. Wenn die Funktions-App horizontal auf mehrere virtuelle Computer hochskaliert wird, wartet jeder virtuelle Computer auf Trigger und versucht, Funktionen auszuführen. Beispiel: Wenn eine Funktions-App auf drei virtuelle Computer horizontal hochskaliert wird, beträgt die standardmäßige maximale Anzahl von parallelen Instanzen einer durch Warteschlangen ausgelösten Funktion 72.

Die Batchgröße und der Schwellenwert für das Abrufen eines neuen Batches können in der Datei [host.json](functions-host-json.md#queues) konfiguriert werden. Sie können die Batchgröße auf 1 festlegen, wenn Sie die parallele Ausführung von durch Warteschlangen ausgelösten Funktionen in einer Funktions-App minimieren möchten. Diese Einstellung verhindert Parallelität nur so lange, wie Ihre Funktions-App auf einem einzelnen virtuellen Computer ausgeführt wird. 

Mit dem Warteschlangentrigger wird automatisch verhindert, dass eine Funktion Warteschlangennachrichten mehrfach verarbeitet. Daher müssen keine idempotenten Funktionen geschrieben werden.

## <a name="trigger---hostjson-properties"></a>Trigger: Eigenschaften von „host.json“

Die Datei [host.json](functions-host-json.md#queues) enthält Einstellungen, mit denen das Verhalten des Warteschlangentriggers gesteuert werden kann.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Output

Verwenden Sie die Azure Queue Storage-Ausgabebindung, um Nachrichten in eine Warteschlange zu schreiben.

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

Die [C#-Funktion](functions-dotnet-class-library.md) des folgenden Beispiels erstellt eine Warteschlangennachricht für jede empfangene HTTP-Anforderung.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

Das folgende Beispiel enthält eine HTTP-Triggerbindung in einer Datei vom Typ *function.json* sowie Code vom Typ [C#-Skript (.csx)](functions-reference-csharp.md), in dem die Bindung verwendet wird. Die Funktion erstellt ein Warteschlangenelement mit einer **CustomQueueMessage**-Objektnutzlast für jede empfangene HTTP-Anforderung.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der folgende C#-Skriptcode erstellt eine einzelne Warteschlangennachricht:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Mithilfe eines Parameters vom Typ `ICollector` oder `IAsyncCollector` können mehrere Nachrichten gleichzeitig gesendet werden. Der folgende C#-Skriptcode sendet mehrere Nachrichten – eine mit den HTTP-Anforderungsdaten und eine mit hartcodierten Werten:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    TraceWriter log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

Das folgende Beispiel enthält eine HTTP-Triggerbindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion erstellt ein Warteschlangenelement für jede empfangene HTTP-Anforderung.

Die Datei *function.json* sieht wie folgt aus:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Wenn Sie mehrere Nachrichten gleichzeitig senden möchten, können Sie ein Nachrichtenarray für die Ausgabebindung `myQueueItem` definieren. Der folgende JavaScript-Code sendet zwei Warteschlangennachrichten mit hartcodierten Werten für jede empfangene HTTP-Anforderung.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Ausgabe: Attribute
 
In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Das Attribut gilt für einen Parameter vom Typ `out` oder für den Rückgabewert der Funktion. Der Konstruktor des Attributs akzeptiert den Namen der Warteschlange, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Durch Festlegen der Eigenschaft `Connection` können Sie das zu verwendende Speicherkonto angeben, wie im folgenden Beispiel zu sehen:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

Mit dem Attribut `StorageAccount` können Sie das Speicherkonto auf Klassen-, Methoden- oder Parameterebene angeben. Weitere Informationen finden Sie unter [Trigger: Attribute](#trigger---attribute).

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Queue` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `queue` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf `out` festgelegt sein. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlange im Funktionscode darstellt. Legen Sie diesen Wert auf `$return` fest, um auf den Rückgabewert der Funktion zu verweisen.| 
|**queueName** |**QueueName** | Der Name der Warteschlange. | 
|**Verbindung** | **Connection** |Der Name einer App-Einstellung, die die Storage-Verbindungszeichenfolge für diese Bindung enthält. Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie hier nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyStorage“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyStorage“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Storage-Verbindungszeichenfolge aus der App-Einstellung `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Ausgabe: Verwendung
 
Verwenden Sie in C# und C#-Skripts einen Methodenparameter wie `out T paramName`, um eine einzelne Warteschlangennachricht zu schreiben. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Anstelle eines Parameters vom Typ `out` können Sie den Rückgabetyp der Methode verwenden, und `T` kann einer der folgenden Typen sein:

* Ein Objekt, das als JSON serialisierbar ist
* `string`
* `byte[]`
* [CloudQueueMessage] 

Verwenden Sie einen der folgenden Typen, um in C# und C#-Skripts mehrere Warteschlangennachrichten zu schreiben: 

* `ICollector<T>` oder `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Verwenden Sie in JavaScript-Funktionen `context.bindings.<name>`, um auf die Ausgabewarteschlangennachricht zuzugreifen. Für die Nutzlast des Warteschlangenelements kann eine Zeichenfolge oder ein JSON-serialisierbares Objekt verwendet werden.


## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung |  Verweis |
|---|---|
| Warteschlange | [Warteschlangen-Fehlercodes](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabelle, Warteschlange | [Speicherfehlercodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabelle, Warteschlange |  [Problembehandlung](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Funktion, die durch Azure Queue Storage ausgelöst wird](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Meldungen in die Warteschlange von Azure Storage mithilfe von Functions](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
