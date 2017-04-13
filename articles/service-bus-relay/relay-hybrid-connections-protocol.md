<properties 
    pageTitle="Azure Relay Hybrid forbindelser Protocol | Microsoft Azure"
    description="zure Relay Hybrid forbindelser Protocol vejledning."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybrid forbindelser protokol

Azure Relay er et af de vigtigste egenskab søjler af Azure Service Bus platformen. Den Relay nye "Hybrid forbindelser" ikke er en sikker, Åbn protocol udvikling, der er baseret på http- og WebSockets.

Det erstatter den tidligere, lige med navnet "BizTalk-tjenester" funktion, der blev oprettet i en beskyttet protokol foundation. Integration af hybride forbindelser i Azure App Services fortsætter med at fungere som-er.

"Hybrid forbindelser" giver mulighed for oprettelse af tovejs-, binære stream kommunikation mellem to netværksforbundet programmer, hvorved et eller begge af parterne kan være placeret bag NAT-enheder eller Firewalls.

I dette dokument beskrives klientsiden interaktion med Hybrid forbindelser relay til at oprette forbindelse-klienter med lytteren og afsenderen roller og hvordan lyttere acceptere nye forbindelser.

## <a name="interaction-model"></a>Interaktion model

Hybrid forbindelser relay forbinder to parter ved at give et rendezvous punkt i Azure skyen, både parter kan opdage og oprette forbindelse til fra deres egne netværk perspektiv. Dette rendezvous punkt hedder "Hybrid forbindelsen" i dette og andre dokumentation, i API'erne og også på portalen Azure. Hybrid forbindelser tjenesteslutpunkt der kaldes "service" for resten af dette dokument.

Interaktion modellen leans på overskriften etableret ved mange andre netværk API'er:

Der findes en lytter, der angiver først er klar til at håndtere indgående forbindelser, og efterfølgende accepterer dem, når de modtages. På anden siden er der en forbindende klient, som opretter forbindelse lytteren, du forventer at modtage denne forbindelse til accepteret til at oprette en tovejskommunikation sti. "Forbind", "Lytte", "Accepter" er de samme betingelser, du kan finde i de fleste socket API'er.

En videresendt meddelelse model har parternes oprette udgående forbindelser med et slutpunkt, service, som gør "lytteren" også en "klient" i almindelige brug og kan også medføre andre terminologi overloads; den præcise terminologi vi bruge dermed til Hybrid forbindelser er som følger:

Programmerne på begge sider af en forbindelse kaldes "klient", fordi de er klienter til tjenesten. Den klient, der venter og accepterer forbindelser er "lytteren" eller siges at være i "lytteren rollen". Den klient, der starter en ny forbindelse med en lytter via tjeneste hedder "afsenderen" eller "afsender rolle".

## <a name="listener-interactions"></a>Lytteren interaktioner

Lytteren har fire interaktion med tjenesten; alle oplysninger om tråd er beskrevet senere i dette dokument i sektionen reference.

### <a name="listen"></a>Lytte

For at angive er klar til tjenesten, som en lytter er klar til at acceptere forbindelser, der oprettes en udgående web socket forbindelse. Forbindelse kvitteringen samme navn som en Hybrid forbindelse, der er konfigureret i navneområdet Relay og et sikkerhedstoken, der giver "Lytte" videre, hvor navnet. Når web socket besvares af tjenesten, registrering er fuldført, og den eksisterende web socket er aktiv som "kontrolelement kanalen" for at aktivere alle efterfølgende interaktioner. Tjenesten giver mulighed for op til 25 samtidige lyttere på en Hybrid forbindelse. Hvis der er 2 eller flere aktive lyttere, fordeles indgående forbindelser på dem i tilfældig rækkefølge er ingen garanti for sædvanlig fordeling.

### <a name="accept"></a>Acceptere

Når en afsender åbnes en ny forbindelse på tjenesten, vælg tjenesten, og give besked, en af de aktive lyttere på Hybrid forbindelsen. Meddelelsen er sendt til lytteren over kanalen Åbn kontrolelement som en JSON-meddelelse, der indeholder URL-adressen på det Web socket slutpunkt, lytteren skal oprettes forbindelse til for accept af forbindelsen.

