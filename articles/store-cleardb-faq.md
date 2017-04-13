<properties
    pageTitle="Ofte stillede spørgsmål om ClearDB MySql databaser med Azure App Service | Microsoft Azure"
    description="Svar på ofte stillede spørgsmål om brug af ClearDB MySQL databaser med Azure App Service."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Ofte stillede spørgsmål om ClearDB MySql databaser med Azure App Service

Disse ofte stillede spørgsmål besvares nogle almindelige spørgsmål om at bruge og købe ClearDB MySQL databaser til Azure Web Apps.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Hvilke muligheder har jeg for MySQL på Azure?

Har du forskellige valgmuligheder:

* [ClearDB delt MySQL-database](/marketplace/partners/cleardb/databases/)

* [ClearDB MySQL Premium klynger](/marketplace/partners/cleardb-clusters/cluster/)

* [MySQL klynge, der kører på en Azure VM](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Enkelt forekomst af MySQL, der kører på en Azure VM](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB er en værtstjeneste MySQL og administrerer MySQL-infrastruktur for dig. Når du kører din egen MySQL klynge eller database på en Azure Virtual Machine, skal du konfigurere MySQL-serveren og holde den opdateret med rettelser.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Skal jeg bruge et kreditkort for online + MySQL skabelon i Azure Marketplace?

Dette afhænger af typen abonnement, du bruger. Her er nogle ofte anvendte abonnementstyper:

* [Betal efterhånden](/offers/ms-azr-0003p/): kræver et kreditkort, og når du køber et betalt MySQL-database dit kreditkort er faktureret.

* [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/): omfatter kredit til brug sammen med Microsoft Azure services, men ikke tillader, at køb af tredjeparters ressourcer. Aktiveret abonnement for at købe tredjepartstjenester eller et betalt MySQL-database, du skal bruge et kreditkort. Web Apps, kan du oprette en gratis ClearDB MySQL-database.

* [MSDN-abonnement](/pricing/member-offers/msdn-benefits/) og **MSDN Udviklingscenter Test betale løbende gå**: svarer til gratis prøveversion, et MSDN-abonnement kræver, at du har et kreditkort til at købe en betalt MySQL-løsning fra ClearDB.

* [Enterprise-aftale (EA)](/pricing/enterprise-agreement/): EA kunder faktureres mod deres EA hvert kvartal for alle deres Azure Marketplace (fra tredjepart) køb på en separat, konsoliderede faktura. Du er faktureret uden for valuta anvendelsen for en hvilken som helst marketplace køb. Vær opmærksom på, på nuværende tidspunkt Azure Store ikke er tilgængelig for kunder, der er registreret i Aserbajdsjan, Kroatien, Norge og Puerto Rico. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): Du kan oprette kun gratis ClearDB databaser for Web Apps. Der er ingen begrænsning på antallet af gratis ClearDB MySQL-databaser, kan du oprette. Bemærk, at der er gratis databaser ikke skal bruges til fremstilling webapps, som denne tjeneste er kun beregnet til prøveversion.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Hvorfor blev jeg betale 3.50 til en WebApp + MySQL fra Azure Marketplace?

Indstillingen standard database er Tital, som er $3.50. Vi vises ikke omkostninger under oprettelse af databasen, og du kan købe en database, du ikke har til hensigt at kommet. Vi prøver at finde en metode til at forbedre oplevelsen, men indtil da du skal tjekke alle dine markerede priser niveauer for WebApp og database før klikke på **Opret** og starte installationen af ressourcer.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Jeg kører MySQL på mit eget Azure virtuel maskine. Kan jeg oprette forbindelse min Azure WebApp til min database?

Ja. Du kan oprette forbindelse din online til databasen, som din Azure VM har givet fjernadgang til din online. Se [Installere MySQL på en virtuel maskine](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)kan finde flere oplysninger.

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Hvor er lande ClearDB Premium MySQL klynger understøttes?

[ClearDB Premium MySQL klynger](/marketplace/partners/cleardb-clusters/cluster/) er tilgængelige i alle Azure områder i hele verden med undtagelse af Indien, Australien, Brasilien syd og Kina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan jeg oprette en ny klynge før du opretter en database med ClearDB premium klyngeløsning?

Nej, oprette tomme ClearDB klynger understøttes ikke. Portalen Azure kan du oprette databaser i en klynge, som kan oprette en ny klynge på samme tid.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Kan jeg en advarsel, hvis jeg forsøger at slette en ClearDB MySQL-database, der er i brug af en af mine-programmer?

Nej, Azure ikke advarer dig, hvis du sletter et marketplace køb, afhænger af dit program.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Hvilke områder kan jeg oprette ClearDB databaser i?

Azure Marketplace er ikke tilgængelig for kunder, der er tilmeldt Aserbajdsjan, Kroatien, Norge eller Puerto Rico. ClearDB er ikke tilgængelig i disse områder.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Hvilke priser niveau skal jeg vælge for en fremstilling WebApp og database?

