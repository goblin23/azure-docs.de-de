---
title: Verwalten von VM-Datenträgern in Azure Stack | Microsoft-Dokumentation
description: Stellen Sie Datenträger für virtuelle Computer in Azure Stack bereit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 8e91b4d83aa90a7e744fb8e73cda788dbf8c58ec
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2018
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Bereitstellen von VM-Datenträgerspeicher in Azure Stack

*Gilt für: integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel wird beschrieben, wie Sie VM-Datenträgerspeicher mit dem Azure Stack-Portal oder mit PowerShell bereitstellen.

## <a name="overview"></a>Übersicht

Mit Azure Stack können [nicht verwaltete Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) auf virtuellen Computern sowohl als Betriebssystemdatenträger als auch als Datenträger für Daten verwendet werden.

Zum Verwenden von nicht verwalteten Datenträgern erstellen Sie ein [Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), unter dem die Datenträger gespeichert werden können. Die von Ihnen erstellten Datenträger werden als VM-Datenträger bezeichnet und in Containern im Speicherkonto gespeichert.

### <a name="best-practice-guidelines"></a>Richtlinien zu bewährten Methoden

Zur Verbesserung der Leistung und Reduzierung der Gesamtkosten empfehlen wir Ihnen, jeden VM-Datenträger in einem separaten Container anzuordnen. Ein Container sollte entweder einen Betriebssystem-Datenträger oder einen Datenträger für Daten enthalten, aber nicht beides gleichzeitig. (Es hindert Sie aber nichts daran, beide Arten von Datenträgern in demselben Container anzuordnen.)

Wenn Sie einem virtuellen Computer Datenträger für Daten hinzufügen, sollten Sie als Speicherort für diese Datenträger zusätzliche Container verwenden. Der Betriebssystemdatenträger für zusätzliche virtuelle Computer sollte sich ebenfalls in einem eigenen Container befinden.

Wenn Sie mehrere virtuelle Computer erstellen, können Sie für jeden neuen virtuellen Computer das gleiche Speicherkonto wiederverwenden. Lediglich die erstellten Container sollten getrennt sein.

### <a name="adding-new-disks"></a>Hinzufügen von neuen Datenträgern

In der folgenden Tabelle ist zusammengefasst, wie Sie Datenträger über das Portal und mit PowerShell verwenden.

