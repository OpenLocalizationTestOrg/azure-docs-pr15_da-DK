Data factory er en med flere lejer-tjeneste, der har følgende begrænsninger for standard for at sikre, at kunden abonnementer er beskyttet mod hinandens arbejdsmængder. Mange af begrænsningerne kan nemt hæves for dit abonnement op til maksimumgrænsen ved at kontakte support. 

**Ressource** | **Standardgrænse** | **Højeste antal**
-------- | ------------- | -------------
data fabrikker i et Azure-abonnement | 50 | [Kontakt support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
rørledninger inden for en data fabrik | 2500 | [Kontakt support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
datasæt i en data fabrik | 5000 | [Kontakt support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
samtidige udsnit per datasæt | 10 | 10
byte per objekt for pipeline objekter <sup>1</sup> | 200 KB | 2000 KB
byte per objekt for dataset og sammenkædede service objekter <sup>1</sup> | 100 KB | 2000 KB
HDInsight efter behov klynge kerner inden for et abonnement <sup>2</sup> | 48 | [Kontakt support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Skyen data bevægelse enhed <sup>3</sup> | 8 | [Kontakt support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Antal for pipeline aktivitet kører forsøg | 1000 | MaxInt (32-bit)

<sup>1</sup> pipeline, dataset og sammenkædede serviceobjekter repræsenterer en logisk gruppering af din arbejdsbyrde. Begrænsninger for disse objekter vedrører ikke mængde data, du kan flytte og behandle med Azure Data Factory-tjenesten. Data factory er udviklet til at skalere til at håndtere petabytes af data.

<sup>2</sup> efter behov HDInsight kerner er allokeret af det abonnement, der indeholder data factory. Som et resultat, er grænsen for ovenstående Data Factory tvungen core grænsen for efter behov HDInsight kerner og adskiller sig fra den core grænse, der er knyttet til abonnementet Azure.

<sup>3</sup> skyen data bevægelse enhed (DMU), der anvendes i skyen i skyen kopiering. Det er en måling, der repræsenterer en potens (en kombination af CPU, hukommelse og netværk ressourceallokering) på en enkelt enhed i Data Factory. Du kan opnå højere kopi overførselshastighed ved at udnytte flere DMUs for visse scenarier. Referere til [skyen data bevægelse enheder](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) sektion på detaljer.

**Ressource** | **Standard nedre grænse** | **Mindste grænse**
-------- | ------------------- | -------------
Planlægning af interval | 15 minutter | 15 minutter
Intervallet mellem forsøg forsøg | 1 sekund | 1 sekund
Prøv igen timeoutværdien | 1 sekund | 1 sekund


### <a name="web-service-call-limits"></a>Begrænsninger for Web service-opkald

Azure ressourcestyring har begrænsninger for API-kald. Du kan foretage API opkald på en sats i [Azure ressourcestyring API begrænsninger](../azure-subscription-service-limits.md#resource-group-limits). 