Bruge grundlæggende eller en nyere priser niveau til Web Apps. For ClearDB anbefaler vi en Saturn eller Jupiter plan. Gennemse de funktioner og begrænsninger af hver priser niveauet for både [Web Apps](/pricing/details/app-service/) og [ClearDB MySQL-databaser](/marketplace/partners/cleardb/databases/) at vælge det diagram, der passer til dine behov.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hvordan opgraderer jeg mit ClearDB database fra en plan til en anden?

[Azure-portalen](https://portal.azure.com), kan du skalere en delt hosting ClearDB-database. Læs denne [artikel](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) for at få mere at vide. Vi understøtter ikke opgradering til ClearDB Premium klynger i portalen Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Jeg kan ikke se mine ClearDB database i Azure portal?

Hvis vi opretter ClearDB database ved hjælp af Azure ressourcestyring eller [Ny Azure-Portal](https://portal.azure.com), kan den ikke ses i den [Gamle Azure-portalen](https://manage.windowsazure.com). Hvis du vil arbejde-løse dette problem er ved at sammenkæde databasen manuelt til WebApp. På samme måde hvis oprette ClearDB database i den [gamle portal](https://manage.windowsazure.com) du kan ikke kunne se databasen i [Nye Azure-Portal](https://portal.azure.com). Der er ingen arbejde-omkring til det sidste scenario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Hvem jeg kontakter efter support når min database er nede?

Kontakt [ClearDB understøtter](https://www.cleardb.com/developers/help/support) til en database, der drejer problemer. Vær klar til at give dem med dine Azure abonnementsoplysninger.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan jeg oprette flere brugere til min ClearDB MySQL-database klyngeløsning? 

Nej. Du kan ikke oprette flere brugere, men du kan oprette flere databaser på din ClearDB database klynge.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Kan Basic/Pro serie databaser blive opgraderet lokalt ligner omkring planer i dag på ClearDB portal?

Ja, opgraderes grundlæggende serie databaser kan være lokal (grundlæggende 60 gennem grundlæggende 500). Pro serie kan være opgraderet lokalt (Pro 125 gennem Pro 1000) med undtagelse af Pro 60. Vi understøtter ikke opgraderer Pro 60 database i øjeblikket. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Når jeg overfører mine ressourcer fra et abonnement til en anden, min ClearDB MySQL-database overflyttes samt? 

Når du udfører ressource overførsel på tværs af abonnementer, anvende visse [begrænsninger](./app-service-web/app-service-move-resources.md) . En ClearDB MySQL-database er en tredjepartstjenester og derfor overflyttes ikke under Azure abonnement overflytningen. Hvis du ikke administrerer overførslen af din MySQL-database, før du overfører Azure ressourcer, kan ClearDB MySQL databaser deaktiveres. Manuelt overføre dine databaser først og derefter udføre Azure abonnement overførsel til din online. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan jeg overføre en ClearDB database fra et kreditkort abonnement til et EA-abonnement?

Eksisterende ClearDB databaser Brug det kreditkort, der er knyttet til de eksisterende abonnementer. Sådan bruger du et EA-abonnement, du vil flytte dine data til en ny database:

* Købe en ny ClearDB database med abonnementet EA.
* Overføre dine data til den nye database.
* Opdater dit program du bruger den nye database.
* Slet den gamle ClearDB database.

Når du opretter en ny WebApp med MySQL (ClearDB) eller oprette en MySQL-database (ClearDB), bestemmer det abonnement, du vælger, hvordan du vil betale for tjenesten. Med et EA-abonnement, vil vi ikke blokere indkøb af tredjeparters tjenester som ClearDB i portalen Azure. EA abonnementer er faktureret uden for valuta anvendelsen og er faktureret kvartalsvise og i skatterestancer. EA kunden ville have til at konfigurere en betalingsmetode som et kreditkort til at betale for en hvilken som helst fra tredjepart marketplace-tjenester.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Hvor kan jeg se gebyrerne for ClearDB ressourcer i et EA-abonnement?

Azure Marketplace gebyrer er synlige i Enterprise Portal for direkte EA kunder. Bemærk, at alle marketplace køb og forbrug er faktureret uden for valuta anvendelsen og er faktureret, kvartalsvis og i skatterestancer. EA kunder betale direkte til tredjepart tjenesteudbydere og kan gøre det ved at aktivere en betalingsmetode som et kreditkort med deres EA-konto.

Indirekte EA kunder kan finde deres Azure Marketplace-abonnementer på siden **Administrer abonnementer** af Enterprise Portal, men priser er skjult. Kunder skal kontakte deres LSP oplysninger om marketplace gebyrer.

Adgang til Azure Marketplace for tredjepartstjenester kan administreres af din EA Azure tilmelding administratorer. De kan deaktivere eller genaktivere adgang til 3 part køb fra Store i abonnementer og Administrer konti under afsnittet konti i Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Hvem kontakter jeg til spørgsmål om din faktura for ClearDB tjenester i mit EA abonnement?

Du kan kontakte [Enterprise kundesupport](http://aka.ms/AzureEntSupport) forbindelse med fakturering under deres EA tilmelding. EA Portal supportteam vil besvare dit spørgsmål eller at løse problemet.

 



## <a name="more-information"></a>Få mere at vide

[Ofte stillede spørgsmål om Azure Marketplace](/marketplace/faq/)
