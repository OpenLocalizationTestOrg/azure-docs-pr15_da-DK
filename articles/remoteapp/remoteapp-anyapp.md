<properties
   pageTitle="Køre en hvilken som helst Windows-app på en hvilken som helst enhed med Azure RemoteApp | Microsoft Azure"
   description="Lær at dele en hvilken som helst Windows-app med dine brugere ved hjælp af Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Køre en hvilken som helst Windows-app på en hvilken som helst enhed med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Du kan køre et Windows-program et vilkårligt sted på en hvilken som helst enhed lige nu alvorligt - lige ved hjælp af Azure RemoteApp. Uanset om det er et brugerdefineret program, der er skrevet 10 år siden, eller en Office-app, skal brugerne ikke længere være bundet til et bestemt operativsystem (som Windows XP) for de pågældende par programmer.

Med Azure RemoteApp, kan brugerne også bruge deres egne Android- eller Apple-enheder og få den samme oplevelse, de har på Windows (eller på Windows-telefoner). Dette opnås ved at placere din Windows-program i en samling af Windows virtuelle maskiner på Azure - dine brugere kan få adgang til dem fra et vilkårligt sted de har forbindelse til internettet. 

Læs videre for et eksempel på at gøre dette.

I denne artikel, vi dele Access med alle vores brugere. Dog kan du bruge en app. Som du kan installere din app på en computer, Windows Server 2012 R2, kan du dele det at følge fremgangsmåden nedenfor. Du kan gennemse [app krav til](remoteapp-appreqs.md) at sikre, at din app fungerer.

Vær opmærksom på, fordi Access er en database, og vi vil databasen, der skal være nyttige, vi skal udføre nogle ekstra trin for at lade brugere får adgang til Access data del. Hvis din app ikke er en database, eller du ikke har brug for dine brugere skal kunne få adgang til et filshare, kan du springe disse trin i dette selvstudium

