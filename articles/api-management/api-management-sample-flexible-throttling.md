<properties
    pageTitle="Avanceret anmodning (throttling) med Azure API Management"
    description="Lær at oprette og anvende fleksible kvote og rente begrænse politikker til Azure API Management."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Avanceret anmodning (throttling) med Azure API Management

Der kunne begrænses indgående anmodninger er en vigtig rolle af Azure API Management. Enten ved at styre hastigheden af anmodninger om eller den samlede anmodninger/data, der overføres, API administration giver mulighed for udbydere af API til at beskytte deres API'er fra misbrug og oprette værdi for andre API produkt niveauer.

## <a name="product-based-throttling"></a>Produkt baseret (throttling)
Indtil dags dato, rente (throttling) er funktioner begrænset til fastsat til et bestemt produkt abonnement (grundlæggende taster), der er defineret i portalen API Management publisher. Dette er nyttigt til provideren API til at anvende begrænsninger på de udviklere, som har tilmeldt sig til at bruge deres API, men det hjælper ikke, for eksempel i (throttling) individuelle slutbrugere af API. Det er muligt, til en enkelt bruger af den udvikler programmer til at bruge kvoten for hele og derefter forhindre andre kunder udvikleren, kan du bruge programmet. Desuden kan flere kunder, der kan generere en stor mængde anmodninger begrænse adgangen til lejlighedsvise brugere.

## <a name="custom-key-based-throttling"></a>Brugerdefineret nøgle baseret (throttling)
De nye [rente grænse ved nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) og [kvote-ved-nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) politikker giver en betydeligt mere fleksibel løsning til styring af netværkstrafik. Disse nye politikker gør det muligt at definere udtryk for at identificere de pågældende taster, der skal bruges til at registrere trafik brugen. Den måde, det fungerer illustreres easiest med et eksempel. 

## <a name="ip-address-throttling"></a>IP-adresse (throttling)
Følgende politikker begrænse en enkelt klient IP-adresse til kun 10 opkald hvert minut, med en sum af 1.000.000 opkald og 10.000 kilobyte af båndbredde på månedsbasis. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Hvis alle klienter på internettet har brugt en entydig IP-adresse, kan det være en effektiv metode til at begrænse brugen af bruger. Det er dog helt sandsynligvis, at flere brugere kan dele en enkelt offentlige IP-adresse på grund af dem adgang til internettet via en NAT-enhed. På trods af dette for API'er, der tillader ikke-godkendte adgang på `IpAddress` kan være den bedste løsning.

## <a name="user-identity-throttling"></a>Bruger identitet (throttling)
Hvis en slutbruger godkendes derefter kan oprettes en variere den benyttede nøgle baseret på oplysninger, der entydigt identificerer en, der bruger.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

I dette eksempel vi uddrage overskriften tilladelse, kan du konvertere den til `JWT` objekt og bruge emnet for tokenet til at identificere brugeren og bruge det som den rente, begrænse nøgle. Hvis bruger-id er gemt i den `JWT` som én af de andre krav derefter, at værdien kan bruges i stedet.

## <a name="combined-policies"></a>Kombinerede politikker
Selvom de nye variere den benyttede politikker giver mere kontrol end de eksisterende variere den benyttede politikker, er der stadig værdi, der kombinerer begge muligheder. (Throttling) af produktnøgle i forbindelse med abonnement ([grænse opkald rente ved abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) og [angive kvoten for anvendelse af abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) er en god metode til at aktivere monetizing af en API ved opladning baseret på brugen niveauer. Få mere kornet kontrolelementet for at kunne begrænses af bruger er komplementære og forhindrer funktionsmåde for én bruger forringer oplevelse af en anden. 

## <a name="client-driven-throttling"></a>Klient indsatsbaserede (throttling)
Når tasten variere den benyttede defineres ved hjælp af en [politikudtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx), er den API-udbyder, skal du vælge den (throttling) hvordan er fastsat. Men en udvikler måske til at styre, hvordan de vurdere grænse deres egne kunder. Dette kan være aktiveret af API-udbyder ved at introducere et brugerdefineret sidehoved at tillade den udvikler-klientprogrammet til at kommunikere nøglen til API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Dette gør det muligt for den udvikler-klientprogrammet til at vælge, hvordan de skal oprette den rente, begrænse nøgle. En klient udvikler kunne oprette deres egne rente niveauer ved tildeling af sæt af taster til brugere og rotere brugen af nøgler med et lille stykke ingenuity.

## <a name="summary"></a>Oversigt
Azure API Management indeholder rente og tilbud (throttling) Hvis du både Beskyt og føje værdi til din API-tjeneste. De nye variere den benyttede politikker med brugerdefinerede angiver området regler kan du få mere kornet kontrol over disse politikker til at aktivere dine kunder til at oprette endnu bedre programmer. Eksemplerne i denne artikel viser brugen af disse nye politikker om produktionsanlæg rente begrænse taster med Klienternes IP-adresser, bruger-id og klient, der genereres værdier. Der er dog mange andre dele af den meddelelse, der kan bruges som brugeragent, URL-sti fragmenter, meddelelsesstørrelse.

## <a name="next-steps"></a>Næste trin
Giv os din feedback i Disqus tråd i dette emne. Det ville være praktisk at høre om andre mulige nøgleværdier, der er en logisk valg i din scenarier.

## <a name="watch-a-video-overview-of-these-policies"></a>Se en video oversigt over disse politikker
Flere oplysninger om de [rente grænse ved nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) og [kvote-ved-nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) politikker, der er dækket i denne artikel, skal du se følgende video.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
