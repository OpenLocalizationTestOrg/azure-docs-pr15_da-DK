<properties
 pageTitle="Introduktion til Azure Scheduler Azure-portalen | Microsoft Azure"
 description="Introduktion til Azure Scheduler Azure-portalen"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introduktion til Azure Scheduler Azure-portalen

Det er nemt at oprette planlagte opgaver i Azure Scheduler. I dette selvstudium lærer du, hvordan du opretter en sag. Du lærer også Schedulers overvågning og administration funktioner.

## <a name="create-a-job"></a>Oprette en sag

1.  Log på [Azure-portalen](https://portal.azure.com/).  

2.  Klik på **+ Ny** > Skriv _Opgavestyring_ i søgefeltet > Vælg **Scheduler** i resultater > Klik på **Opret**.

     ![][marketplace-create]

3.  Lad os oprette en sag, du skal blot rammer http://www.microsoft.com/ med en GET-anmodning. Angiv følgende oplysninger i skærmbilledet **Scheduler Job** :

    1.  **Navn:**`getmicrosoft`  

    2.  **Abonnement:** Abonnementet Azure   

    3.  **Job af websteder:** Vælg en eksisterende job af websteder, eller klik på **Opret ny** > Angiv et navn.

4.  Derefter skal du angive følgende værdier i **Handlingsindstillinger**:

    1.  **Handlingstype:**` HTTP`  

    2.  **Metode:**`GET`  

    3.  **URL-adresse:**` http://www.microsoft.com`  

      ![][action-settings]

5.  Lad os definere til sidst en tidsplan. Jobbet kan defineres som et enkelt job, men lad os vælge en tidsplan for gentagelse:

    1. **Gentagelse**:`Recurring`

    2. **Start**: dags dato

    3. **Gentages hver**:`12 Hours`

    4. **Afslut**: to dage fra dags dato er dato  

      ![][recurrence-schedule]

6.  Klik på **Opret**

## <a name="manage-and-monitor-jobs"></a>Administrere og overvåge job

Når der oprettes et job, vises det i den primære Azure dashboard. Klikke på sag, og der åbnes et nyt vindue med følgende faner:

1.  Egenskaber  

2.  Handlingsindstillinger  

3.  Tidsplan  

4.  Oversigt over

5.  Brugere

    ![][job-overview]

### <a name="properties"></a>Egenskaber

Disse skrivebeskyttede egenskaber beskriver management metadataene for Scheduler jobbet.

   ![][job-properties]


### <a name="action-settings"></a>Handlingsindstillinger

At klikke på en sag i skærmbilledet **job** , kan du konfigurere jobbet. Dette kan du konfigurere avancerede indstillinger, hvis du ikke konfigurerer dem i den hurtige – Opret guiden.

Du kan ændre politikken prøv igen og handlingen fejl for alle typer af handling.

For HTTP og HTTPS job handling, kan du ændre metoden til en hvilken som helst tilladte HTTP-verber. Du kan også tilføje, slette eller ændre sidehoveder og basisgodkendelse oplysninger.

Du kan ændre lagerplads konto, kø navn, SAS token og brødteksten for lagerplads kø handlingstyper.

Du kan ændre navneområde, emne eller en kø sti, godkendelsesindstillinger, transporttype, meddelelsesegenskaber og meddelelsesteksten for tjenesten bus handling typer.

   ![][job-action-settings]

### <a name="schedule"></a>Tidsplan

Dette gør det muligt at omkonfigurere tidsplanen, hvis du vil ændre den plan, du oprettede i den hurtige – Opret guiden.

Dette er en mulighed for at oprette [komplekse tidsplaner og avancerede gentagelse i dit arbejde](scheduler-advanced-complexity.md)

Du kan ændre startdatoen og klokkeslæt, tidsplanen for gentagelse og slutningen dato og klokkeslæt (hvis opgaven er tilbagevendende.)

   ![][job-schedule]


### <a name="history"></a>Oversigt over

Fanen **Oversigt** viser markerede målepunkter til hver udførelse af job i systemet for det valgte job. Denne statistik indeholder realtid værdier angående tilstanden for din Scheduler:

1.  Status  

2.  Detaljer  

3.  Gentagne forsøg

4.  Forekomst: 1, 2., 3, osv.

5.  Starttidspunktet for udførelse af  

6.  Sluttidspunktet for udførelse af

   ![][job-history]

Du kan klikke på en Kør for at få vist dets **Oversigt over oplysninger**, herunder hele svaret for hver udførelse af. Denne dialogboks kan du kopiere svaret til Udklipsholder.

   ![][job-history-details]

### <a name="users"></a>Brugere

Azure rollebaseret Access kontrolelement (RBAC) gør det muligt for administration af detaljerede adgang til Azure Scheduler. Lære at bruge fanen brugere, skal du se [Azure_Role-Based adgangskontrol](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Se også

 [Hvad er Scheduler?](scheduler-intro.md)

 [Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Sådan oprettes komplekse tidsplaner og avancerede gentagelse med Azure Scheduler](scheduler-advanced-complexity.md)

 [Scheduler REST-API reference](https://msdn.microsoft.com/library/mt629143)

 [Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Begrænsninger for Opgavestyring, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Scheduler udgående godkendelse](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
