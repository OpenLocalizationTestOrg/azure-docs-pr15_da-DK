<properties
     pageTitle="Hvordan du knytter Azure Content Delivery netværk (CDN) indhold til et brugerdefineret domæne | Microsoft Azure"
     description="Dette emne viser, hvordan du knytter et CDN indhold til et brugerdefineret domæne."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Sådan tilknyttes brugerdefineret domæne til indhold levering netværk (CDN) slutpunkt
Du kan knytte et brugerdefineret domæne til et CDN slutpunkt for at bruge dit eget domænenavn i URL-adresser til cachelagret indhold i stedet for at bruge et underdomæne til azureedge.net.

Der er to måder at tilknytte dit brugerdefinerede domæne til et CDN slutpunkt:

1. [Oprette en CNAME-post med din domæneregistrator og knytte dit eget domæne og underdomæne til CDN slutpunktet](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    En CNAME-post er en DNS-funktion, der knytter et kilde-domæne, vil `www.contosocdn.com` eller `cdn.contoso.com`, til en destinationsdomænet. I dette tilfælde er kildedomænet dit eget domæne og underdomæne (et underdomæne, såsom **www** eller **cdn** er altid påkrævet). Destinationsdomænet er din CDN slutpunkt.  

    Processen med at tilknytte dit brugerdefinerede domæne til din CDN slutpunkt kan dog resultere i en kort periode nedetid for domænenavn, mens du registrerer domænet i portalen Azure.

2. [Tilføje en mellemliggende registrering trin med **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Hvis dit brugerdefinerede domæne understøtter i øjeblikket et program med en serviceniveauaftale (SLA), der kræver, at der ikke nogen nedetid, kan du bruge Azure **cdnverify** underdomænet til at levere en mellemliggende registrering trin, så brugerne kan få adgang til dit domæne under DNS tilknytning finder sted.  

Når du har registreret dit brugerdefinerede domæne ved hjælp af en af ovenstående fremgangsmåder, vil du [bekræfte, at det brugerdefinerede underdomæne henviser til din CDN slutpunkt](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Du skal oprette en CNAME-post med din domæneregistrator skal tilknyttes CDN slutpunktet dit domæne. CNAME-poster tilknytte specifikke underdomæner såsom `www.contoso.com` eller `cdn.contoso.com`. Det er ikke muligt at tilknytte en CNAME-post til et roddomæne som `contoso.com`.
>    
> Et underdomæne kan kun være knyttet til et CDN slutpunkt. CNAME-posten, som du opretter dirigere al trafik til underdomænet til det angivne slutpunkt.  Hvis du knytte eksempelvis `www.contoso.com` til din CDN slutpunkt, så du kan ikke tilknytte andre Azure slutpunkter, som et lager konto slutpunkt eller en skybaseret tjenesteslutpunkt. Dog kan du bruge forskellige underdomæner fra det samme domæne til anden tjeneste slutpunkter. Du kan også knytte forskellige underdomæner til den samme CDN slutpunkt.
>
> Bemærk, at det tager til **90 minutter** til brugerdefineret domæne ændringer kan overføres til CDN kant noder til **Azure CDN fra Verizon** (Standard og Premium) slutpunkter.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrere et brugerdefineret domæne til Azure CDN ark

1.  Log på [Azure-portalen](https://portal.azure.com/).
2.  Klik på **Gennemse**, derefter **CDN profiler**, derefter CDN profilen med det slutpunkt, du vil knytte til et brugerdefineret domæne.  
3.  Klik på det CDN slutpunkt, som du vil knytte underdomænet i bladet **CDN profil** .
4.  Klik på knappen **Tilføj brugerdefineret domæne** øverst i bladet slutpunkt.  I bladet **tilføjes et brugerdefineret domæne** , får du vist værtsnavn slutpunkt, udledt fra din CDN slutpunkt, du bruger i at oprette en ny CNAME-post. Formatet af adressen på navn vises som ** &lt;EndpointName >. azureedge.net**.  Du kan kopiere dette værtsnavn til brug ved oprettelse af CNAME-posten.  
5.  Gå til domæneregistratorens websted, og Find sektionen til oprettelse af DNS-poster. Du kan finde det i en sektion som **Domænenavn**, **DNS**eller **Name Server Management**.
6.  Find sektionen til administration af CNAMEs. Du kan være nødvendigt at gå til en side, avancerede indstillinger, og se efter ordene, CNAME, Alias eller underdomæner.
7.  Oprette en ny CNAME-post, der knytter et valgte underdomæne (for eksempel **www** eller **cdn**) til værtsnavnet fremgår bladet **tilføjes et brugerdefineret domæne** .
8.  Gå tilbage til bladet **tilføjes et brugerdefineret domæne** , og Angiv dit eget domæne, herunder underdomænet, i dialogboksen. For eksempel Indtast domænenavn i formatet `www.contoso.com` eller `cdn.contoso.com`.   

    Azure kontrollere, at der findes CNAME-posten for det domænenavn, du har angivet. Hvis CNAME er korrekte, valideres dit eget domæne.  Det kan tage op til 90 minutter til brugerdefineret Domæneindstillinger at sprede til alle CDN kant noder, men til **Azure CDN fra Verizon** (Standard og Premium) slutpunkter.  

    Bemærk, at i nogle tilfælde kan det tage tid for CNAME-posten kan overføres til DNS-servere på internettet. Hvis dit domæne ikke er blevet godkendt med det samme, og du mener, er korrekte CNAME-posten, vent et par minutter, og prøv igen.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrere et brugerdefineret domæne til Azure CDN ark ved hjælp af mellemliggende cdnverify underdomæne  

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse**, derefter **CDN profiler**, derefter CDN profilen med det slutpunkt, du vil knytte til et brugerdefineret domæne.  
3. Klik på det CDN slutpunkt, som du vil knytte underdomænet i bladet **CDN profil** .
4. Klik på knappen **Tilføj brugerdefineret domæne** øverst i bladet slutpunkt.  I bladet **tilføjes et brugerdefineret domæne** , får du vist værtsnavn slutpunkt, udledt fra din CDN slutpunkt, du bruger i at oprette en ny CNAME-post. Formatet af adressen på navn vises som ** &lt;EndpointName >. azureedge.net**.  Du kan kopiere dette værtsnavn til brug ved oprettelse af CNAME-posten.
5. Gå til domæneregistratorens websted, og Find sektionen til oprettelse af DNS-poster. Du kan finde det i en sektion som **Domænenavn**, **DNS**eller **Name Server Management**.
6. Find sektionen til administration af CNAMEs. Du kan være nødvendigt at gå til en side, avancerede indstillinger, og se efter ordene **CNAME** **Alias**eller **underdomæner**.
7. Oprette en ny CNAME-post, og Giv et underdomæne alias, der indeholder **cdnverify** underdomænet. For eksempel være det underdomæne, du angiver i formatet **cdnverify.www** eller **cdnverify.cdn**. Giv dernæst værtsnavn, som er dit CDN slutpunkt i formatet **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Gå tilbage til bladet **tilføjes et brugerdefineret domæne** , og Angiv dit eget domæne, herunder underdomænet, i dialogboksen. For eksempel Indtast domænenavn i formatet `www.contoso.com` eller `cdn.contoso.com`. Bemærk, at i dette trin skal du ikke behøver at skrive underdomænet med **cdnverify**.  

    Azure kontrollere, at der findes CNAME-posten for den cdnverify domænenavn, du har angivet.
9. På dette tidspunkt dit eget domæne er blevet godkendt af Azure, men er ikke dirigeres trafik til dit domæne til din CDN slutpunkt. Når vente længe nok til at tillade de brugerdefinerede Domæneindstillinger kan overføres til noderne CDN kant (90 minutter til **Azure CDN fra Verizon**, 1-2 minutter til **Azure CDN fra Akamai**), du vende tilbage til DNS-udbyderens websted og oprette en anden CNAME-post knytter, der et underdomæne til din CDN slutpunkt. For eksempel angiver underdomænet som **www** eller **cdn**og hostname som ** &lt;EndpointName >. azureedge.net**. Registrering af dit eget domæne er færdig med dette trin.
10. Til sidst, kan du slette den CNAME-post, du har oprettet ved hjælp af **cdnverify**, som den var nødvendige kun som en mellemliggende trin.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Kontrollér, at det brugerdefinerede underdomæne henviser CDN første eller sidste ark

- Når du har fuldført registrering af dit eget domæne, kan du få adgang til indhold, der er cachelagret på din CDN slutpunktet, ved hjælp af dit eget domæne.
Først skal du sikre dig, at du har offentlige indhold, der er cachelagret på slutpunktet. Hvis din CDN slutpunkt er knyttet til en lagerplads-konto, cachelagres CDN eksempelvis indhold i offentlige blob beholdere. Kontrollér, at din objektbeholder er indstillet til Tillad offentlig adgang og, at den indeholder mindst én blob for at teste dit eget domæne.
- I din browser skal du gå til adressen på blob ved hjælp af dit eget domæne. Hvis dit brugerdefinerede domæne er eksempelvis `cdn.contoso.com`, URL-adressen til en cachelagrede blob bliver svarende til følgende URL-adressen: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Se også

[Hvordan du aktiverer netværket til levering af indhold (CDN) til Azure](./cdn-create-new-endpoint.md)  
