## <a name="prerequisites"></a>Forudsætninger

Før vi kan skrive CDN management kode, skal vi gøre nogle forberedelser til at aktivere vores kode til at arbejde med Azure ressourcestyring.  For at gøre dette, skal du:

* Oprette en ressourcegruppe indeholde profilen CDN vi oprette i dette selvstudium
* Konfigurere Azure Active Directory for at angive godkendelse for vores program
* Anvende tilladelser på ressourcegruppen, så kun godkendte brugere fra vores Azure AD-lejer kan interagere med vores CDN profil

### <a name="creating-the-resource-group"></a>Oprette ressourcegruppen

1. Log på [Azure-portalen](https://portal.azure.com).

2. Klik på knappen **Ny** i øverst til venstre, og klik derefter **Management**og **Ressourcegruppe**.
    
    ![Oprette en ny ressourcegruppe](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Ring til din ressourcegruppe *CdnConsoleTutorial*.  Vælg dit abonnement, og vælg en placering i nærheden af dig.  Hvis du vil, kan du klikke på afkrydsningsfeltet **Fastgør til dashboard** for at fastgøre ressourcegruppe til dashboard i portalen.  Dette vil gøre det nemmere at finde senere.  Når du har foretaget dine valg, skal du klikke på **Opret**.

    ![Navngive ressourcegruppen](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Efter ressourcegruppen er oprettet, hvis du ikke har fastgøre den til dit dashboard, kan du finde den ved at klikke på **Gennemse**og derefter **Ressourcegrupper**.  Klik på ressourcegruppen for at åbne den.  Skal du notere af dit **Abonnement-ID**.  Vi skal bruge den senere.

    ![Navngive ressourcegruppen](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Oprette Azure AD-program og anvender tilladelser

Der er to måder at app godkendelse med Azure Active Directory: individuelle brugere eller en tjeneste sikkerhedskonto. En tjeneste vigtigste ligner en tjenestekonto i Windows.  I stedet for at tildele en bestemt bruger tilladelser til at interagere med CDN profiler, give vi i stedet tilladelser til den primære tjeneste.  Principper for tjenesten bruges normalt til automatiseret, ikke-interaktiv processer.  Selvom dette selvstudium skriver en interaktiv console-app, fokuserer vi på den tjeneste vigtigste tilgang.

Oprette en tjeneste sikkerhedskonto består af flere trin, herunder oprettelse af et Azure Active Directory-program.  For at gøre dette, vi at [følge dette selvstudium](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Sørg for at følge trinnene i den [sammenkædede selvstudium](../articles/resource-group-create-service-principal-portal.md).  Det er *meget vigtigt* , at du fuldfører den nøjagtigt, som beskrevet.  Husk at notere din **lejer-ID**, **lejer domænenavn** (normalt en *. onmicrosoft.com* domæne medmindre du har angivet et brugerdefineret domæne), **klient-ID**og **klienten godkendelsesnøgle**, som vi skal bruge disse senere.  Pas på meget til at beskytte din **klient-ID** og **klienten godkendelsesnøgle**, som disse legitimationsoplysninger kan bruges af andre til at udføre handlinger som service hovedstolen. 
>   
> Vælg **ingen**, når du kommer til trin med navnet [Konfigurer flere lejer programmet](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application).
> 
> Når du kommer til trin [tildeler program til rolle](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), skal du bruge ressourcegruppen vi oprettede tidligere, *CdnConsoleTutorial*, men i stedet for rollen **læser** , tildele rollen **CDN profil bidragyder** .  Når du tildeler programmet rollen **CDN profil bidragyder** på ressourcegruppe, kan du gå tilbage til dette selvstudium. 

Når du har oprettet din tjeneste vigtigste og tildelt rollen **CDN profil bidragyder** , bladet **brugere** til din ressourcegruppe skal ligne dette.

![Brugere blade](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Interaktive brugergodkendelse

Hvis du i stedet for en tjeneste sikkerhedskonto, vil du have interaktive individuelle brugergodkendelse, minder processen meget, til en tjeneste sikkerhedskonto.  Faktisk skal du følge den samme fremgangsmåde, men foretage nogle mindre ændringer.

> [AZURE.IMPORTANT] Følg kun disse næste trin, hvis du vælger at bruge individuelle brugergodkendelse i stedet for en primære tjeneste.

1. Når du opretter dit program i stedet for **Webprogram**, skal du vælge **oprindelige program**. 
    
    ![Oprindelige program](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. På den næste side, vil du blive bedt om en **omdirigere URI**.  URI valideres ikke, men husk, hvad du har angivet.  Du skal bruge den senere. 

3. Det er ikke nødvendigt at oprette en **klient godkendelsesnøgle**.

4. I stedet for at tildele en tjeneste sikkerhedskonto til rollen **CDN profil bidragyder** , vi tildele individuelle brugere eller grupper.  I dette eksempel kan du se, at jeg har tildelt *CDN Demo bruger* rollen **CDN profil bidragyder** .  
    
    ![Individuelle brugeradgang](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

