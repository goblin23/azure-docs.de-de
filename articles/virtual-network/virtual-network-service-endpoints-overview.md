---
title: Dienstendpunkte von virtuellen Azure-Netzwerken | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie den direkten Zugriff auf Azure-Ressourcen aus einem virtuellen Netzwerk über Dienstendpunkte ermöglichen.
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: anithaa
ms.custom: ''
ms.openlocfilehash: 001aadc3dee03a9868a2a78e8dfc280d504633e1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2018
---
# <a name="virtual-network-service-endpoints"></a>Dienstendpunkte im virtuellen Netzwerk

Mit Dienstendpunkten von virtuellen Netzwerken (VNETs) werden der Bereich privater Adressen Ihres virtuellen Netzwerks und die Identität Ihres VNET über eine direkte Verbindung auf die Azure-Dienste erweitert. Endpunkte ermöglichen es Ihnen, Ihre kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Der Datenverkehr aus Ihrem VNET an den Azure-Dienst verbleibt immer im Backbone-Netzwerk von Microsoft Azure.

Dieses Feature ist für die folgenden Azure-Dienste und -Regionen verfügbar:

- **Azure Storage**: Allgemein verfügbar in allen Azure-Regionen.
- **Azure SQL-Datenbank**: Allgemein verfügbar in allen Azure-Regionen.
- **Azure Cosmos DB**: Allgemein verfügbar in allen Azure-Regionen mit öffentlichen Clouds. 
- **Azure SQL Data Warehouse**: Vorschau in allen Azure-Regionen mit öffentlichen Clouds.

