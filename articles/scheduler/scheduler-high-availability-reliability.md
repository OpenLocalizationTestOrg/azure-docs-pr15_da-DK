<properties
 pageTitle="Scheduler høj tilgængelighed og pålidelighed"
 description="Scheduler høj tilgængelighed og pålidelighed"
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
 ms.topic="article"
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Scheduler høj tilgængelighed og pålidelighed

## <a name="azure-scheduler-high-availability"></a>Azure Scheduler høj tilgængelighed

Som core Azure-platformen service Azure Scheduler er meget tilgængelig og indeholder både geografisk overflødige service installation og geografisk internationale job gentagelse.

### <a name="geo-redundant-service-deployment"></a>Implementeringen af geografisk overflødige service

Azure Scheduler er tilgængelige via Brugergrænsefladen i næsten alle geografisk område, der er i Azure i dag. Listen over områder, der er tilgængelig i Azure Scheduler er [angivet her](https://azure.microsoft.com/regions/#services). Hvis et datacenter i et område, der er hostet gengives ikke tilgængelig, er failoveregenskaber Azure Scheduler så tjenesten er tilgængelig fra en anden datacenter.

### <a name="geo-regional-job-replication"></a>Geografisk internationale job gentagelse

Ikke alene er Azure planlæggeren front end tilgængelig for administration af anmodninger, men din egen job er også geografisk replikerede. Når der er en afbrydelse i ét område, Azure Scheduler skifter og sikrer, at der køres jobbet fra en anden datacenter i parvis geografiske område.

Eksempelvis hvis du har oprettet en sag i Syd Central os, kopieres Azure Scheduler automatisk jobbet i Nord Central os. Når der er en fejl i Syd Central os, sikrer Azure Scheduler, køres jobbet fra nord Central os. 

![][1]

Som et resultat sikrer Azure Scheduler, at dine data forbliver i den samme bredere geografiske område i tilfælde af en Azure mislykket. Som et resultat, du skal ikke duplikere tingene kun, hvis du vil tilføje høj tilgængelighed – Azure Scheduler indeholder automatisk høj tilgængelighed funktioner for dine sager.

## <a name="azure-scheduler-reliability"></a>Azure Scheduler pålidelighed

Azure Scheduler garanterer sin egen høj tilgængelighed og tager en anden metode til brugeroprettede sager. Tingene kan for eksempel kalde en HTTP-slutpunkt, der ikke er tilgængelig. Azure Scheduler noget forsøger at udføre dit arbejde korrekt, ved at gøre det alternative indstillinger til at håndtere fejl. Azure Scheduler gør dette på to måder:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Konfigurerbar prøv igen politik via "retryPolicy"

Azure Scheduler kan du konfigurere en politik for prøv igen. Som standard, hvis et job mislykkes, prøver Opgavestyring jobbet igen fire flere gange, med 30 sekunder intervaller. Du kan konfigurere denne politik skal skrives forsøg igen for at være mere tilstanden (for eksempel ti gange med 30 sekunder intervaller) igen eller løsere (for eksempel to gange i daglige intervaller.)

Som et eksempel på når dette kan hjælpe, kan du oprette et job, der kører en gang om ugen og aktiverer HTTP ark. Hvis HTTP slutpunktet ned i et par timer, når tingene kører, kan du ikke vil vente en flere uge for jobbet skal køre igen, da endnu flere forsøg standardpolitikken mislykkes. I så fald kan du omkonfigurere politikken standard forsøg at prøve igen hver tredje time (for eksempel) i stedet for hvert 30.

Få at vide, hvordan du kan konfigurere en politik for forsøg igen, skal du se [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Alternative slutpunkt produktionsfunktioner via "errorAction"

Hvis target slutpunkt til Azure Scheduler tingene forbliver er utilgængelig, går Azure Scheduler tilbage til den alternative fejlhåndtering slutpunkt når du har fulgt sin politik på Prøv igen. Hvis alternative fejlhåndtering ark er konfigureret, aktiverer Azure Scheduler den. Din egen job er meget tilgængelige trods fejl med et andet slutpunkt.

Som et eksempel i diagrammet nedenfor, følger Azure Scheduler sin forsøg politik for at ramme en New York webtjeneste. Når gentagelserne mislykkes, kontrollerer det, om der er et alternativ. Derefter går videre og begynder at foretage anmodninger om alternative med den samme retry-politik.

![][2]

Bemærk, at den samme retry-politik gælder for både den oprindelige handling og handlingen alternative fejl. Det er også muligt at have handlingen alternative fejl handlingstype være forskellige fra den primære handling handlingstype. For eksempel mens den primære handling kan aktivering HTTP ark, handlingen fejl i stedet muligvis en lagerplads kø, service bus kø eller service bus emne handling, der indeholder logføring af fejl ved.

Få at vide, hvordan du konfigurerer et alternativt slutpunkt, skal du se [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Se også

 [Hvad er Scheduler?](scheduler-intro.md)

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Sådan oprettes komplekse tidsplaner og avancerede gentagelse med Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