URL-adressen kan og skal anvendes direkte af lytteren uden ekstra arbejde; kodet oplysningerne gælder kun for en kort periode, grundlæggende for så længe, som afsenderen er villig til at vente for forbindelsen skal være eksisterende til en komplet, men op til maksimalt 30 sekunder. URL-adressen kan kun bruges til en vellykket forbindelsesforsøg. Så snart socket internetforbindelse med rendezvous URL-adressen er oprettet, er alle yderligere aktivitet på dette Web socket videresendt fra og til afsenderen, uden brugerinput eller fortolkning af tjenesten.

### <a name="renew"></a>Forny 

Det sikkerhedstoken, som skal bruges til at registrere lytteren og vedligeholde salgskanalen kontrolelement udløbe, mens lytteren er aktiv. Token udløbet påvirker ikke igangværende forbindelser, men det kan medføre salgskanalen kontrolelementet skal afbrydes af tjenesten på eller, når tidspunkt, hvor udløbet. "Fornyelse"-bevægelse er en JSON-meddelelse, der kan sende lytteren at erstatte det token, der er knyttet til kanalen kontrolelement, så salgskanalen kontrolelement kan vedligeholdes i længere perioder.

### <a name="ping"></a>Ping 

Hvis kontrolelementet kanalen forbliver inaktiv i lang tid, mellemled på måde, som Indlæs kan balancere eller NAT-enheder slippe TCP-forbindelsen. "Ping" bevægelse undgår, ved at sende en lille mængde data på den kanal, der minder alle på netværket ruten, forbindelsen skal være aktiv, og det fungerer også som en liveness test for lytteren. Hvis ping mislykkes, kontrolelement kanalen skal betragtes som ubrugelig og lytteren skal oprette forbindelse igen.

## <a name="sender-interaction"></a>Afsenderen interaktion

Afsenderen kun har én enkelt interaktion med tjenesten, der oprettes forbindelse.

### <a name="connect"></a>Oprette forbindelse

Den "forbinde" bevægelse åbnes en Web socket på den tjeneste, give navnet på Hybrid forbindelsen og en (valgfrit, men påkrævet som standard) sikkerhedstoken giver "Send" tilladelse i forespørgselsstrengen. Tjenesten derefter interagere med lytteren i den måde, som er beskrevet ovenfor og har lytteren oprette en rendezvous forbindelse, der skal sammenkædes med denne Web socket. Når Web socket er blevet accepteret, bliver alle yderligere interaktioner på Web socket derfor med en forbundne lytter.

## <a name="interaction-summary"></a>Interaktion oversigt

Resultatet af denne interaktion model er, at afsenderen klienten leveres af kvitteringen med en "ren" Web socket, som er forbundet med en lytter og, der kræver ikke yderligere preambles eller forberedelse. Dette giver mulighed for næsten alle eksisterende Web socket klient implementering let udnytte tjenesten Hybrid Connections ved angivelse af en korrekt udarbejdet URL-adresse til deres Web socket klient lag.

Rendezvous forbindelsen Web Socket, der henter lytteren gennem Acceptér interaktionen er også ren og kan afleveres til en eksisterende implementering af Web socket server med nogle minimale ekstra fremstilling, skelner mellem "Accepter" handlinger på deres framework lokale netværk lyttere og Hybrid forbindelser remote "Accepter" handlinger.

## <a name="protocol-reference"></a>Protocol reference

I dette afsnit beskrives detaljerne for protocol interaktioner, der er beskrevet ovenfor.

Alle Web socket forbindelser oprettes på port 443 som en opgradering fra HTTPS 1.1, som repræsenteres ofte af nogle Web socket framework eller API. Beskrivelsen af her der bevares implementering neutrale, uden at foreslå en bestemt ramme.

## <a name="listener-protocol"></a>Lytteren protokol

Lytteren protokollen består af to forbindelse bevægelser og tre meddelelse handlinger.

### <a name="listener-control-channel-connection"></a>Lytteren kontrolelement kanal forbindelse

Kontrolelementet kanalen er åben til at oprette en Web socket forbindelse til:

