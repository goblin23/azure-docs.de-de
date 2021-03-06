---
title: 'Schnellstart: Erstellen eines Azure Media Services-Kontos mithilfe der CLI 2.0 | Microsoft-Dokumentation'
description: Führen Sie die Schritte dieser Schnellstartanleitung aus, um ein Azure Media Services-Konto zu erstellen.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Schnellstart: Erstellen eines Azure Media Services-Kontos

> [!NOTE]
> Die neueste Version von Azure Media Services (2018-03-30) befindet sich in der Vorschau. Diese Version wird auch als „v3“ bezeichnet. 

Wenn Sie Entwickler sind oder Medieninhalte erstellen, müssen Sie ein Media Services-Konto erstellen, um Medieninhalte in Azure zu speichern, zu verschlüsseln, zu codieren, zu verwalten und zu streamen. Wenn Sie ein Media Services-Konto erstellen, müssen Sie die ID einer Azure Storage-Kontoressource angeben. Das angegebene Storage-Konto wird an Ihr Media Services-Konto angefügt. Diese Storage-Kontoressource muss sich in der gleichen geografischen Region befinden wie das Media Services-Konto.  

Diese Schnellstartanleitung beschreibt die Schritte zum Erstellen eines neuen Azure Media Services-Kontos mithilfe der CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an, und starten Sie **CloudShell**, um CLI-Befehle auszuführen, wie in den nächsten Schritten gezeigt.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, erfordert dieses Thema die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um herauszufinden, welche Version Sie haben. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

## <a name="set-the-azure-subscription"></a>Einrichten des Azure-Abonnements

Geben Sie im folgenden Befehle die ID des Azure-Abonnements an, das Sie für das Media Services-Konto verwenden möchten. Sie können eine Liste der Abonnements anzeigen, auf die Sie Zugriff haben, indem Sie zu [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) navigieren.

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

Der folgende Befehl erstellt eine Ressourcengruppe, die das Storage- und das Media Services-Konto enthalten soll. Ersetzen Sie den Platzhalter *myresourcegroup* durch den Namen, den Sie für Ihre Ressourcengruppe verwenden möchten.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Wenn Sie ein Media Services-Konto erstellen, müssen Sie die ID einer Azure Storage-Kontoressource angeben. Das angegebene Storage-Konto wird an Ihr Media Services-Konto angefügt. 

Sie müssen über ein **primäres** Storage-Konto verfügen. Darüber hinaus können Sie beliebig viele **sekundäre** Storage-Konten an Ihr Media Services-Konto anfügen. Media Services unterstützt Konten vom Typ **Allgemein v2** (GPv2) oder **Allgemein v1** (GPv1). Reine Blobkonten sind als **primäre** Konten nicht zulässig. Weitere Informationen zu Storage-Konten finden Sie unter [Optionen für Azure Storage-Konten](../../storage/common/storage-account-options.md). 

Der folgende Befehl erstellt das Storage-Konto, das dem (primären) Media Services-Konto zugeordnet werden soll. Ersetzen Sie im unten stehenden Skript den Platzhalter *storageaccountforams*. Die Zeichenfolge für „account_name“ muss weniger als 24 Zeichen lang sein.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Erstellen eines Azure Media Services-Kontos

Unten finden Sie die Azure CLI-Befehle, die ein neues Media Services-Konto erstellen. Sie müssen nur die folgenden hervorgehobenen Werte ersetzen:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen in der Ressourcengruppe, einschließlich des in dieser Schnellstartanleitung erstellten Media Services-Kontos, nicht mehr benötigen, löschen Sie die Ressourcengruppe.

Führen Sie in **CloudShell** den folgenden Befehl aus:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Streamen einer Datei](stream-files-dotnet-quickstart.md)
