<properties
    pageTitle="Sådan køber du et brugerdefineret domænenavn i Azure App Service Web Apps"
    description="Lær, hvordan du køber et brugerdefineret domænenavn med en WebApp i Azure App Service."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>Købe og konfigurere et brugerdefineret domænenavn i Azure App Service

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Når du opretter en web-app, tildeler Azure den til et underdomæne til azurewebsites.net. Hvis din online hedder **contoso**, er URL-adressen for eksempel **contoso.azurewebsites.net**. Azure tildeler også en virtuel IP-adresse.

En fremstilling web App vil du sandsynligvis brugerne skal have vist et brugerdefineret domænenavn. I denne artikel forklarer, hvordan du køber og konfigurere et brugerdefineret domæne med [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). 

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## <a name="overview"></a>Oversigt

Hvis du ikke har et domænenavn til din online, kan du nemt købe en på [Azure-portalen](https://portal.azure.com/). Du kan vælge at har WWW og rod domænets DNS-poster, der knyttes til din online automatisk under købet. Du kan også administrere dit domæne direkte i Azure-portalen.


Brug følgende trin til at købe domænenavne og tildele til din online.

1. I din browser skal du åbne [Azure-portalen](https://portal.azure.com/).

2. Fanen **Web Apps** , klik på navnet på din online skal du vælge **Indstillinger**og derefter vælge **brugerdefinerede domæner**

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Klik på **Køb domæner**i bladet **Custom domains** .

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Brug tekstfeltet i bladet **Købe domæner** til at skrive det domænenavn, du vil købe, og tryk på Enter. De foreslåede tilgængelige domæner vises lige under tekstboksen. Vælg det domæne, du vil købe. Du kan vælge at købe flere domæner på én gang. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Klik på **Kontaktoplysninger om** og udfylde domænets kontaktoplysninger formular.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] Det er meget vigtigt, at du udfylde alle de obligatoriske felter med den ønskede nøjagtighed som muligt, især mailadresse. I tilfælde af købe domænet uden "Beskyttelse af personlige", kan du blive bedt om at bekræfte din mail, før domænet aktiveres. I nogle tilfælde medfører forkerte data til kontaktoplysninger fejl ved at købe domæner. 

6. Nu kan du vælge at,

    a) "automatisk fornyelse" domænet hvert år
    
    b) tilføjelsesprogram til "Beskyttelse" der findes i indkøbsprisen gratis (med undtagelse af TLD'er der registreringsdatabasen ikke understøtter beskyttelse af personlige oplysninger. For example:. co.in,. co.uk osv.)  
    
    c) "tildeler standard værtsnavne" til WWW og root domæne til det aktuelle Web App. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Indstillingen C konfigurerer DNS bindinger og Hostname bindinger automatisk for dig.  Denne måde, kan din online åbnes ved hjælp af brugerdefinerede domæne, så snart købet er fuldført (baring DNS-overførsel forsinkelser i nogle tilfælde). I tilfælde din online er bag Azure trafik Manager, kan du ikke kan se muligheden for at tildele roddomænet, som A-poster ikke fungerer med trafik Manager. Du kan altid tildele den domæner/regionale-domains købt gennem en Web App til en anden Web App og omvendt. Se trin 8 for at få mere at vide. 
    
7. Klik på **Vælg** på **Køb domæner** blade, og derefter kan du se oplysningerne om køb på **Køb bekræftelse** blade. Hvis du accepterer de juridiske vilkår og klikke på **Køb**, sendes din ordre, og du kan overvåge indkøb processen på **besked**. Køb af domæne kan tage nogle minutter at gennemføre. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Hvis du er blevet bestilt et domæne, kan du administrere domænet og tildele til din online. Klik på den **"..."** på højre side af dit domæne. Derefter kan du **annullere køb** eller **Administrer domæne**. Klik på **Administrer domæne**, derefter vi kan binde **underdomæne** til vores online på **Administrer domæne** blade. Hvis du vil binde et **underdomæne** til en anden WebApp derefter udføre dette trin fra i forbindelse med de respektive Web App. Du vælger for at tildele domænet til trafik manager slutpunkt (Hvis der er online bag TM) ved blot vælge trafik manager navn på rullelisten over her. Ved at gøre dette, tildeles domæne/underdomæne automatisk til alle de Web Apps bag pågældende trafik Manager slutpunkt. 

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] Du kan "annullere køb" inden for 5 dage til fuld refusion. Når 5 dage, du vil kunne ikke "Annuller køb", i stedet du får vist muligheden for at "Slette" domænet. Slette domænenavn, medfører frigivelse fra dit abonnement uden refusion og bliver tilgængelige domæne. 

Når konfigurationen er fuldført, vises det brugerdefinerede domænenavn i sektionen **Hostname bindinger** for din online.

Du skal på dette tidspunkt ikke kunne Angiv navnet på det brugerdefinerede domæne i din browser, og se, at den er blevet tager dig til din online.
 
## <a name="what-happens-to-the-custom-domain-you-bought"></a>Hvad sker der med dit eget domæne, du har købt

Dit eget domæne, du har købt i bladet **brugerdefinerede domæner og SSL** er bundet til Azure abonnementet. Som en ressource som Azure, er denne brugerdefinerede domæne adskilte og uafhængige fra App Service app, som du først har købt til domænet for. Det betyder, at:

- Du kan bruge dit eget domæne, du har købt i portalen Azure for mere end én App Service app og ikke kun for den app, du først har købt dit eget domæne til. 
- Du kan administrere de brugerdefinerede domæner, du har købt i Azure-abonnement ved at gå til den **brugerdefinerede domæner og SSL** blade af *enhver* App Service app i dette abonnement.
- Du kan tildele en App Service-app fra det samme Azure abonnement til et underdomæne i det brugerdefinerede domæne.
- Hvis du beslutter at slette en App Service-app, kan du vælge ikke at slette dit eget domæne, som det er bundet til, hvis du vil fortsætte med at bruge det til andre apps.

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>Hvis du ikke kan se dit eget domæne, du købte

Hvis du har købt dit eget domæne fra i bladet **brugerdefinerede domæner og SSL** , men ikke kan se dit eget domæne under **administrerede domæner**skal du kontrollere følgende ting:

- Oprettelse af det brugerdefinerede domæne er muligvis ikke blevet sendt. Se klokken meddelelse øverst i portalen Azure for status.
- Oprettelse af det brugerdefinerede domæne kan være mislykket eller anden grund. Se klokken meddelelse øverst i portalen Azure for status.
- Dit eget domæne muligvis har fuldført, men bladet er muligvis ikke opdateret endnu. Prøv at åbne igen bladet **brugerdefinerede domæner og SSL** .
- Du har slettet dit eget domæne på et tidspunkt. Kontrollere overvågningsloggene ved at klikke på **Indstillinger for** > **Overvåge logfiler** fra din app primære blade. 
- Bladet **brugerdefinerede domæner og SSL** , du leder i kan tilhøre en app, der er oprettet i et andet Azure-abonnement. Skifte til en anden app i et andet abonnement, og Markér den **brugerdefinerede domæner og SSL** blade.  
  I portalen, kan du ikke kunne se og administrere brugerdefinerede domæner, der er oprettet i et andet Azure abonnement end appen. Men hvis du klikker på **Avanceret administration** i domænets **Administrer domæne** blade, bliver du omdirigeret til domæne-udbyderens websted, hvor du vil kunne   [konfigurere dit eget domæne som en hvilken som helst eksterne eget domæne](web-sites-custom-domain-name.md)  
   til apps, der er oprettet i et andet Azure-abonnement. 