*wss: / / {navneområde-adresse} /* *$servicebus* */* *hybridconnection /**{path}? sb-CH-handling =... og sb-CH-id =... og sb-CH-token =... "*

*Navneområde-adresse* er det fulde domænenavn af navneområdet Azure Relay, der er vært Hybrid forbindelsen, typisk i formularen {*myname}. servicebus.windows.net.*

Forespørgselsindstillinger streng parameter er som følger

| Parameteren        | Påkrævet? | Beskrivelse                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-CH-handling | Ja       | For rollen lytteren parameteren skal være **sb-CH-handling = lytte**                                                                                                                                |
| {path}       | Ja       | URL-kodet navneområde stien til den forudkonfigurerede Hybrid-forbindelse til at registrere denne lytteren på. Dette udtryk føjes til den faste * **$servicebus**/**hybridconnection /*** sti del. |
| SB-CH-token  | Ja\*     | Lytteren skal angive en gyldig, URL-kodet Service Bus delt Access Token for navneområde eller Hybrid forbindelse, der giver ret **lytte** .                                           |
| SB-CH-id     | Nej        | Dette klient-leveret valgfrit ID giver mulighed for diagnosticering sporing fra til slutpunkt.                                                                                                                             |

Hvis forbindelsen Web Socket mislykkes på grund af Hybrid forbindelse stien ikke bliver registreret, eller et ugyldigt eller mangler token eller en anden fejl, leveres fejl tilbagemeldinger ved hjælp af almindelige HTTP 1.1 status feedback-modellen. Statusbeskrivelsen af skal indeholde en fejl registrering-id, der kan kommunikeres til Azure Support:

| Kode | Fejl          | Beskrivelse                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Blev ikke fundet      | Hybrid forbindelse **sti** er ugyldig eller base URL-adressen er forkert udformet |
| 401  | Uautoriseret   | Sikkerhedstokenet er manglende eller forkert udformet eller ugyldig                  |
| 403  | Forbudt      | Sikkerhedstokenet gælder ikke for denne sti til denne handling          |
| 500  | Intern fejl | Der opstod en fejl i tjenesten                                    |

Hvis socket internetforbindelse lukkes bevidst ned af tjenesten, når den først er sat op årsagen til derved kommunikeres ved hjælp af en passende Web socket protocol fejlkode sammen med en beskrivende fejlmeddelelse, der også omfatter en opfølgning-id. Tjenesten lukkes ikke salgskanalen kontrolelement uden støder på en fejltilstand. En ren lukning er klient, der kontrolleres.

| WS Status | Beskrivelse                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Hybrid forbindelse stien er blevet slettet eller deaktiveret.                           |
| 1008      | Sikkerhedstokenet er udløbet, og godkendelse politikken ignoreres derfor. |
| 1011      | Der opstod en fejl i tjenesten.                                           |

### <a name="accept-handshake"></a>Acceptere handshake 

Acceptér meddelelse sendes af tjenesten til lytteren over kanalen tidligere eksisterende kontrolelement som JSON meddelelse i en tekstrude Web socket. Der er ingen svar på denne meddelelse.

Meddelelsen indeholder et JSON-objekt med navnet "accepterer", som definerer følgende egenskaber på nuværende tidspunkt:

-   **adresse** – strengen URL-adressen skal bruges til at oprette Web Socket til tjenesten til at acceptere et indgående forbindelse.

-   **id** – et entydigt id for denne forbindelse. Hvis-id, der blev angivet af afsenderen klienten, det er afsender, der er leveret værdien, ellers er det en genereres af systemet værdi.

-   **connectHeaders** – alle HTTP-headere, der er leveret til Relay slutpunktet af afsenderen, som også indeholder Sec-WebSocket-protokollen og Sec-WebSocket-udvidelser overskrifterne.

| Acceptere meddelelse                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

Den URL-adresse, der er angivet i JSON meddelelsen bruges af lytteren for at fastlægge denne Web Socket for at acceptere eller afvise afsender socket.

#### <a name="accepting-the-socket"></a>Godkendelse af socket

Hvis du vil acceptere, opretter lytteren WebSocket forbindelse til den angivne adresse.

Noget imod, at for perioden preview adressen URI kan bruge en enkelt IP-adresse og TLS-certifikat, der leveres af serveren mislykkes validering på denne adresse. Dette vil blive udbedret under Vis udskrift.

Hvis meddelelsen "acceptere" indeholder et "Sec-WebSocket-protokollen" sidehoved, forventes, at acceptere lytteren kun WebSocket, hvis den understøtter denne protokol og, angiver sidehovedet som WebSocket er etableret.

På samme måde gælder for overskriften "Sec-WebSocket-udvidelser". Hvis, som understøtter et filtypenavn, skal den indstille sidehovedet til *server* side svaret af nødvendige "Sec-WebSocket-udvidelser" kvitteringen for filtypenavnet.

URL-adressen skal bruges som-er til at oprette Acceptér socket, men indeholder følgende parametre:

| Parameteren        | Påkrævet? | Beskrivelse                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-CH-handling | Ja       | For accept af en socket parameteren skal være **sb-CH-handling = acceptere**                                                                                                                                                                                                                          |
| {path}       | Ja       | URL-kodet navneområde stien til den forudkonfigurerede Hybrid-forbindelse til at registrere denne lytteren på. Dette udtryk føjes til den faste * **$servicebus**/**hybridconnection /*** sti del.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| SB-CH-id | No        | Se beskrivelse af **id** ovenfor.                                                                                                                                                                                                                                                              |

Hvis der er en fejl, kan tjenesten svare på følgende måde:

| Kode | Fejl          | Beskrivelse                         |
|------|----------------|-------------------------------------|
| 403  | Forbudt      | URL-adressen er ikke gyldige.               |
| 500  | Intern fejl | Der opstod en fejl i tjenesten |

Når forbindelsen er oprettet, lukkes serveren Web socket, når afsender Web socket lukkes ned eller med følgende status

| WS Status | Beskrivelse                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Afsenderen klienten lukker forbindelsen                                        |
| 1001      | Hybrid forbindelse stien er blevet slettet eller deaktiveret.                           |
| 1008      | Sikkerhedstokenet er udløbet, og godkendelse politikken ignoreres derfor. |
| 1011      | Der opstod en fejl i tjenesten.                                           |

#### <a name="rejecting-the-socket"></a>Afvise socket

Afvise socket efter undersøge meddelelsen "acceptere" kræver en lignende handshake, så statuskode og status beskrivelse kommunikere årsagen til afvisningen kan flyde tilbage til afsenderen.

Design valg protocol her, er at bruge en WebSocket handshake (som er udviklet til at afslutte i en defineret fejltilstand) så lytteren klient installationer kan fortsætte med at skal have en WebSocket klient og behøver ikke at anvende en ekstra, bare HTTP-klienten.

Hvis du vil afvise Socketen, klienten benytter adressen URI fra meddelelsen "acceptere" og tilføjer to forespørgselsparametre streng:

| Parameteren             | Påkrævet? | Beskrivelse                             |
|-------------------|-----------|-----------------------------------------|
| Statuskode        | Ja       | Numeriske HTTP-statuskode                |
| statusDescription | Ja       | Human læsbare årsagen til afvisningen |

Den resulterende URI bruges derefter til at oprette forbindelse WebSocket; igen, Husk, at TLS-certifikatet ikke kan stemmer overens adressen under Vis udskrift, så validering kan være nødvendigt at være deaktiveret.

Når du har fuldført korrekt, mislykkes denne handshake bevidst med en HTTP-fejlkode 410, siden ingen WebSocket er blevet oprettet. Hvis der opstår fejl, er disse indstillinger:

| Kode | Fejl          | Beskrivelse                         |
|------|----------------|-------------------------------------|
| 403  | Forbudt      | URL-adressen er ikke gyldige.               |
| 500  | Intern fejl | Der opstod en fejl i tjenesten |

### <a name="listener-token-renewal"></a>Lytteren token fornyelse

Når den lytteren token er ved at udløbe, kan det erstatte det ved at sende en tekstmeddelelse til ramme til tjenesten via salgskanalen eksisterende kontrolelement. Meddelelsen indeholder et JSON objekt med navnet "renewToken", som definerer følgende egenskab på nuværende tidspunkt:

-   **token** – en gyldig, URL-kodet Service Bus delt Access Token til navneområdet eller Hybrid forbindelse, der giver ret **lytte** .

| renewToken meddelelse                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Hvis det token ikke lykkes, adgang nægtet og skybaseret tjeneste lukkes kontrolelementet kanal websocket med en fejl, ellers der findes ingen svar.

| WS Status | Beskrivelse                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | Sikkerhedstokenet er udløbet, og godkendelse politikken ignoreres derfor. |

<a name="sender-protocol"></a>Afsenderen protokol
---------------

Afsenderen protokollen er effektivt identisk med hvordan en lytter oprettes. Målet er gennemsigtige til til slut Web socket. Den adresse, der skal oprettes forbindelse til er den samme som for lytteren, men "handlingen" skiller sig ud og tokenet skal bruge en anden tilladelse:

*wss: / / {navneområde-adresse} /* *$servicebus* */* *hybridconnection /**{path}? sb-CH-handling =... og sb-CH-id =... \[& sbc-CH-token =... \]*

*Navneområde-adresse* er det fulde domænenavn af navneområdet Azure Relay, der er vært Hybrid forbindelsen, typisk i formularen {*myname}. servicebus.windows.net.*

Anmodningen kan indeholde vilkårlig ekstra HTTP-headere, herunder dem, der er defineret af programmet og. Alle medfølgende sidehoveder flyde til lytteren og kan findes på "connectHeader" objektet i meddelelsen "acceptere" kontrolelement.

Forespørgselsindstillinger streng parameter er som følger

| Parameteren        | Påkrævet? | Beskrivelse                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SB-CH-handling | Ja       | For rollen lytteren parameteren skal være **handling = forbinde**                                                                                                                                                    |
| {path}       | Ja       | URL-kodet navneområde stien til den forudkonfigurerede Hybrid-forbindelse til at registrere denne lytteren på.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| SB-CH-token | Yes\*     | Lytteren skal angive en gyldig, URL-kodet Service Bus delt Access Token for navneområde eller Hybrid forbindelse, der giver ret **Send** .                                                            | | SB-CH-id | No        | Et valgfrit ID, der kan til slut diagnosticering sporing og de gøres tilgængelige for lytteren under Accepter kvitteringen.                                                                                       |

Hvis forbindelsen Web Socket mislykkes på grund af Hybrid forbindelse stien ikke bliver registreret, eller et ugyldigt eller mangler token eller en anden fejl, leveres fejl tilbagemeldinger ved hjælp af almindelige HTTP 1.1 status feedback-modellen. Statusbeskrivelsen af skal indeholde en fejl registrering-id, der kan kommunikeres til Azure Support:

| Kode | Fejl          | Beskrivelse                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Blev ikke fundet      | Hybrid forbindelse **sti** er ugyldig eller base URL-adressen er forkert udformet |
| 401  | Uautoriseret   | Sikkerhedstokenet er manglende eller forkert udformet eller ugyldig                  |
| 403  | Forbudt      | Sikkerhedstokenet gælder ikke for denne sti til denne handling          |
| 500  | Intern fejl | Der opstod en fejl i tjenesten                                    |

Hvis socket internetforbindelse lukkes bevidst ned af tjenesten, når den først er sat op årsagen til derved kommunikeres ved hjælp af en passende Web socket protocol fejlkode sammen med en beskrivende fejlmeddelelse, der også omfatter en opfølgning-id.

| WS Status | Beskrivelse                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | Lytteren Luk denne socket.                                                 |
| 1001      | Hybrid forbindelse stien er blevet slettet eller deaktiveret.                           |
| 1008      | Sikkerhedstokenet er udløbet, og godkendelse politikken ignoreres derfor. |
| 1011      | Der opstod en fejl i tjenesten.                                           |

## <a name="next-steps"></a>Næste trin:

- [Relay ofte stillede spørgsmål](relay-faq.md)
- [Oprette et navneområde](relay-create-namespace-portal.md)
- [Introduktion til .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Komme i gang med Node](relay-hybrid-connections-node-get-started.md)