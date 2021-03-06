---
title: Abrufen der Ergebnisse der Azure AD-Zugriffsüberprüfung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Ergebnisse von Azure Active Directory-Zugriffsüberprüfungen abrufen.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: billmath
ms.openlocfilehash: cdd07fd837863d9a5abced0db8cacaded6288a41
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192223"
---
# <a name="retrieve-access-review-results"></a>Abrufen der Ergebnisse der Zugriffsüberprüfung

Mit Azure Active Directory (Azure AD) können Administratoren [eine Zugriffsüberprüfung erstellen](active-directory-azure-ad-controls-create-access-review.md). Diese kann für Mitglieder einer Gruppe oder Benutzer, die einer Anwendung zugewiesen sind, durchgeführt werden.  Benutzern mit den Rollen **Globaler Administrator**, **Sicherheitsadministrator** oder **Benutzer mit Leseberechtigung für Sicherheitsfunktionen** können auch die Ergebnisse einer Zugriffsüberprüfung lesen.  Zum Zuweisen von Benutzern zu einer dieser Rollen kann ein Administrator für privilegierte Rollen mithilfe von Azure AD PIM Benutzern die Berechtigung zum Aktivieren der Rolle erteilen. Alternativ kann ein globaler Administrator [einem Benutzer die Rolle dauerhaft zuweisen](active-directory-users-assign-role-azure-portal.md).

## <a name="locating-an-access-review"></a>Suchen einer Zugriffsüberprüfung

Wenn Sie wissen, welches Programm die Zugriffsüberprüfung enthält, navigieren Sie zur Zugriffsüberprüfungsseite, klicken Sie auf **Programme**, und wählen Sie das Programm aus, welches das Zugriffsüberprüfungs-Steuerelement enthält.  Klicken Sie auf **Steuerelemente**, und wählen Sie das Steuerelement für die Zugriffsüberprüfung aus. Wenn das Programm viele Steuerelemente enthält, können Sie nach Steuerelementen eines bestimmten Typs filtern und diese nach ihrem Status sortieren. Darüber hinaus können Sie nach dem Namen des Zugriffsüberprüfung-Steuerelements oder dem Anzeigename des Besitzers, der das Steuerelement erstellt hat, suchen. 

Wenn Sie nicht wissen, welches Programm die Zugriffsüberprüfung enthält, navigieren Sie zur Zugriffsüberprüfungsseite, und klicken Sie auf **Steuerelemente**.  Sie können nach Steuerelementen eines bestimmten Typs filtern und dabei nach deren Status sortieren. Sie können auch nach dem Namen des Zugriffsüberprüfungs-Steuerelements oder dem Anzeigename des Besitzers suchen, der es erstellt hat. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Abrufen der Ergebnisse für eine einmalige Zugriffsüberprüfung

Ist als Wiederholungstyp der Überprüfung „Einmal“ festgelegt, können der Status einer aktiven Zugriffsüberprüfung und die Ergebnisse einer abgeschlossenen Zugriffsüberprüfung im Abschnitt **Ergebnisse** abgerufen werden.  Sie können den Anzeigenamen oder den Benutzerprinzipalname eines Benutzers eingeben, dessen Zugriff überprüft wird, um nur den Zugriff dieses Benutzers anzuzeigen.  Wenn Sie alle Ergebnisse einer abgeschlossenen Zugriffsüberprüfung abrufen möchten, klicken Sie auf die Schaltfläche **Herunterladen**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Abrufen der Ergebnisse für mehrere Instanzen einer Serienzugriffsüberprüfung

Klicken Sie zum Anzeigen des Status einer aktiven Serienzugriffsüberprüfung auf den Abschnitt **Ergebnisse**.  Sie können den Anzeigenamen oder den Benutzerprinzipalname eines Benutzers eingeben, dessen Zugriff überprüft wird.

Klicken Sie zum Anzeigen der Ergebnisse einer abgeschlossenen Instanz einer Serienzugriffsüberprüfung auf **Ausführungsverlauf**. Wählen Sie anschließend basierend auf dem Start- und Enddatum der Instanz die spezifische Instanz in der Liste der abgeschlossenen Zugriffsüberprüfungsinstanzen aus.   Die Ergebnisse dieser Instanz können im Abschnitt **Ergebnisse** abgerufen werden.  Sie können den Anzeigenamen oder den Benutzerprinzipalname eines Benutzers eingeben, dessen Zugriff überprüft wird, um nur den Zugriff dieses Benutzers anzuzeigen.  Wenn Sie alle Ergebnisse einer abgeschlossenen Instanz einer Serienzugriffsüberprüfung abrufen möchten, klicken Sie auf die Schaltfläche **Herunterladen**.


## <a name="removing-users-from-an-access-review"></a>Entfernen von Benutzern aus einer Zugriffsüberprüfung

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

Ein gelöschter Benutzer bleibt in Azure AD standardmäßig 30 Tage lang gelöscht. In diesem Zeitraum kann er von einem Administrator wiederhergestellt werden, sofern erforderlich.  Nach 30 Tagen wird dieser Benutzer endgültig gelöscht.  Darüber hinaus kann ein globaler Administrator über das Azure Active Directory-Portal explizit [einen kürzlich gelöschten Benutzer endgültig löschen](active-directory-users-restore.md), bevor dieser Zeitraum abgelaufen ist.  Wurde ein Benutzer endgültig gelöscht, werden nachfolgende Daten zu diesem Benutzer aus aktiven Zugriffsüberprüfungen entfernt.  Überwachungsinformationen zu gelöschten Benutzern verbleiben im Überwachungsprotokoll.

## <a name="next-steps"></a>Nächste Schritte

- [Manage user access with Azure AD access reviews (Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen)](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Manage guest access with Azure AD access reviews](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) (Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen)
- [Manage programs and controls for Azure AD access reviews](active-directory-azure-ad-controls-manage-programs-controls.md) (Verwalten der Programme und Kontrollen für Azure AD-Zugriffsüberprüfungen)
- [Erstellen einer Zugriffsüberprüfung für Mitglieder einer Gruppe oder den Zugriff auf eine Anwendung](active-directory-azure-ad-controls-create-access-review.md)
- [Erstellen einer Zugriffsüberprüfung von Benutzern in der Azure AD-Administratorrolle](active-directory-privileged-identity-management-how-to-start-security-review.md)


