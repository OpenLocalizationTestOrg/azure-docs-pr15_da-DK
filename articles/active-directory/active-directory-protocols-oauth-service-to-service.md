<properties
    pageTitle="Azure AD tjenester Auth ved hjælp af OAuth2.0 | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du bruger HTTP-meddelelser til at implementere tjenester godkendelse ved hjælp af OAuth2.0 klient legitimationsoplysninger Giv strømmen."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="service-to-service-calls-using-client-credentials"></a>Tjenesten til tjenesten opkald ved hjælp af klienten legitimationsoplysninger

Den OAuth 2.0-klienten legitimationsoplysninger Giv dataflow tillader, at en webtjeneste (en *fortrolige klienten*) til at bruge sin egen legitimationsoplysninger til at godkende, når du ringer til en anden webtjeneste, i stedet for at efterligne en bruger. I dette scenarie skal er kunden typisk en midterste niveau webtjeneste, et daemon tjeneste eller et websted.

## <a name="client-credentials-grant-flow-diagram"></a>Klient legitimationsoplysninger give rutediagram

I det følgende diagram beskrives det, hvordan legitimationsoplysningerne, der klient give flow fungerer i Azure Active Directory (Azure AD).

![OAuth2.0 klient legitimationsoplysninger Giv Flow](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientprogrammet godkender til Azure AD token udstedelse slutpunkt og anmoder om et adgangstoken.
2. Azure AD token udstedelse slutpunktet udsteder adgangstoken.
3. Adgangstoken bruges til at godkende sikret ressourcen.
4. Der returneres dataene fra den beskyttede ressource til webprogrammet.

## <a name="register-the-services-in-azure-ad"></a>Registrere tjenesterne i Azure AD

Registrere både tjenesten opkald og tjenesten modtagelse i Azure Active Directory (Azure AD). Finde detaljerede oplysninger, se [tilføjelse, opdatering, og fjerne en App](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Anmode om et adgangstoken

For at anmode om et adgangstoken, skal du bruge en HTTP-POST til lejer-specifikke Azure AD slutpunkt.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Token anmodning om adgang til tjenesten-tjenesten

En access tjenester token anmodningen indeholder følgende parametre.

| Parameter | | Beskrivelse |
|-----------|------|------------|
| response_type | påkrævet | Angiver den ønskede svar. Værdien skal være **client_credentials**i et klient legitimationsoplysninger Giv flow.|
| client_id | påkrævet | Angiver Azure AD-klient-id for den opkald webtjeneste. For at finde opkald programmets klient-ID i portalen Azure Management, skal du klikke på **Active Directory**, klikke på mappen, skal du klikke på programmet og derefter klikke på **Konfigurer**.|
| client_secret | påkrævet |  Angiv en registreret for opkald webtjenesten i Azure AD-nøgle. Oprette en nøgle i portalen Azure Management, skal du klikke på **Active Directory**, skal du klikke på mappen, klik på programmet, og klik derefter på **Konfigurer**. |
| ressource | påkrævet | Angiv tjenesten modtagelse web App-ID URI. Find den App-ID URI i portalen Azure Management, klikke på **Active Directory**, klikke på mappen, klik på programmet, og klik derefter på **Konfigurer**. |

## <a name="example"></a>Eksempel

Den følgende HTTP-POST, som en adgangstoken til webtjenesten https://service.contoso.com/. Den `client_id` identificerer den webtjeneste, der anmoder om adgangstoken.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Adgang til tjenesten-tjenesten Token svar

Svar succes indeholder et JSON OAuth 2.0 svar med følgende parametre.

| Parameter   | Beskrivelse |
|-------------|-------------|
|access_token |Nødvendige adgangstoken. Kalde webtjenesten kan bruge dette token til at godkende modtagelse webtjenesten. |
|access_type  | Angiver værdien token type. Den eneste type, der understøtter Azure AD er **bæreren**. Finde flere oplysninger om bæreren tokens, den [OAuth 2.0 godkendelse Framework: bæreren Token brugen (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | Hvor længe adgangstoken er gyldig (i sekunder).|
|expires_on   |Det tidspunkt, hvor adgangstoken udløber. Datoen, der repræsenteres som antallet sekunder fra 1970-01-01T0:0:0Z UTC indtil udløbsdatoen. Denne værdi bruges til at bestemme levetiden for cachelagrede tokens. |
|ressource     | App-ID URI for modtagelse webtjeneste. |

## <a name="example"></a>Eksempel

I følgende eksempel viser et succes svar på en anmodning om et adgangstoken til en webtjeneste.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Se også

* [OAuth 2.0 i Azure AD](active-directory-protocols-oauth-code.md)
