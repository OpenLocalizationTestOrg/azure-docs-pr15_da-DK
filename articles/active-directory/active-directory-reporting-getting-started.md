<properties
   pageTitle="Azure Active Directory-rapportering: Introduktion | Microsoft Azure"
   description="Viser en liste over de forskellige tilgængelige rapporter i Azure Active Directory-rapportering"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Introduktion til Azure Active Directory-rapportering

## <a name="what-it-is"></a>Det er

Azure Active Directory (Azure AD) indeholder sikkerhed, aktivitet og Overvåg rapporter til adresselisten. Her er en liste over de rapporter, der er inkluderet:

### <a name="security-reports"></a>Sikkerhedsrapporter

- Logon fra ukendte kilder
- Logon efter flere fejl
- Logon fra forskellige lande
- Logon fra IP-adresser med mistænkelig aktivitet
- Uregelmæssige logon aktivitet
- Logon fra muligvis inficerede enheder
- Brugere med uoverensstemmende logon-aktiviteter

### <a name="activity-reports"></a>Aktivitetsrapporter

- Brug af program: Oversigt
- Brug af program: detaljerede
- Programmet dashboard
- Klargøringsfejl-konto
- Individuel Brugerenheder
- Individuel bruger aktivitet
- Grupper aktivitetsrapport
- Adgangskode Nulstil registrering aktivitetsrapport
- Aktivitet til nulstilling af adgangskode

### <a name="audit-reports"></a>Overvågningslog rapporter

- Directory overvågningslog rapport

> [AZURE.TIP] Se [få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)flere dokumentation på Azure AD-rapportering.



## <a name="how-it-works"></a>Sådan fungerer det


### <a name="reporting-pipeline"></a>Pipeline-rapportering

Rapportering pipeline består af tre hovedtrin. Hver gang en bruger logger på, eller en godkendelse er gjort, sker der følgende:

- Først skal brugeren er godkendt (korrekt eller ikke lykkes), og resultatet er gemt i Azure Active Directory-tjenesten databaser.
- Med jævne mellemrum, alle seneste log ins behandles. På dette tidspunkt vores sikkerhed og uoverensstemmende aktivitet algoritmer søger alle seneste log in-programmer for mistænkelig aktivitet.
- Efter behandling, er rapporterne, der skrevet, cachelagret, og served i portalen Azure klassisk.

### <a name="report-generation-times"></a>Rapportere tid at oprette

På grund af den store mængde godkendelse og logge ins behandlet af Azure AD-platformen, er de seneste logon behandles, i gennemsnit én time gammel. Det kan tage op til otte timer at behandle de seneste logon i sjældne tilfælde.

Du kan finde den seneste behandlede logon ved at undersøge Hjælp-teksten øverst på hver rapport.

![Hjælp-tekst øverst på hver rapport](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Se [få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)flere dokumentation på Azure AD-rapportering.



## <a name="getting-started"></a>Kom godt i gang


### <a name="sign-into-the-azure-classic-portal"></a>Log på portalen Azure klassisk

Først skal du logge på [Azure klassisk portal](https://manage.windowsazure.com) som global eller overholdelse administrator. Du skal også være en Azure abonnement tjenesteadministratoren eller samtidig administrator, eller brug "Adgang til Azure AD" Azure-abonnement.

### <a name="navigate-to-reports"></a>Gå til rapporter

For at se rapporter, skal du gå til fanen rapporter øverst i mappen.

Hvis dette er første gang, få vist rapporterne, skal du acceptere en dialogboks, før du kan få vist rapporterne. Dette er at sikre, at det er acceptabel for administratorer i din organisation til at få vist disse data, som kan betragtes som private oplysninger i nogle lande.

![I dialogboksen](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Udforske hver rapport

Gå til hver rapport for at se de data, der indsamles og de log-ins, behandles. Du kan finde en [liste over alle rapporter her](active-directory-reporting-guide.md).

![Alle rapporter](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Hente rapporterne som CSV

Hver rapport kan hentes som en CSV-fil (kommaseparerede værdier). Du kan bruge disse filer i Excel, PowerBI eller tredjeparts analyse-programmer til at yderligere analysere dine data.

Gå til rapporten for at hente en rapport som en CSV-fil, og klik på "Hent" nederst.

![Knappen Hent](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Se [få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)flere dokumentation på Azure AD-rapportering.





## <a name="next-steps"></a>Næste trin

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Tilpasse beskeder for uoverensstemmende log i aktivitet

Gå til fanen "Konfigurer" i mappen.

Rul ned til sektionen "Beskeder".

Aktivere eller deaktivere afsnittet "E-mail-meddelelser om uoverensstemmende logon".

![Afsnittet meddelelser](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrere med den Azure AD API-rapportering

Se [Introduktion til API rapportering](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Deltage Multi-Factor Authentication på brugere

Vælg en bruger i en rapport.

Klik på knappen "Aktivere MFA" nederst på skærmen.

![Knappen Multi-Factor Authentication nederst på skærmen](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Se [få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)flere dokumentation på Azure AD-rapportering.




## <a name="learn-more"></a>Lær mere


### <a name="audit-events"></a>Overvågningshændelser

Få mere at vide om, hvilke hændelser der skal overvåges i mappen i [Azure Active Directory rapportering overvågningshændelser](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>API-Integration

Se [Introduktion til rapportering API](active-directory-reporting-api-getting-started.md) og [API referencedokumentation](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Komme i kontakt

Mail [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) til feedback, Hjælp eller har du muligvis har spørgsmål.

> [AZURE.TIP] Se [få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)flere dokumentation på Azure AD-rapportering.
