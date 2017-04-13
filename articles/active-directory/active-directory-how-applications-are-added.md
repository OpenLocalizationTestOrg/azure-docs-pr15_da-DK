<properties
   pageTitle="Sådan føjes programmer til Azure Active Directory."
   description="I denne artikel beskrives, hvordan programmer føjes til en forekomst af Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hvordan og hvorfor programmer føjes til Azure AD

En af de oprindeligt uventede ting, når du får vist en liste over programmer i din forekomst af Azure Active Directory om, hvor programmerne, der stammer fra, og hvorfor de er til rådighed.  I denne artikel giver et højt niveau overblik over hvordan programmer er repræsenteret i mappen og giver dig kontekst, der kan hjælpe dig med at forstå, hvordan et program fulgte skal være i mappen.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Hvilke tjenester giver Azure AD til programmer?

Programmer føjes til Azure AD benytte en eller flere af de tjenester, den indeholder.  Disse tjenester, omfatter:

* App godkendelse og autorisation
* Brugergodkendelse og godkendelse
* Enkeltlogon (SSO) ved hjælp af sammenslutning eller adgangskode
* Brugerklargøring af og synkronisering
* Rollebaseret adgangskontrol Brug mappen til at definere programmet roller for at udføre roller baseret godkendelse kontrol i en app.
* oAuth godkendelse tjenester (bruges af Office 365 og andre Microsoft-apps til at give adgang til API'er/ressourcer.)
* Programmet publicering og proxy; Publicere en app fra et privat netværk til internettet

## <a name="how-are-applications-represented-in-the-directory"></a>Hvordan repræsenteres programmer i mappen?

Programmer er repræsenteret i den Azure AD ved hjælp af 2 objekter: et programobjekt og en tjeneste primært objekt.  Der er et objekt i programmet, er registreret i et "Hjem" / "ejer" eller "udgivelse" directory og en eller flere servicere vigtigste objekter, der repræsenterer det aktuelle program på alle mapper, hvor det fungerer.  

Objektet application beskrives appen til Azure AD (tjenesten med flere lejer) og kan du benytte en af følgende: (*Bemærk*: Dette er ikke en omfattende liste.)

* Navn, Logo og Publisher
* Hemmeligheder (symmetriske og/eller asymmetrisk nøgler, der bruges til at godkende appen)
* API afhængigheder (oAuth)
* API'er/ressourcer/områder publiceret (oAuth)
* App roller (RBAC)
* SSO-metadataene og konfiguration (SSO)
* Bruger klargøring af metadata og konfiguration
* Proxy-metadataene og konfiguration

Tjenesten hovedstolen er en post af programmet i alle mapper, hvor programmet fungerer, herunder en privat mappe.  Tjenesten hovedstolen:

* Henviser til et programobjekt via egenskaben app-id
* Poster lokal bruger og app-rolle gruppetildelinger
* Registrerer lokale brugere og administratorer tilladelser ydes til appen
    * For eksempel: tilladelse til at få adgang til en bestemt brugere mail-appen
* Poster lokale politikker, herunder betinget access-politik
* Poster lokale alternative lokale indstillinger for en app
    * Krav transformation regler
    * Attributten tilknytninger (brugerklargøring)
    * Lejer bestemt app roller (Hvis app understøtter brugerdefinerede roller)
    * Navn/Logo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Et diagram over programmet objekter og principper for tjenesten på tværs af mapper

![Et diagram, som illustrerer, hvordan programmet objekter og servicere principper for eksisterende med Azure AD forekomster.][apps_service_principals_directory]

Som du kan se fra diagrammet ovenfor.  Microsoft beskytter to kataloger internt (til venstre) bruges til at udgive programmer.

* En til Microsoft-Apps (Microsoft services bibliotek)
* En for allerede integrerede 3 part Apps (Appgalleri bibliotek)

Programmet udgivere/leverandører, der integreres med Azure AD skal have en publicering mappe.  (Nogle SAAS bibliotek).

Programmer, du tilføjer dig selv, omfatter:

* Apps, du har udviklet (integreret med AAD)
* Apps, du har forbindelse til single-sign-on
* Apps du udgivet ved hjælp af proxyen Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Et par noter og undtagelser

* Ikke alle principper for tjenesten peger tilbage på objekter i programmet.  Huh? Når Azure AD oprindeligt blev oprettet tjenesterne til programmer er meget mere begrænset og tjenesten hovedstolen er tilstrækkelige til at oprette en app-identitet.  Tjenesten oprindelige vigtigste blev nærmere i figur til Windows Server Active Directory-tjenestekontoen.  Derfor er det stadig muligt at oprette principper for tjenesten ved hjælp af Azure AD PowerShell uden først at oprette et programobjekt.  Graph API kræver et app-objekt før du opretter en tjeneste vigtigste.
* Ikke alle de oplysninger, der er beskrevet ovenfor er i øjeblikket vises fra et program.  Følgende er kun tilgængelige i Brugergrænsefladen:
    * Krav transformation regler
    * Attributten tilknytninger (brugerklargøring)
* Du kan finde flere detaljerede oplysninger om tjenesten hovedstolen og programmet objekter skal du i dokumentationen til Azure AD Graph REST-API reference.  *Tip*: feltet Azure AD-graf API dokumentation er den nærmeste ting at en skemareference til Azure AD, der er tilgængelig i øjeblikket.  
    * [Program](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Tjenesten hovedstolen](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Sådan føjes apps til min Azure AD-forekomst?
Der er mange måder kan tilføjes en app til Azure AD:

* Tilføj en app fra [Azure Active Directory App-galleriet](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Log op/til en 3 en App fra integreret med Azure Active Directory (For eksempel: [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Under tilmeldingen op/i brugere bliver bedt om at give tilladelse til appen til at få adgang til vedkommendes profil og andre tilladelser.  Den første person kan godkende bevirker, at en tjeneste-sikkerhedskonto app, der skal lægges til mappen.
* Log op/til Microsoft online services som [Office 365](http://products.office.com/)
    * Når du abonnerer på Office 365 eller starte en prøveversion, en eller flere principper for tjenesten er oprettet i den mappe, der repræsenterer de forskellige tjenester, der bruges til at levere alle de funktioner, der er knyttet til Office 365.
    * Nogle Office 365-tjenester som SharePoint oprette principper for tjenesten på grundlag af videre tillade sikker kommunikation mellem komponenter, herunder arbejdsprocesser.
* Tilføj en app, du udvikler i Azure Management Portal se: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Tilføje en app, du udvikler ved hjælp af Visual Studio i:
    * [ASP.Net godkendelsesmetoder](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Forbundne tjenester](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Tilføje en app til at bruge til at bruge [Azure AD-proxyen](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Forbinde en app til single sign-on ved hjælp af SAML eller adgangskode SSO
* Mange andre herunder forskellige udvikler oplevelser i Azure og/i API explorer oplever på tværs af developer Center

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Hvem har tilladelse til at føje programmer til min Azure AD-forekomst?

Kun globale administratorer kan:

* Tilføj apps fra galleriet Azure AD-app (allerede integrerede 3 part Apps)
* Publicere en app ved hjælp af Azure AD-proxyen

Alle brugere i mappen har rettigheder til at tilføje programmer, som de udvikler og skøn over hvilke programmer de del/Giv adgang til deres egne data.  *Husk på brugerens logonadresse op/til en app, og give tilladelser kan medføre en tjeneste sikkerhedskonto, der oprettes.*

Denne fungerer muligvis først lyd vedrørende, men Behold følgende opmærksom på:

* Apps har været i stand til at udnytte Windows Server Active Directory til brugergodkendelse i mange år uden at programmet skal være registreret/optaget i kataloget.  Nu vil organisationen har forbedret synlighed er angivet til nøjagtigt, hvor mange apps bruger mappen, og hvad for.
* Ingen grund til administrator indsatsbaserede app udgivelse/registreringsprocessen.  Det var sandsynligvis, er en administrator til at tilføje en app som en afhængige part på vegne af udviklere med Active Directory Federation Services.  Udviklere kan nu selvbetjening.
* Brugere logger på/op til apps ved hjælp af deres organisation konti til erhvervsbrug er godt.  Hvis de efterfølgende forlader organisationen vil de miste adgang til deres konto i det program, de blev brugt.
* Har du en oversigt over, hvilke data deles, som programmet er en god ting.  Data er mere transportable end nogensinde før og har en Ryd oversigt over der delt hvilke data med hvilke programmer er nyttig.
* Apps, der anvender Azure AD til oAuth Bestem præcis hvilke tilladelser, som brugerne kan give til programmer og der tilladelser kræver en administrator for at acceptere.  Det skal gå uden siger, at kun administratorer kan samtykke større områder og vigtigere tilladelser.
* Brugere, tilføje og tillade apps for at åbne deres data er overvåget begivenheder, så du kan se overvågningslog rapporter i portalen Azure Management for at finde ud af, hvordan en app blev føjet til biblioteket.

**Note:** *Microsoft selve har fungeret ved hjælp af standardkonfiguration nu mange måneder.*

Sagde med alle, som det er muligt at forhindre brugere i mappen i at tilføje programmer og fra konsoliderede skøn over hvilke oplysninger giver de dele med programmer til et ved at ændre Directory konfigurationen i portalen Azure Management.  Følgende konfiguration kan få adgang til portalen Azure Management dit bibliotek under fanen "Konfigurer".

![Et skærmbillede af Brugergrænsefladen til konfiguration af integreret app-indstillinger][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Lær mere om, hvordan du føjer programmer til Azure AD og hvordan du konfigurerer tjenester til apps.

* Udviklere: [Lær, hvordan du integrerer et program med AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Udviklere: [Gennemse eksempelkode til apps, der er integreret med Azure Active Directory på Github](https://github.com/AzureADSamples)
* Udviklere og IT-medarbejdere: [Gennemse REST-API dokumentation til Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT-fagfolk: [Lær at bruge foruddefinerede integrerede Azure Active Directory-programmer fra galleriet App](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT-medarbejdere: [finde selvstudier til konfiguration af bestemte allerede integrerede apps](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT-fagfolk: [Lær, hvordan du publicerer en app ved hjælp af proxyen Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Se også

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
