<properties
    pageTitle="Azure Active Directory identitetsbeskyttelse beskeder | Microsoft Azure"
    description="Få mere at vide, hvordan meddelelser understøtter dine undersøgelsesaktiviteter."
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, skyen app registrering, administration af programmer, sikkerhed, risikoen, risikoen niveau, sikkerhedsrisiko, sikkerhedspolitik"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory identitetsbeskyttelse beskeder 


Azure AD identitet beskyttelse sender to typer automatiseret besked om mails kan hjælpe dig med at administrere bruger risiko og risikohændelser:

- Brugeren er blevet kompromitteret beskeder om mail

- Ugentlig samlet mail

## <a name="user-compromised-alert-email"></a>Brugeren er blevet kompromitteret beskeder om mail

Der oprettes en bruger kompromitteret e-mail-påmindelse, når Azure AD identitet beskyttelse identificerer en konto, som er blevet kompromitteret. E-mailen indeholder et link til de brugere, der er markeret med flag til risikoen rapport i dashboardet for beskyttelse af din identitet. Vi anbefaler, at du straks Undersøg meddelelser om er blevet kompromitteret.


## <a name="weekly-digest-email"></a>Ugentlig samlet mail

Ugentlig samlet e-mailen indeholder en oversigt over nye risikoen begivenheder.<br>
Den indeholder:

- Brugere på risiko
- Mistænkelige aktiviteter
- Fundet svagheder
- Links til de relaterede rapporter i beskyttelse af din identitet


<br>
![Afhjælpning](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

Du kan skifte afsendelse af en ugentlig samlet mail.
<br><br>
![Bruger risici](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Åbne dialogboksen relaterede konfiguration**:

1. Klik på **Indstillinger**på bladet **Azure AD identitet beskyttelse** .
<br><br>
![Bruger risikoen politik](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. Klik på **beskeder**i sektionen **Generelt** .
<br><br>
![Bruger risikoen politik](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Se også

- [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md) 