| Methode | Optionen
|-|-|
|[Benutzerportal](#use-the-portal-to-add-additional-disks-to-a-vm)|- Fügen Sie einer vorhandenen VM neue Datenträger für Daten hinzu. Von Azure Stack werden neue Datenträger erstellt. </br> </br>- Fügen Sie eine vorhandene Datenträgerdatei (.vhd) einer vorab bereitgestellten VM hinzu. Zu diesem Zweck müssen Sie die VHD vorbereiten und die Datei dann in Azure Stack hochladen. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - Erstellen Sie einen neuen virtuellen Computer mit einem Betriebssystem-Datenträger, und fügen Sie dem virtuellen Computer gleichzeitig einen oder mehrere Datenträger für Daten hinzu. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Verwenden des Portals zum Hinzufügen von Datenträgern zu einer VM

Wenn Sie über das Portal einen virtuellen Computer erstellen, wird bei den meisten Marketplace-Elementen standardmäßig nur der Betriebssystemdatenträger erstellt.

Nach der Erstellung einer VM können Sie das Portal für folgende Zwecke verwenden:
* Erstellen Sie einen neuen Datenträger für Daten, und fügen Sie ihn an den virtuellen Computer an.
* Laden Sie einen vorhandenen Datenträger für Daten hoch, und fügen Sie ihn an die VM an.

Jeder nicht verwaltete Datenträger, den Sie hinzufügen, sollte in einem separaten Container angeordnet werden.

>[!NOTE]
>Von Azure erstellte und verwaltete Datenträger werden als [verwaltete Datenträger](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview) bezeichnet.

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Verwenden des Portals zum Erstellen und Anfügen eines neuen Datenträgers für Daten

1.  Wählen Sie im Portal die Option **Virtuelle Computer**.    
    ![Beispiel: VM-Dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Wählen Sie einen virtuellen Computer aus, der zuvor bereitgestellt wurde.   
    ![Beispiel: Auswählen eines virtuellen Computers im Dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Wählen Sie für den virtuellen Computer die Option **Datenträger** > **Neuen anfügen**.       
    ![Beispiel: Anfügen eines neuen Datenträgers an den virtuellen Computer](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Wählen Sie im Bereich **Neuen Datenträger anfügen** die Option **Speicherort**. Der Speicherort ist standardmäßig auf den Container festgelegt, der auch den Betriebssystem-Datenträger enthält.      
    ![Beispiel: Festlegen des Datenträgerspeicherorts](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Wählen Sie das zu verwendende **Speicherkonto** aus. Wählen Sie als Nächstes den **Container** aus, in dem der Datenträger für Daten platziert werden soll. Auf der Seite **Container** können Sie bei Bedarf auch einen neuen Container erstellen. Anschließend können Sie den Speicherort für den neuen Datenträger auf den eigenen Container festlegen. Wenn Sie für jeden Datenträger einen separaten Container verwenden, verteilen Sie die Platzierung des Datenträgers für Daten, was zur Verbesserung der Leistung beitragen kann. Wählen Sie zum Speichern der Auswahl die Option **Auswählen**.     
    ![Beispiel: Auswählen eines Containers](media/azure-stack-manage-vm-disks/select-container.png)

6.  Aktualisieren Sie auf der Seite **Neuen Datenträger anfügen** die Einstellungen für **Name**, **Typ**, **Größe** und **Hostzwischenspeicherung** des Datenträgers. Wählen Sie anschließend **OK**, um die neue Datenträgerkonfiguration für den virtuellen Computer zu speichern.  
    ![Beispiel: Abschließen der Datenträgeranfügung](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Nachdem der Datenträger von Azure Stack erstellt und an den virtuellen Computer angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen des virtuellen Computers unter **DATENTRÄGER** aufgeführt.   
    ![Beispiel: Anzeigen des Datenträgers](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Hinzufügen eines vorhandenen Datenträgers zu einem virtuellen Computer

1.  [Bereiten Sie eine VHD-Datei vor](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd), um sie als Datenträger für einen virtuellen Computer zu verwenden. Laden Sie die VHD-Datei in ein Speicherkonto hoch, das Sie mit dem virtuellen Computer verwenden, an den Sie die VHD-Datei anfügen möchten.

  Verwenden Sie für die VHD-Datei nicht den Container, der den Betriebssystem-Datenträger enthält.   
  ![Beispiel: Hochladen einer VHD-Datei](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Nach dem Hochladen der VHD-Datei können Sie die virtuelle Festplatte an einen virtuellen Computer anfügen. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer**.  
 ![Beispiel: Auswählen eines virtuellen Computers im Dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Wählen Sie den gewünschten virtuellen Computer in der Liste aus.    
  ![Beispiel: Auswählen eines virtuellen Computers im Dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Wählen Sie auf der Seite für den virtuellen Computer die Option **Datenträger** > **Vorhandenen anfügen**.   
  ![Beispiel: Anfügen eines vorhandenen Datenträgers](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Wählen Sie auf der Seite **Vorhandenen Datenträger anfügen** die Option **VHD-Datei**. Die Seite **Speicherkonten** wird geöffnet.    
  ![Beispiel: Auswählen einer VHD-Datei](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Wählen Sie unter **Speicherkonten** das zu verwendende Konto und anschließend einen Container aus, der die zuvor hochgeladene VHD-Datei enthält. Wählen Sie die VHD-Datei aus, und klicken Sie auf **Auswählen**, um die Auswahl zu speichern.    
  ![Beispiel: Auswählen eines Containers](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Die ausgewählte Datei wird unter **Vorhandenen Datenträger anfügen** unter **VHD-Datei** angezeigt. Aktualisieren Sie die Einstellung **Hostzwischenspeicherung** des Datenträgers, und wählen Sie dann **OK**, um die neue Datenträgerkonfiguration für den virtuellen Computer zu speichern.    
  ![Beispiel: Anfügen der VHD-Datei](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Nachdem der Datenträger von Azure Stack erstellt und an den virtuellen Computer angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger** aufgeführt.   
  ![Beispiel: Abschließen der Datenträgeranfügung](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Hinzufügen mehrerer nicht verwalteter Datenträger zu einem virtuellen Computer mithilfe von PowerShell
Mit PowerShell können Sie einen virtuellen Computer bereitstellen und einen neuen Datenträger hinzufügen oder eine bereits vorhandene **VHD-Datei** als Datenträger anfügen.

Das Cmdlet **Add-AzureRmVMDataDisk** fügt einem virtuellen Computer einen Datenträger hinzu. Ein Datenträger kann beim Erstellen eines virtuellen Computers oder einem bereits vorhandenen virtuellen Computer hinzugefügt werden. Geben Sie den Parameter **VhdUri** an, um die Datenträger auf unterschiedliche Container zu verteilen.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Hinzufügen von Datenträgern zu einem neuen virtuellen Computer
In den folgenden Beispielen wird mithilfe von PowerShell-Befehlen ein virtueller Computer mit drei Datenträgern erstellt, die jeweils in einem anderen Container platziert werden.

Der erste Befehl erstellt ein VM-Objekt und speichert es in der Variablen *$VirtualMachine*. Der Befehl weist dem virtuellen Computer einen Namen und eine Größe zu.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Die nächsten drei Befehle weisen den Variablen *$DataDiskVhdUri01*, *$DataDiskVhdUri02* und *$DataDiskVhdUri03* Pfade von drei Datenträgern zu. Definieren Sie in der URL einen anderen Pfadnamen, um die Datenträger auf unterschiedliche Container zu verteilen.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Die letzten drei Befehle fügen dem in *$VirtualMachine* gespeicherten virtuellen Computer Datenträger hinzu. Die einzelnen Befehle geben jeweils den Namen, den Speicherort und zusätzliche Eigenschaften des Datenträgers an. Der URI des jeweiligen Datenträgers wird in *$DataDiskVhdUri01*, *$DataDiskVhdUri02* bzw. *$DataDiskVhdUri03* gespeichert.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Verwenden Sie die folgenden PowerShell-Befehle, um dem virtuellen Computer den Betriebssystem-Datenträger und die Netzwerkkonfiguration hinzuzufügen, und starten Sie dann den neuen virtuellen Computer.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Hinzufügen von Datenträgern zu einem vorhandenen virtuellen Computer
In den folgenden Beispielen werden einem vorhandenen virtuellen Computer mithilfe von PowerShell-Befehlen drei Datenträger hinzugefügt.
Der erste Befehl ruft mithilfe des Cmdlets **Get-AzureRmVM** den virtuellen Computer „VirtualMachine“ ab. Der Befehl speichert den virtuellen Computer in der Variablen *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Die nächsten drei Befehle weisen den Variablen „$DataDiskVhdUri01“, „$DataDiskVhdUri02“ und „$DataDiskVhdUri03“ Pfade von drei Datenträgern zu.  Die verschiedenen Pfadnamen im VhdUri-Parameter geben unterschiedliche Container für die Platzierung des Datenträgers an.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Die nächsten drei Befehle fügen die Datenträger dem in der Variablen *$VirtualMachine* gespeicherten virtuellen Computer hinzu. Die einzelnen Befehle geben jeweils den Namen, den Speicherort und zusätzliche Eigenschaften des Datenträgers an. Der URI des jeweiligen Datenträgers wird in *$DataDiskVhdUri01*, *$DataDiskVhdUri02* bzw. *$DataDiskVhdUri03* gespeichert.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  Der letzte Befehl aktualisiert den Zustand des in *$VirtualMachine* gespeicherten virtuellen Computers in *-ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu virtuellen Azure Stack-Computern finden Sie unter [Aspekte von virtuellen Computern in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