> [AZURE.NOTE] <a name="note"></a>Du skal bruge en Azure-konto til at udføre dette selvstudium:
> - Du kan [åbne en Azure-konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant op du holder kontoen, og brug ledig Azure tjenester, som websteder. Dit kreditkort vil aldrig blive faktureret, medmindre du eksplicit ændre dine indstillinger og bede om at betale.
> - Du kan [aktivere MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.


## <a name="create-a-collection-in-remoteapp"></a>Oprette en samling i RemoteApp

Starte med at oprette en samling. Samlingen fungerer som en beholder til dine apps og brugere. Hver af websteder er baseret på et billede – du kan oprette din egen eller bruge en forsynet med dit abonnement. I dette selvstudium bruger vi Office 2013 prøveversion billedet – den indeholder den app, vi vil dele.

1. I portalen Azure, Rul ned i den venstre navigationstræ indtil du ser RemoteApp. Åbn siden.
2. Klik på **Opret en RemoteApp af websteder**.
3. Klik på **Hurtig oprettelse** , og Angiv et navn til din samling.
4. Vælg det område, du vil bruge til at oprette din samling. Vælg det område, der er tættest geografisk til den placering, hvor brugerne skal have adgang til appen for at få den bedste oplevelse. For eksempel i dette selvstudium vil brugerne være placeret i Redmond, Washington. Det nærmeste Azure område er **Vest USA**.
5. Vælg den fakturering plan, du vil bruge. Den grundlæggende fakturering plan placerer 16 brugere i en stor Azure VM, mens den standard fakturering plan har 10 brugere på en stor Azure VM. Som et generelt eksempel velegnet på grundlæggende plan til data-posttype arbejdsproces. Til en produktivitet-app, som Office, vil standard planen.
6. Markér Office 2013 Professional billedet. Dette billede viser Office 2013-apps. Bare en påmindelse - dette billede er kun god til prøveversionen af websteder og POCs. Du ' kan ikke bruge dette billede i en fremstilling af websteder.
7. Klik nu **oprette RemoteApp af websteder**.

![Oprette en skyen samling i RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Dette starter oprette af websteder, men det kan tage op til en time.

Nu er du klar til at tilføje dine brugere.

## <a name="share-the-app-with-users"></a>Dele appen med brugere

Når din samling er blevet oprettet, er det tid at udgive adgang til brugere og føje de brugere, der skal have adgang til den.

Hvis du har navigeret væk fra noden Azure RemoteApp, mens samlingen scorecard, kan du starte med at finde tilbage til den fra Azure hjemmeside.

2. Klik på den samling, du har oprettet tidligere til at få adgang til flere indstillinger og konfigurere samlingen.
![En ny RemoteApp skyen samling](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Klik på **Publicer** nederst på skærmen på fanen **udgivelse** , og klik derefter på **programmer i menuen publicere Start**.
![Publicere et RemoteApp-program](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Vælg de apps, du vil publicere på listen. Vi valgte adgang til vores formål. Klik på **udført**. Vent apps for at afslutte udgivelse.
![Publicering adgang i RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Når programmet afsluttes publicering, afsnit til fanen **Brugeradgang** at tilføje alle de brugere, der skal have adgang til dine apps. Angive brugernavne (mailadresse) for dine brugere, og klik derefter på **Gem**.

![Føje brugere til RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Nu er det tid at fortælle brugerne om disse nye apps og hvordan du kan få adgang til dem. For at gøre dette, sende brugerne en mail, pege URL-adressen med Fjernskrivebord klient download.
![Klienten hente URL-adressen til RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Konfigurere adgang til Access

Visse apps skal ekstra konfiguration, når du installerer dem via RemoteApp. Især adgang vi til at oprette et filshare på Azure, der kan få adgang til alle brugere. (Hvis du ikke vil gøre det, kan du oprette en [hybrid samling](remoteapp-create-hybrid-deployment.md) [i stedet for vores skyen samling], der giver brugerne mulighed for at få adgang til filer og oplysninger på dit lokale netværk.) Derefter skal vi se vores brugere at tilknytte et lokalt drev på deres computer til Azure-filsystemet.

Den første del, du som administrator gøre. Vi har derefter nogle trin for dine brugere.

1. Start ved at publicere linje kommandogrænseflade (cmd.exe). Vælg **cmd**i fanen **udgivelse** , og klik derefter på **Publicer > Publicer program ved hjælp af sti**.
2. Skriv navnet på appen og stien. Bruge "Stifinder" som felterne navn og "% SYSTEMDRIVE%\windows\explorer.exe" som stien til vores formål.
![Publicere cmd.exe filen.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Nu skal du oprette en Azure [lagerplads konto](../storage/storage-create-storage-account.md). Vi med navnet vores "accessstorage", så Vælg et navn, der giver mening for dig. (Hvis du vil misquote Highlander, der kan være kun én "accessstorage.") ![Our Azure-lager-konto](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Gå nu tilbage til dit dashboard så du kan hente stien til dit lager (slutpunkt placering). Du skal bruge dette i lidt, så Vær sikker på, at du kopierer det et sted.
![Lagerplads konto stien](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Når kontoen lagerplads er blevet oprettet, skal du derefter den primære hurtigtast. Klik på **Administrer access-taster**, og derefter kopiere den primære hurtigtast.
6. Nu angive konteksten af lager-konto, og oprette et nyt filshare til Access. Kør følgende cmdletter i en øgede Windows PowerShell-vinduet:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Så til vores del, er disse cmdlet'er vi køre:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Nu er det brugerens slå. Først skal have dine brugere med at installere en [RemoteApp-klienten](remoteapp-clients.md). Brugerne skal derefter knytte et drev fra deres konto til dette Azure filshare, du har oprettet og tilføje deres få adgang til filer. Dette er, hvordan de gøre det:

1. Adgang til de publicerede apps i RemoteApp-klienten. Start cmd.exe-program.
2. Kør følgende kommando for at tilknytte et drev fra din computer til det pågældende filshare:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Hvis du angiver parameteren **/ fast** til Ja, bevares det tilknyttede drev på tværs af forskellige sessioner.
1. Nu skal starte appen Stifinder fra RemoteApp. Kopiere en hvilken som helst få adgang til filer, du vil bruge i den delte app til det pågældende filshare.
![Hvordan man anbringer få adgang til filer i en Azure del](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Til sidst skal Åbn Access, og Åbn derefter den database, du lige har delt. Du bør se dine data i Access, der kører fra skyen.
![Et reelt tal Access-database, der kører fra skyen](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Nu kan du bruge Access på alle dine enheder - lige Sørg for, at du har installeret et RemoteApp-klient.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Nu hvor du har styr på oprettelse af en samling, kan du prøve at oprette en [af websteder, der bruger Office 365](remoteapp-tutorial-o365anywhere.md). Eller du kan oprette en [hybrid af websteder ](remoteapp-create-hybrid-deployment.md), der kan få adgang til dit lokale netværk.

<!--Image references-->
 
