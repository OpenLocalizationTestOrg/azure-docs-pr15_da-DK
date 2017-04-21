<properties title="" pageTitle="Filnavne og placeringer til Azure tekniske artikler" description="Forklarer filstruktur til artikler og de navngivningskonventioner, skal du følge, når du opretter en ny artikel." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Filnavne og placeringer til Azure tekniske artikler

I vores technical indhold lager bruge vi en enkelt mappe (mappen **artikler** ) for alle artikler. Der er ingen mappehierarkiet – alle artikler live i flad filstruktur. Hvis du opretter mapper med artikler i dem, kan ikke udgives artikler.

I stedet for brug af en filstruktur som en organisere princippet, bruger vi en faste filnavngivning, der tydeligt identificerer emner og, der bidrager til synlighed på internettet.

Her er, hvad du bør vide:

+ [Regler]
+ [Mønster]
+ [Standard eksempler]
+ [Marketplace indhold]
+ [Filen navnet godkendelse]

##<a name="rules"></a>Regler

- Filnavnene kan indeholde kun små bogstaver, tal og bindestreger. 
- Ingen mellemrum eller tegnsætningstegn. Brug bindestreger til at adskille ord og tal i filnavnet.
- Ikke mere end 80 tegn - dette er en publicering systemgrænsen
- Brug handling verber, der er specifikke, f.eks, udvikle, købe, opbygge, fejlfinding i forbindelse med. Ingen - Træk ord.
- Ingen small ord - ikke omfatter en og, den, i eller, etc.
- Alle filer, der skal være i tabsbeløb og bruge filtypenavnet .md.
- Stavningen af ord undgå ikke-godkendte eller unødvendige akronymer i filnavne

Akronymer og initialisms i filnavne - specifikke retningslinjer:

- Ikke forkorte Azure tjenestenavne – de første ord i filnavnet skal være standard, fremgå Azure service eller teknologi navn. 
-   Tillad ikke ressourcestyring eller arm som akronymer et vilkårligt sted i et filnavn
- Andre branchestandard forkortelser accepteres som det er nødvendigt i filnavne. 

##<a name="pattern"></a>Mønster

Her er de generelle mønster:

 **Service-platform-Language-Content-Product-version.MD**

Brug af webdelene mønsterets, der gælder, og gennemgå listen over artikler i lager til at få en ide om eksisterende navne. Navne, som ikke starter med en Udviklingscenter platform eller et navnet på tjenesten er sandsynligvis mistænkelige og forsinkede gennem.

##<a name="standard-examples"></a>Standard eksempler

Her er nogle få eksempler på gyldige navne, der følger efter mønstret. :

- cloud-services-dotnet-fortløbende-delivery.md
- Mobile-tjenester – ios-get-started.md
- documentdb administrere account.md
- Mobile-Services-DotNet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-AUTHENTICATE-Users-Access-Control-ECLIPSE.MD
- Virtual-Machines-Install-Windows-Server-2008r2.MD
- cache-aspnet-session-tilstand-udbyder
- Azure-SDK-DotNet-Release-Notes-2-8
- storsimple-Disaster-Recovery-Using-Azure-site-Recovery

##<a name="marketplace-content"></a>Marketplace indhold

Start filnavne med "marketplace" for at skelne indhold, der fokuserer på partner bidrag til Azure marketplace. Dette indhold må ikke være for almindelige, som de fleste partner indhold skal der oprettes på partneres egne websteder.

- Marketplace-mongodb-Virtual-Machines-Install-Windows-Server-2008r2.MD

##<a name="file-name-approval"></a>Filen navnet godkendelse

Det er kørslen af vores gruppe hente anmodning korrekturlæsere Gennemse filnavne, når en ny fil sendes til lagringsstedet for første gang. Hente anmodning korrekturlæsere skal gennemse filnavnet og give feedback via hente anmodning kommentar strømmen, hvis ændringer du skal bruge. Filnavnet skal rettes, før hente anmodningen accepteres. Bidragydere kan nemt push opdateringen til ventende hente anmodningen.

##<a name="folder-names-in-the-repo"></a>Mappenavne i repo

Mapper skal oprettes kun for tjenester og filnavnet skal svare til tjenesten sats. Brug kun bogstaver og bindestreger, og brug små bogstaver. Indhente godkendelse fra lager administratoren, inden du opretter en ny mappe, der ikke er til en frigivne tjeneste.

##<a name="changing-case-in-file-names"></a>Ændre store og små bogstaver i filnavne

Windows-operativsystemer er bogstaver, så hvis du vil ændre et filnavn til at løse hus, er det bedre at foretage en ændring i væsentlige, medmindre du kan foretage ændring på en Linux eller Mac. Eksempel:

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Brug følgende kommando til at omdøbe en fil:
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Bidragydere vejledning Links

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)


<!--Anchors-->
[Regler]: #rules
[Mønster]: #pattern
[Standard eksempler]: #standard-examples
[Marketplace indhold]: #marketplace-content
[Filen navnet godkendelse]: #file-name-approval
