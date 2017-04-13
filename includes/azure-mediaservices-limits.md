Ressource|Standardgrænse|Højeste antal
---|---|---
Azure Media Services (AMS)-konti i et enkelt abonnement||25
Aktiver per AMS konto||1.000.000<sup>1</sup>
Sammenkædede opgaver per job||30
Aktiver hver opgave||50
Aktiver per job||100
Job per AMS konto ||50.000<sup>2</sup>
Entydige locators, der er knyttet til et aktiv ad gangen||5<sup>4</sup>
Direkte kanaler per AMS konto </p></td>|5</p></td>|I/t<sup>1</sup>
Programmer i stoppet tilstand per kanal </p></td>|50</p></td>|I/t<sup>1</sup>
Programmer i kører tilstand per kanal </p></td>|3</p></td>|3
Streaming slutpunkter i kører tilstand per AMS konto</p></td>|2</p></td>|I/t<sup>1</sup>
Streaming enheder per streaming slutpunkt </p></td>|10 </p></td>|I/t<sup>1</sup>
Kodning enheder per AMS konto </p></td>|25</p></td>|I/t<sup>1</sup>
Lagerplads konti | |1.000<sup>5</sup>
Politikker || 1.000.000<sup>6</sup>

<sup>1</sup> kan du anmode om at opdatere begrænsninger for denne kvote ved at åbne en supportbilletter. Opret ikke flere AMS konti øge begrænsninger, skal du i stedet indsende en supportbilletter.

<sup>2</sup> dette nummer indeholder i kø, færdig, aktive og annullerede job. Det omfatter ikke slettede job. Du kan slette de gamle job ved hjælp af **IJob.Delete** eller **slette** HTTP-anmodningen.

<sup>3</sup> når du laver en anmodning til listen Job enheder, maksimalt 1.000 returneres per anmodning. Hvis du har brug at holde styr på alle sendte job, kan du bruge top/Spring, som beskrevet i [OData-system forespørgselsindstillinger](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> locators er ikke beregnet til at administrere adgangskontrol for hver bruger. For at give forskellige adgangsrettigheder til individuelle brugere, skal du bruge løsninger, Digital Rights Management (DRM).

<sup>5</sup> kontiene lagerplads skal være fra samme Azure-abonnement.

<sup>6</sup> er der en grænse på 1.000.000 politikker for forskellige AMS politikker (for eksempel for Locator politik eller ContentKeyAuthorizationPolicy). Du skal bruge det samme politik-ID, hvis du altid bruger de samme dage / adgangstilladelser / osv.
