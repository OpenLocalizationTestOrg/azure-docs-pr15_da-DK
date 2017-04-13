<properties
    pageTitle="Azure AD-forbindelse synkronisering: forstå deklarativ klargøring | Microsoft Azure"
    description="Forklarer deklarativ klargøring konfigurationsmodellen i Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD-forbindelse synkronisering: forstå deklarativ klargøring
Dette emne forklares konfigurationsmodellen i Azure AD-forbindelse. Modellen kaldes deklarativ klargøring og det gør muligt at konfiguration af uden besvær. Mange ting, der er beskrevet i dette emne er Avanceret og kræves ikke til de fleste kundescenarier.

## <a name="overview"></a>Oversigt
Klargøring af deklarativ behandling af objekter, der kommer fra en forbundet kildemappe og bestemmer, hvordan objekter og attributter skal være transformeret fra en kilde til en destination. Et objekt er behandlet i en synkronisering rørledning og en pipeline er det samme for indgående og udgående regler. En indgående regel er fra en connector-plads til metaverse og en udgående regel er fra metaverse til et forbindelse mellemrum.

![Synkroniser pipeline](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Pipeline har flere forskellige moduler. Hver enkelt er ansvarlig for et emne i objekt synkronisering.

![Synkroniser pipeline](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Kilde kildeobjektet
- [Omfang](#scope), finder alle regler for synkronisering, der findes i omfang
- [Deltage i](#join), bestemmer forholdet mellem connector-plads og metaverse
- [Transformere](#transform), beregner hvordan attributter skal være transformeret og strømmer
- [Rangorden for](#precedence)løser problemer med uoverensstemmende attribut bidrag
- Målet, målobjektet

## <a name="scope"></a>Omfang
Modulet omfang evaluering af et objekt og bestemmer de regler, der er i omfang og skal medtages i behandlingen. Afhængigt af værdierne, attributter på objektet evalueres forskellige Synkroniser regler for at være i omfang. For eksempel har en deaktiveret bruger med ingen Exchange-postkassen forskellige regler end en aktiveret bruger med en postkasse.  
![Omfang](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

Omfanget er defineret som grupper og delsætninger. Disse betingelser er i en gruppe. En logisk og bruges mellem alle betingelser i en gruppe. For eksempel (afdeling = IT og land = Danmark). Et logisk eller bruges mellem grupper.

![Omfang](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
Området på dette billede skal læses som (afdeling = IT og land = Danmark) eller (land = Sverige). Hvis grupperne 1 eller 2 evalueres til sand, og klik derefter på reglen er i omfang.

Modulet omfang understøtter følgende handlinger.

Handling | Beskrivelse
--- | ---
LIGE, NOTEQUAL | En streng Sammenlign, der evalueres, hvis værdien er lig med værdien i attributten. Med flere værdier med attributter skal du se ISIN og ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | En streng Sammenlign, der evalueres, hvis værdien er mindre end værdien i attributten.
INDEHOLDER, NOTCONTAINS | En streng Sammenlign, der evalueres, hvis værdi du kan finde et sted i værdien i attributten.
STARTSWITH, NOTSTARTSWITH | En streng Sammenlign, der evalueres, hvis værdien er i starten af værdien i attributten.
ENDSWITH, NOTENDSWITH | En streng Sammenlign, der evalueres, hvis værdien er i slutningen af værdien i attributten.
GREATERTHAN, GREATERTHAN_OR_EQUAL | En streng Sammenlign, der evalueres, hvis værdien er større end værdien i attributten.
ISNULL, ISNOTNULL | Evaluerer Hvis attributten er fraværende fra objektet. Hvis attributten ikke findes Præsenter og derfor null, er reglen i omfang.
ISIN, ISNOTIN | Evaluerer, hvis værdien er angivet i attributten defineret. Denne handling er den med flere valgmuligheder variation af lig med HINANDEN og NOTEQUAL. Attributten skal være en opslagskolonne med flere valgmuligheder attribut, og hvis værdien kan findes i nogen af attributværdierne, er reglen i omfang.
ISBITSET, ISNOTBITSET | Evaluerer, hvis en bestemt bit er indstillet. For eksempel kan bruges til at evaluere bit i Brugerkontokontrol til at se, om en bruger er aktiveret eller deaktiveret.
ISMEMBEROF, ISNOTMEMBEROF | Værdien skal indeholde en DN til en gruppe i connector-plads. Hvis objektet, der er medlem af gruppen angivet, er reglen i omfang.

## <a name="join"></a>Deltage i
Modulet Deltag i Synkroniser pipeline er ansvarlig for at finde forholdet mellem objektet i kilden og et objekt i destinationen. Denne relation ville være et objekt i en connector-plads, søge efter en relation til et objekt i metaverse på en indgående regel.  
![Deltage i mellem cs og mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
Målet er at se, hvis der er et objekt allerede findes i metaverse, der er oprettet af en anden forbindelse, skal være knyttet til. For eksempel i et område, konto er ressourcerelaterede skal brugeren fra konto skov sammenkædes med brugeren fra den ressourceskov.

Joinforbindelser bruges hovedsageligt på indgående regler til at sammenføje forbindelse mellemrum objekter på det samme metaverse objekt.

Joinforbindelserne er defineret som en eller flere grupper. I en gruppe har du delsætninger. En logisk og bruges mellem alle betingelser i en gruppe. Et logisk eller bruges mellem grupper. Grupperne behandles i rækkefølge fra top til bund. Når en gruppe har fundet ét Sammenlign med et objekt i destinationen, evalueres ingen andre joinforbindelse regler. Hvis nul eller mere end ét objekt findes, fortsætter behandling til den næste gruppe af regler. Derfor skal reglerne være oprettet i den rækkefølge, mest eksplicitte første og mere uskarpt i slutningen.  
![Deltage i definition](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Joinforbindelser på dette billede er behandlet fra top til bund. Synkroniser pipeline ser først, hvis der er en match på medarbejder-id. Hvis ikke, den anden regel ser Hvis kontonavnet kan bruges til at sammenføje objekterne. Hvis, der ikke er en match enten, er tredje og sidste reglen en mere uskarpt match ved hjælp af navnet på brugeren.

Hvis alle regler for joinforbindelse har evalueret, og der er ikke helt resultater, bruges den **Kædetype** på siden **Beskrivelse** . Hvis denne indstilling er indstillet til **klargøring**, oprettes et nyt objekt i mållejerens.  
![Klargøring eller join](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Et objekt skal kun har én enkelt Synkroniser regel med joinforbindelse regler i omfang. Hvis der er flere Synkroniser regler, hvor joinforbindelse er defineret, er der en fejlmeddelelse. Rangorden for bruges ikke til at løse konflikter joinforbindelse. Et objekt skal have en join-regel i området for attributter, der ombrydes med samme indgående/udgående retning. Hvis du vil lade attributter, både indgående og udgående på det samme objekt, skal du have en indgående og en regel for udgående synkronisering med join.

Udgående joinforbindelser har særlige funktioner, når den forsøger at klargøre et objekt til et destinationsprogram forbindelse mellemrum. Attributten DN bruges til at første gang prøver en omvendt-joinforbindelse. Hvis der er allerede et objekt i target connector-plads med det samme DN, er objekterne, der sammenføjet.

Deltag i modulet kun evalueres, når hvornår en ny regel for synkronisering træder i omfang. Når et objekt har optaget, det ikke disjoining selvom kriteriet Deltag ikke længere er opfyldt. Hvis du vil disjoin et objekt, skal den Synkroniser regel, der er joinforbundne objekterne gå af omfang.

### <a name="metaverse-delete"></a>Metaverse Slet
Et metaverse objekt forbliver, som lang tid, der er angivet en regel for synkronisering i omfang med **Kædetype** til **klargøring** eller **StickyJoin**. En StickyJoin bruges, når en forbindelse ikke har tilladelse til at klargøre et nyt objekt til metaverse, men når den er tilsluttet, den skal slettes i kilden, før objektet metaverse bliver slettet.

Når et metaverse objekt slettes, markeres alle objekter, der er knyttet til en udgående Synkroniser regel, der er markeret med henblik på **klargøring** til en Slet.

## <a name="transformations"></a>Transformationer
Transformeringer bruges til at definere, hvordan attributter skal flyde fra kilden til destinationen. Flyder kan have en af følgende **flowtyper af**: direkte, konstant eller udtryk. En direkte flow flyder en attributværdien som-er uden yderligere transformeringer. En konstant værdi angiver den angivne værdi. Et udtryk, der bruger deklarativ klargøring udtrykssproget til at udtrykke hvordan transformation skal være. Detaljerne for udtrykssproget, kan findes i emnet [forstå deklarativ klargøring udtrykssproget](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Klargøring eller join](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

Afkrydsningsfeltet **Anvend én gang** definerer, attributten skal kun angives, når objektet oprettes. Denne konfiguration kan for eksempel bruges til at angive en indledende adgangskode til en ny brugerobjektet.

### <a name="merging-attribute-values"></a>Fletning af attributværdier
I attributten flyder er der en indstilling til at bestemme, hvis der skal flettes med flere valgmuligheder attributter fra flere forskellige forbindelser. Standardværdien er **opdatering**, som angiver, at reglen synkronisering med højeste prioritet skal vinder.

![Flette typer](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Der er også **flette** og **MergeCaseInsensitive**. Disse indstillinger giver dig mulighed at flette værdier fra forskellige kilder. Det kan for eksempel bruges til at flette medlem eller proxyAddresses-attributten fra flere forskellige områder. Når du bruger denne indstilling, kan alle regler for synkronisering i området for et objekt skal bruge den samme type som fletningen. Du kan ikke definere **Update** fra én forbindelse og **flette** fra en anden. Hvis du prøver, modtager du en fejl.

Forskellen mellem **Flet** og **MergeCaseInsensitive** er Sådan behandles dublerede værdier. Synkroniser program sikrer, at dublerede værdier ikke er indsat i målattributten. Dublerede værdier med kun en forskel med **MergeCaseInsensitive**, i tilfælde af, at der ikke skal være til stede. For eksempel skal du ikke se afsnittet begge "SMTP:bob@contoso.com" og "smtp:bob@contoso.com" i målattributten. **Flette** kun se på de nøjagtige værdier og flere værdier, hvor der kun er forskel på store og små bogstaver kan være til stede.

Indstillingen **Erstat** er den samme som **opdatering**, men det er ikke brugt.

### <a name="control-the-attribute-flow-process"></a>Styre processen attribut flow
Når flere synkronisering af indgående regler er konfigureret til at bidrage til den samme metaverse-attribut, bruges rangorden for til at bestemme vinderen. Synkroniser reglen med højeste prioritet (laveste numeriske værdi), skal bidrage værdien. Det samme sker for udgående regler. Synkroniser reglen med højeste prioritet vinder og bidrage værdien til den mappe, der er forbundet.

I nogle tilfælde i stedet for bidrag en værdi, Synkroniser reglen skal finde ud af, hvordan andre regler skal fungerer. Der er nogle speciel konstanter, der bruges til dette tilfælde.

For indgående regler for synkronisering kan i ordret **NULL** bruges til at angive, at strømmen ikke har en værdi til at bidrage. En anden regel med lavere prioritet kan bidrage til en værdi. Hvis ingen regel bidraget med en værdi, fjernes attributten metaverse. For en udgående regel, hvis **NULL** er den endelige værdi efter alle regler for synkronisering er blevet behandlet, fjernes derefter værdien i den tilsluttede mappe.

Ordret **AuthoritativeNull** minder til **NULL** , men med forskellen, ingen lavere prioriteringsregler kan bidrage til en værdi.

En attribut flow kan også bruge **IgnoreThisFlow**. Det er lig med NULL i den betydning, der angiver, der ikke er noget til at bidrage. Forskellen er, at det ikke fjerne en eksisterende værdi i mållejerens. Det er som attributten strømmen har aldrig været der.

Her er et eksempel:

Følgende strømmen kan findes i *ud til AD - bruger Exchange hybrid* :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Dette udtryk skal læses som: Hvis brugerpostkasse er placeret i Azure AD, derefter dataflow attributten fra Azure AD til AD. Hvis ikke, ikke dataflow noget tilbage til Active Directory. I dette tilfælde vil det være AD den eksisterende værdi.

### <a name="importedvalue"></a>ImportedValue
Funktionen ImportedValue er anderledes end alle andre funktioner, da attributnavnet skal stå i anførselstegn i stedet for kantede parenteser:  
`ImportedValue("proxyAddresses")`.

Normalt under synkronisering bruger en attribut den forventede værdi, selvom den endnu ikke er eksporteret eller modtog en fejl under eksport ("toppen af tower"). En indgående synkronisering antages det, at en attribut, der endnu ikke har nået en forbundne mappe til sidst, når den. I nogle tilfælde er det vigtigt at Synkroniser kun en værdi, der er blevet bekræftet af mappen forbundne ("hologram og delta importere tower").

Et eksempel på denne funktion kan findes i out of box synkronisering reglen *i fra AD-bruger almindelige fra Exchange*. I hybride Exchange bør den værdi, der er tilføjet af Exchange online kun skal synkroniseres, når det er blevet bekræftet, at værdien, der er eksporteret:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Rangorden for
Når flere Synkroniser regler forsøger at bidrage samme attributværdien til destinationen, bruges værdien rangorden for til at bestemme vinderen. Reglen med højeste prioritet, laveste numerisk værdi, der skal bidrage attributten i en konflikt.

![Flette typer](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

I denne rækkefølge kan bruges til at definere mere præcis attribut flyder til en lille del af objekter. For eksempel Sørg fra-for-feltet-reglerne for, at attributter fra en aktiveret konto (**Bruger AccountEnabled**) har rangorden for fra andre konti.

Rangorden for kan defineres mellem forbindelser. Der tillader forbindelser med bedre data til at bidrage værdier først.

### <a name="multiple-objects-from-the-same-connector-space"></a>Flere objekter fra det samme rum forbindelse
Hvis du har flere objekter på den samme forbindelse plads, der er knyttet til det samme objekt metaverse, skal rangorden for justeres. Hvis der er flere objekter i omfanget af samme Synkroniser reglen, derefter kan Synkroniser programmet ikke til at afgøre rangordenen. Det er tvetydige hvilke kildeobjektet bør bidrage værdien til metaverse. Denne konfiguration rapporteres som tvetydige, selvom attributterne i kilden har samme værdi.  
![Flere objekter, der er joinforbundne på det samme mv objekt](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

I dette scenarie skal du ændre omfanget af reglerne for synkronisering, så de kilde objekter har forskellige Synkroniser regler i omfang. Som gør det muligt at angive forskellige prioritet.  
![Flere objekter, der er joinforbundne på det samme mv objekt](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Næste trin

- Læs mere om udtrykssproget i [Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Se hvordan deklarativ klargøring er anvendte out of box i [forstå standardkonfiguration](active-directory-aadconnectsync-understanding-default-configuration.md).
- Se, hvordan du ændrer praktiske ved hjælp af deklarativ klargøring i [Sådan foretager en ændring af standard-konfigurationen](active-directory-aadconnectsync-change-the-configuration.md).
- Fortsæt med at læse, hvordan brugere og kontaktpersoner arbejde sammen i [om brugere og kontaktpersoner](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)

**Referenceemner**

- [Azure AD-forbindelse synkronisering: funktioner Reference](active-directory-aadconnectsync-functions-reference.md)
