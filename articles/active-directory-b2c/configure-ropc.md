---
title: Konfigurieren des Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern in Azure AD B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Flow für Kennwortanmeldeinformationen von Ressourcenbesitzern in Azure AD B2C konfigurieren.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158253"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Konfigurieren des Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern in Azure AD B2C

Der Flow für Kennwortanmeldeinformationen von Ressourcenbesitzern (Resource Owner Password Credentials, ROPC) ist ein OAUTH-Standardauthentifizierungsflow, in dem die Anwendung, auch als vertrauende Seite bezeichnet, gültige Anmeldeinformationen, etwa Benutzer-ID und Kennwort für ein ID-Token, Zugriffstoken und Aktualisierungstoken, austauscht. 

> [!NOTE]
> Dieses Feature befindet sich in der Vorschauphase.

In Azure AD B2C werden diese Optionen unterstützt:

- **Nativer Client**: Die Benutzerinteraktion während der Authentifizierung erfolgt über Code, der auf einem Gerät auf Benutzerseite ausgeführt wird und eine mobile Anwendung sein kann, die unter dem Betriebssystem, z. B. Android, oder im Browser, z. B. über JavaScript ausgeführt wird.
- **Flow für öffentlichen Client**: Im API-Aufruf werden nur die von einer Anwendung gesammelten Benutzeranmeldeinformationen gesendet. Die Anmeldeinformationen der Anwendung werden nicht gesendet.
- **Neue Ansprüche hinzuzufügen**: Der Inhalt des ID-Tokens kann geändert werden, um neue Ansprüche hinzuzufügen. 

Diese Flows werden nicht unterstützt:

- **Server-zu-Server**: Das Identitätsschutzsystem benötigt eine zuverlässige IP-Adresse, die vom Aufrufer (der native Client) im Rahmen der Interaktion erfasst wurde.  In einem serverseitigen API-Aufruf wird nur die IP-Adresse des Servers verwendet, und das Identitätsschutzsystem kann eine wiederholte IP-Adresse als Angreifer identifizieren, wenn ein dynamischer Schwellenwert für fehlgeschlagene Authentifizierungen überschritten wird.
- **Flow für vertraulichen Client**: Die Anwendungsclient-ID wird überprüft, aber der geheime Anwendungsschlüssel wird nicht überprüft.

##  <a name="create-a-resource-owner-policy"></a>Erstellen von Richtlinien für Ressourcenbesitzer

1. Melden Sie sich beim Azure-Portal als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Um zu Ihrem Azure AD B2C-Mandanten zu wechseln, wählen Sie das B2C-Verzeichnis in der oberen rechten Ecke des Portals.
3. Wählen Sie unter **Richtlinien** die Option **Richtlinien für Ressourcenbesitzer** aus.
4. Geben Sie einen Namen für die Richtlinie ein, z. B. *ROPC_Auth*, und klicken Sie dann auf **Anwendungsansprüche**.
5. Wählen Sie die Anwendungsansprüche aus, die Sie für Ihre Anwendung benötigen, z. B. benötigen *Anzeigename*, *E-Mail-Adresse* und *Identitätsanbieter*.
6. Klicken Sie auf **OK**, und klicken Sie dann auf **Erstellen**.

Es wird dann ein Endpunkt angezeigt (wie das folgende Beispiel):

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrieren einer Anwendung

1. Klicken Sie in den B2C-Einstellungen auf **Anwendungen** und dann auf **+ Hinzufügen**.
2. Stellen Sie einen Namen für die Anwendung bereit, z. B. *ROPC_Auth_app*.
3. Klicken Sie auf **Nein** für **Web-App/Web-API**, und klicken Sie auf **Ja** für **Nativer Client**.
4. Belassen Sie die Einstellungen aller anderen Werte, und klicken Sie auf **Erstellen**.
5. Wählen Sie die neue Anwendung aus, und notieren Sie sich die Anwendungs-ID.

## <a name="test-the-policy"></a>Testen der Richtlinie

Verwenden Sie Ihre bevorzugte API-Entwicklungsanwendung, um einen API-Aufruf zu generieren, und überprüfen Sie die Antwort, um Ihre Richtlinie zu debuggen. Erstellen Sie einen Aufruf wie den folgenden, wobei Sie die Informationen aus der folgenden Tabelle als Hauptteil der POST-Anforderung verwenden:
- Ersetzen Sie *yourtenant.onmicrosoft.com* durch den Namen Ihres B2C-Mandanten.
- Ersetzen Sie *B2C_1A_ROPC_Auth* durch den vollständigen Namen Ihrer ROPC-Richtlinie.
- Ersetzen Sie *bef2222d56-552f-4a5b-b90a-1988a7d634c3* durch die Anwendungs-ID aus der Registrierung.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Schlüssel | Wert |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | token id_token |

*Client_id* ist der Wert, den Sie zuvor als Anwendungs-ID notiert haben. *Offline_access* ist optional, wenn Sie ein Aktualisierungstoken erhalten möchten. 

Die tatsächliche POST-Anforderung sieht wie folgt aus:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Eine erfolgreiche Antwort mit Offlinezugriff ähnelt dem folgenden Beispiel:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Einlösen eines Aktualisierungstokens

Erstellen Sie einen POST-Aufruf wie den folgenden, wobei Sie die Informationen aus der folgenden Tabelle als Hauptteil der Anforderung verwenden:

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Schlüssel | Wert |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* und *resource* sind die Werte, die Sie zuvor als Anwendungs-ID notiert haben. *Refresh_token* ist das Token, das Sie in dem zuvor erwähnten Authentifizierungsaufruf empfangen haben.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementieren mit dem bevorzugten nativen SDK oder Verwenden von AppAuth


Die Azure AD B2C-Implementierung entspricht den OAuth 2.0-Standards oder ROPC für öffentliche Clients und sollte mit den meisten Client-SDKs kompatibel sein.  Wir haben diesen Flow ausgiebig in Produktionsumgebungen mit AppAuth für iOS und AppAuth für Android getestet.  Die neuesten Informationen hierzu finden Sie unter https://appauth.io/.

Sie können funktionierende Beispiele, die zur Verwendung mit Azure AD B2C konfiguriert sind, von GitHub unter https://aka.ms/aadb2cappauthropc für Android und unter https://aka.ms/aadb2ciosappauthropc herunterladen.




