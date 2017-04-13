<properties 
    pageTitle="Hvordan bruge Blitline til billede behandling - Azure funktionen vejledning" 
    description="Lær at bruge tjenesten Blitline til proces billeder i et Azure-program." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Sådan bruger du Blitline med Azure og Azure-lager

Denne vejledning forklarer, hvordan du kan få adgang til Blitline tjenester, og hvordan du kan føje job til Blitline.

## <a name="what-is-blitline"></a>Hvad er Blitline?

Blitline er en skybaseret billede behandling tjeneste, som indeholder enterprise niveau billedbehandling for en brøkdel af den pris, det vil koste for at opbygge det selv.

Faktum er, at billedbehandling er blevet udført igen og igen som regel genopbygges fra bunden til hver websted. Vi opdager dette, fordi vi har oprettet dem en million tidspunkter for. Én dag Vi besluttede dig for, at måske er det tid gøre vi kun det for alle. Vi vide, hvordan du gør det, kan du gøre det hurtigt og effektivt og gemme alle arbejds i mellemtiden.

Du kan finde flere oplysninger [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Hvad Blitline er ikke...

Hvis du vil gøre det klart, hvad Blitline er nyttige til, er det ofte nemmere at identificere, hvad Blitline ikke før fremover.

- Blitline har ikke HTML-knapper til at overføre billeder. Du skal have tilgængelige billeder, offentligt eller med begrænsede tilladelser, der er tilgængelige for Blitline når.

- Blitline ikke livevisningen behandling, som Aviary.com

- Blitline accepterer ikke billedoverførsler, du kan ikke overføre billederne til Blitline direkte. Du skal overføre dem til Azure-lager eller andre steder Blitline understøtter og derefter Fortæl Blitline Sådan kommer du hente dem.

- Blitline er stort omfang parallelle og ikke en hvilken som helst synkron behandling. Hvilket betyder, at du skal give os en postback_url og vi kan fortælle dig, når vi har kataloger.

## <a name="create-a-blitline-account"></a>Oprette en Blitline-konto

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Sådan oprettes et Blitline-job

Blitline bruger JSON til at definere de handlinger, du vil tage på et billede. Denne JSON består af et par enkle felter.

Det nemmeste eksempel er som følger:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Her har vi JSON, som fører en "src" billede "... boys.jpeg" og derefter tilpasse størrelsen på billedet til 240 x 140.

Program-ID er noget, du kan finde dit **FORBINDELSESOPLYSNINGER** eller **ADMINISTRER** under fanen på Azure. Det er dit hemmeligt id, der gør det muligt at køre job på Blitline.

Parameteren "Gem" identificerer oplysninger om, hvor du vil placere billedet, når den er blevet behandlet. I dette tilfælde er trivial ikke har vi defineret en. Hvis ingen placering er defineret Blitline gemmer den lokalt (og midlertidigt) på et entydigt skyen placering. Du vil kunne få den nye placering fra JSON returneres af Blitline, når du foretager i Blitline. "Billede" id er påkrævet og returneres til dig, når der gemmes billede til at identificere denne bestemte.

Du kan finde flere oplysninger om de *Funktioner* , som vi understøtter her: <http://www.blitline.com/docs/functions>

Du kan også finde dokumentation om job indstillingerne her: <http://www.blitline.com/docs/api>

Når du har din JSON alt, du skal gøre, er **Send** den til`http://api.blitline.com/job`

Du får JSON tilbage, der ser nogenlunde sådan ud:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Dette fortæller dig, at Blitline har modtaget din anmodning, det er indsat i en behandlingskø, og når den er fuldført billedet være tilgængelige på: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Sådan gemmes et billede til kontoen Azure-lager

Hvis du har en Azure-lager-konto, kan du nemt få Blitline push behandlede billeder til din Azure beholder. Du kan definere placeringen af og tilladelser for Blitline til at overføre til ved at tilføje en "azure_destination".

Her er et eksempel:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Ved at udfylde værdierne CAPITALIZED med dine egne, kan du sende denne JSON til http://api.blitline.com/job og "src" billedet skal behandles med et filter for Mindsk skarpheden og derefter overføres til Azure destination.

###<a name="please-note"></a>Vær opmærksom på:

Sikkerhedstilknytningerne skal indeholde hele SAS URL-adressen, inklusive filnavnet i destinationsfilen.

Eksempel:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Du kan også læse den seneste udgave af Blitline's Azure-lager dokumenter [her](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Næste trin

Besøg blitline.com kan læse mere om alle vores andre funktioner:

* Blitline API slutpunkt dokumenter <http://www.blitline.com/docs/api>
* Blitline API funktioner <http://www.blitline.com/docs/functions>
* Blitline API eksempler <http://www.blitline.com/docs/examples>
* Tredje del Nuget bibliotek <http://nuget.org/packages/Blitline.Net>