Aktuelle Benachrichtigungen finden Sie auf der Seite [Azure Virtual Network-Updates](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Hauptvorteile

Dienstendpunkte bieten folgende Vorteile:

- **Verbesserte Sicherheit für Ihre Azure-Dienstressourcen**: Mit Dienstendpunkten können Ressourcen von Azure-Diensten auf Ihr virtuelles Netzwerk beschränkt und so geschützt werden. Das Sichern von Dienstressourcen in einem virtuellen Netzwerk erhöht die Sicherheit, da der Zugriff über das öffentliche Internet auf Ressourcen vollständig verhindert und nur Datenverkehr aus Ihrem virtuellen Netzwerk zugelassen wird.
- **Optimale Weiterleitung des Datenverkehrs für Azure-Dienste aus Ihrem virtuellen Netzwerk**: Heutzutage wird für alle Routen in Ihrem virtuellen Netzwerk, die für Internet-Datenverkehr den Weg auf Ihre lokalen und/oder virtuellen Geräte erzwingen (als Tunnelerzwingung bezeichnet), auch für den Datenverkehr von Azure-Diensten die gleiche Route wie für den Internet-Datenverkehr erzwungen. Dienstendpunkte ermöglichen eine optimale Weiterleitung für Azure-Datenverkehr. 

  Endpunkte leiten den Datenverkehr der Dienste direkt aus Ihrem virtuellen Netzwerk an den Dienst im Microsoft Azure-Backbone-Netzwerk. Die Verwaltung von Datenverkehr im Azure-Backbonenetzwerk ermöglicht Ihnen weiterhin die Überwachung und Überprüfung von ausgehendem Internet-Datenverkehr aus Ihren virtuellen Netzwerken durch die Tunnelerzwingung, ohne dass sich dies auf den Datenverkehr der Dienste auswirkt. Informieren Sie sich über [benutzerdefinierte Routen und die Tunnelerzwingung](virtual-networks-udr-overview.md).
- **Einfache Einrichtung mit weniger Verwaltungsaufwand**: Sie benötigen in Ihren virtuellen Netzwerken keine reservierten öffentlichen IP-Adressen mehr, um Azure-Ressourcen über die IP-Firewall zu schützen. Es sind keine NAT- oder Gatewaygeräte erforderlich, um die Dienstendpunkte einzurichten. Dienstendpunkte werden einfach per Klick in einem Subnetz konfiguriert. Es entsteht kein zusätzlicher Aufwand für die Verwaltung der Endpunkte.

## <a name="limitations"></a>Einschränkungen

- Das Feature ist nur für virtuelle Netzwerke verfügbar, für die das Azure Resource Manager-Bereitstellungsmodell verwendet wird.
- Endpunkte sind für Subnetze aktiviert, die in virtuellen Azure-Netzwerken konfiguriert sind. Endpunkte können nicht für Datenverkehr verwendet werden, der aus Ihrer lokalen Umgebung an Azure-Dienste fließt. Weitere Informationen finden Sie unter [Schützen des Zugriffs auf Azure-Dienste aus der lokalen Umgebung](#securing-azure-services-to-virtual-networks).
- Bei Azure SQL gilt ein Dienstendpunkt nur für Datenverkehr von Azure-Diensten in der Region eines virtuellen Netzwerks. Damit Datenverkehr vom Typ RA-GRS und GRS für Azure Storage unterstützt werden kann, gelten Endpunkte zusätzlich auch für Regionspaare, in denen das virtuelle Netzwerk bereitgestellt wird. Informieren Sie sich über [Azure-Regionspaare](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).

## <a name="securing-azure-services-to-virtual-networks"></a>Schützen von Azure-Diensten in virtuellen Netzwerken

- Der Dienstendpunkt eines virtuellen Netzwerk stellt die Identität Ihres virtuellen Netzwerks für den Azure-Dienst bereit. Nachdem Dienstendpunkte in Ihrem virtuellen Netzwerk aktiviert wurden, können Sie Ressourcen von Azure-Diensten auf Ihr virtuelles Netzwerk beschränken und auf diese Weise schützen, indem Sie den Ressourcen eine Regel für das virtuelle Netzwerk hinzufügen.
- Heutzutage werden für Datenverkehr von Azure-Diensten aus einem virtuellen Netzwerk öffentliche IP-Adressen als Quell-IP-Adressen verwendet. Bei Verwendung von Dienstendpunkten wird für den Dienstdatenverkehr zu privaten virtuellen Netzwerk-Adressen als Quell-IP-Adressen gewechselt, wenn aus einem virtuellen Netzwerk auf den Azure-Dienst zugegriffen wird. Dieser Wechsel ermöglicht Ihnen den Zugriff auf die Dienste, ohne dass reservierte öffentliche IP-Adressen in IP-Firewalls verwendet werden müssen.
- __Schützen des Zugriffs auf Azure-Dienste aus der lokalen Umgebung__:

  Standardmäßig sind Azure-Dienstressourcen, die auf virtuelle Netzwerke beschränkt und so geschützt sind, über lokale Netzwerke nicht erreichbar. Wenn Sie Datenverkehr aus der lokalen Umgebung zulassen möchten, müssen Sie auch öffentliche IP-Adressen (meist NAT) aus der lokalen Umgebung bzw. per ExpressRoute zulassen. Diese IP-Adressen können über die Konfiguration der IP-Firewall für Azure-Dienstressourcen hinzugefügt werden.

  ExpressRoute: Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokal, für öffentliches Peering oder Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Schützen von Azure-Diensten in virtuellen Netzwerken](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Dienstendpunkte werden in einem Subnetz eines virtuellen Netzwerks konfiguriert. Für Endpunkte können alle Arten von Computeinstanzen verwendet werden, die in diesem Subnetz ausgeführt werden.
- Sie können mehrere Dienstendpunkte für alle unterstützten Azure-Dienste (z.B. Azure Storage oder Azure SQL-Datenbank) eines Subnetzes konfigurieren.
- Bei Azure SQL müssen sich virtuelle Netzwerke in derselben Region wie die Ressource des Azure-Diensts befinden. Für Azure Storage muss sich das primäre Konto bei Verwendung von GRS- und RA-GRS-Konten in derselben Region wie das virtuelle Netzwerk befinden. Für alle anderen Dienste können die Ressourcen des Azure-Diensts in virtuellen Netzwerken in jeder Region gesichert werden. 
- Das virtuelle Netzwerk, in dem der Endpunkt konfiguriert ist, kann sich unter demselben oder einem anderen Abonnement wie die Ressource des Azure-Diensts befinden. Weitere Informationen zu den erforderlichen Berechtigungen zum Einrichten von Endpunkten und Schützen von Azure-Diensten finden Sie unter [Bereitstellung](#Provisioning).
- Für unterstützte Dienste können Sie neue oder vorhandene Ressourcen in virtuellen Netzwerken schützen, indem Sie Dienstendpunkte verwenden.

### <a name="considerations"></a>Überlegungen

- Nach der Aktivierung eines Dienstendpunkts wird für die IP-Quelladressen von virtuellen Computern im Subnetz von der Verwendung öffentlicher IPv4-Adressen jeweils zur Verwendung der entsprechenden privaten IPv4-Adresse gewechselt, wenn aus diesem Subnetz mit dem Dienst kommuniziert wird. Alle vorhandenen geöffneten TCP-Verbindungen mit dem Dienst werden während dieses Wechselvorgangs geschlossen. Achten Sie darauf, dass keine kritischen Aufgaben ausgeführt werden, wenn Sie einen Dienstendpunkt eines Diensts für ein Subnetz aktivieren oder deaktivieren. Stellen Sie außerdem sicher, dass Ihre Anwendungen nach der Umstellung dieser IP-Adresse automatisch eine Verbindung mit Azure-Diensten herstellen können.

  Die Umstellung der IP-Adresse wirkt sich nur auf Dienstdatenverkehr aus Ihrem virtuellen Netzwerk aus. Es ergeben sich keine Auswirkungen auf anderen Datenverkehr an bzw. von den öffentlichen IPv4-Adressen, die Ihren virtuellen Computern zugewiesen sind. Wenn Sie über vorhandene Firewallregeln mit öffentlichen Azure-IP-Adressen verfügen, funktionieren diese Regeln für Azure-Dienste nicht mehr, nachdem die Umstellung auf private virtuelle Netzwerk-Adressen durchgeführt wurde.
- Bei Verwendung von Dienstendpunkten bleiben DNS-Einträge für Azure-Dienste unverändert und werden weiterhin in öffentliche IP-Adressen aufgelöst, die dem Azure-Dienst zugewiesen werden.
- Netzwerksicherheitsgruppen (NSGs) mit Dienstendpunkten:
  - Da für NSGs ausgehender Internet-Datenverkehr standardmäßig zugelassen wird, ist auch der Datenverkehr aus Ihrem VNET zu Azure-Diensten zulässig. In Verbindung mit Dienstendpunkten funktioniert dies weiter wie bisher. 
  - Falls Sie den gesamten ausgehenden Internet-Datenverkehr verweigern und nur Datenverkehr für bestimmte Azure-Dienste zulassen möchten, können Sie hierfür __Azure-Diensttags__ in Ihren NSGs verwenden. Sie können unterstützte Azure-Dienste als Ziel in Ihren NSG-Regeln angeben, und die Wartung der zugrunde liegenden IP-Adressen der einzelnen Tags wird von Azure bereitgestellt. Weitere Informationen finden Sie unter [Azure Service tags for NSGs](https://aka.ms/servicetags) (Azure-Diensttags für NSGs). 

### <a name="scenarios"></a>Szenarien

- **Mittels Peering verknüpfte, verbundene oder mehrere virtuelle Netzwerke**: Zum Schützen von Azure-Diensten in mehreren Subnetzen innerhalb eines virtuellen Netzwerks oder mehrerer virtueller Netzwerke können Sie Dienstendpunkte unabhängig voneinander in den einzelnen Subnetzen aktivieren und Ressourcen von Azure-Diensten in allen diesen Subnetzen schützen.
- **Filtern von ausgehendem Datenverkehr, der aus dem virtuellen Netzwerk an Azure-Dienste fließt**: Wenn Sie den Datenverkehr, der aus einem virtuellen Netzwerk an einen Azure-Dienst fließen soll, untersuchen oder filtern möchten, können Sie in diesem virtuellen Netzwerk ein virtuelles Netzwerkgerät bereitstellen. Anschließend können Sie Dienstendpunkte auf das Subnetz anwenden, in dem das virtuelle Netzwerkgerät bereitgestellt wurde, und Ressourcen des Azure-Diensts auf dieses Subnetz beschränken und so schützen. Dieses Szenario kann hilfreich sein, wenn Sie den Zugriff auf den Azure-Dienst aus Ihrem virtuellen Netzwerk per Filterung durch ein virtuelles Netzwerkgerät nur auf bestimmte Azure-Ressourcen beschränken möchten. Weitere Informationen finden Sie unter [egress with network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Ausgehender Datenverkehr mit virtuellen Netzwerkgeräten).
- **Schützen von Azure-Ressourcen für Dienste, die direkt in virtuellen Netzwerken bereitgestellt werden**: Verschiedene Azure-Dienste können in einem virtuellen Netzwerk direkt in bestimmten Subnetzen bereitgestellt werden. Sie können Ressourcen von Azure-Diensten für Subnetze mit [verwalteten Diensten](virtual-network-for-azure-services.md) schützen, indem Sie im Subnetz des verwalteten Diensts einen Dienstendpunkt einrichten.
- **Datenträger-Datenverkehr von einem virtuellen Azure-Computer:** Datenträger-Datenverkehr eines virtuellen Computers (einschließlich Einbindung und Aufhebung der Einbindung sowie Datenträger-E/A) für verwaltete/nicht verwaltete Datenträger wird durch Dienstendpunkte, die Änderungen für Azure Storage weiterleiten, nicht beeinflusst. Der REST-Zugriff auf Seitenblobs kann über Dienstendpunkte und [Azure Storage-Netzwerkregeln](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auf bestimmte Netzwerke beschränkt werden. 

### <a name="logging-and-troubleshooting"></a>Protokollierung und Problembehandlung

Nachdem Dienstendpunkte für einen bestimmten Dienst konfiguriert wurden, können Sie wie folgt überprüfen, ob die Dienstendpunktroute eingerichtet wurde: 
 
- Überprüfen der Quell-IP-Adresse einer Dienstanforderung in der Dienstdiagnose. Alle neuen Anforderungen mit Dienstendpunkten zeigen die Quell-IP-Adresse für die Anforderung als private IP-Adresse eines virtuellen Netzwerks an, die dem Client zugewiesen ist, über den die Anforderung aus Ihrem virtuellen Netzwerk gesendet wird. Ohne den Endpunkt handelt es sich bei der Adresse um eine öffentliche Azure-IP-Adresse.
- Anzeigen der effektiven Routen auf einer Netzwerkschnittstelle in einem Subnetz. Die Route zum Dienst:
  - Zeigt eine spezifischere Standardroute zu den Adresspräfixbereichen der einzelnen Dienste an
  - Weist *VirtualNetworkServiceEndpoint* als „nextHopType“ auf
  - Gibt an, dass – gegenüber Routen mit Tunnelerzwingung – eine direktere Verbindung mit dem Dienst vorhanden ist.

>[!NOTE]
> Die Dienstendpunktroute setzt alle BGP- oder UDR-Routen für die Übereinstimmung mit dem Adresspräfix eines Azure-Diensts außer Kraft. Informieren Sie sich über das [Verwenden von effektiven Routen zur Problembehandlung des Datenverkehrsflusses auf virtuellen Computern](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow).

## <a name="provisioning"></a>Bereitstellung

Dienstendpunkte können in virtuellen Netzwerken einzeln von einem Benutzer konfiguriert werden, der über Schreibzugriff auf ein virtuelles Netzwerk verfügt. Zum Schützen der Ressourcen von Azure-Diensten in einem VNET muss der Benutzer die Berechtigung *Microsoft.Network/JoinServicetoaSubnet* für die hinzuzufügenden Subnetze haben. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.

Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuelle Netzwerke und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das virtuelle Netzwerk und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, müssen sich die Ressourcen unter demselben Active Directory-Mandanten befinden. 

## <a name="pricing-and-limits"></a>Preise und Einschränkungen

Für die Nutzung von Dienstendpunkten fallen keine zusätzlichen Gebühren an. Das aktuelle Preismodell für Azure-Dienste (Azure Storage, Azure SQL-Datenbank usw.) gilt unverändert.

Für die Gesamtzahl von Dienstendpunkten in einem virtuellen Netzwerk gilt keine Beschränkung.

Für die Ressource eines Azure-Diensts (z.B. ein Azure Storage-Konto) können Dienste Beschränkungen in Bezug auf die Anzahl von Subnetzen erzwingen, die zum Schützen der Ressource verwendet werden. Ausführliche Informationen finden Sie in der Dokumentation zu den verschiedenen Diensten unter [Nächste Schritte](#next-steps).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren der Dienstendpunkte von virtuellen Netzwerken](tutorial-restrict-network-access-to-resources.md).
- Informieren Sie sich über das [Sichern eines Azure Storage-Konto in einem virtuellen Netzwerk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Informieren Sie sich über das [Sichern einer Azure SQL-Datenbank in einem virtuellen Netzwerk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Informieren Sie sich über die [Azure-Dienstintegration in virtuelle Netzwerke](virtual-network-for-azure-services.md).
-  Schnellstart: [Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) zum Einrichten eines Dienstendpunkts für das Subnetz eines VNET und zum Sichern eines Azure Storage-Kontos in diesem Subnetz

