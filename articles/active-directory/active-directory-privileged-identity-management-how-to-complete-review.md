<properties
   pageTitle="Hvordan du fuldfører en access-Gennemse | Microsoft Azure"
   description="Når du har startet en access-Gennemse Azure AD privilegerede identitet administration, se, hvordan du fuldfører den og få vist resultaterne"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Hvordan du fuldfører en access-Gennemse Azure AD privilegerede identitet administration


Privilegerede rolle administratorer kan gennemse privilegerede access én gang en [sikkerhed Gennemse er blevet startet](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD rettigheder identitet Management (PIM) sender automatisk en mail, at bede brugere om at gennemgå deres adgang. Hvis en bruger ikke får vist en mail, kan du sende dem en vejledningen i, [hvordan du udfører en sikkerhed Gennemse](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Når sikkerhed Gennemse periode er eller alle brugere er færdig med deres egen Gennemse, skal du følge trinnene i denne artikel for at administrere siden Gennemse, og se resultaterne.

## <a name="manage-security-reviews"></a>Administrere sikkerhed gennemsyn

1. Gå til [Azure-portalen](https://portal.azure.com/) , og vælg **Azure AD privilegerede identitet** administrationsprogram på dit dashboard.
2. Vælg **Access gennemser** sektionen af dashboardet.
3. Vælg den access-revision, som du vil administrere.

På siden access Gennemse detaljeret blade findes der en indstillinger for tal for administration af denne gennemgang.

![PIM knapper til Gennemse access - skærmbillede][1]

### <a name="remind"></a>Minde

Hvis en access-Gennemse er konfigureret, så brugerne gennemse selv, sender knappen **Send påmindelse** en meddelelse. 

### <a name="stop"></a>Stop

Alle access anmeldelser har en slutdato, men du kan bruge knappen **Stop** for at afslutte den tidligt. Hvis du ikke har gennemset alle brugere af denne tid, kan de ikke kunne når du holder op med korrekturen. Du kan ikke genstarte en gennemgang, når den er stoppet.

### <a name="apply"></a>Anvende

Når en access-Gennemse er fuldført, implementerer enten fordi du har nået slutdatoen eller ikke længere manuelt, knappen **Anvend** med resultatet af siden Gennemse. Hvis en bruger adgang nægtet i korrekturen, er det trin, der fjerner deres rolletildeling.  

### <a name="export"></a>Eksportere

Hvis du vil anvende resultaterne af sikkerhed gennemsyn manuelt, kan du eksportere gennemgangen. Knappen **Eksporter** begynder at hente en CSV-fil. Du kan administrere resultaterne i Excel eller andre programmer, der åbne csv-filer.

### <a name="delete"></a>Slet

Hvis du ikke er interesseret i at gennemse yderligere, kan du slette den. Knappen **Slet** fjerner siden Gennemse fra programmet PIM.

> [AZURE.IMPORTANT] Du kan ikke får vist en advarsel, før sletningen opstår, så sørg for, at du vil slette, gennemse.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
