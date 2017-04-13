<properties
    pageTitle="Rollebaseret access kontrolelement fejlfinding | Microsoft Azure"
    description="Få hjælp med problemer eller spørgsmål om rolle baseret adgangskontrol ressourcer."
    services="azure-portal"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="kgremban"/>

# <a name="role-based-access-control-troubleshooting"></a>Rollebaseret adgangskontrol til fejlfinding

## <a name="introduction"></a>Introduktion

[Rollebaseret adgangskontrol](role-based-access-control-configure.md) er en effektiv funktion, der gør det muligt at detaljerede stedfortræderadgang til ressourcer i Azure. Det betyder, at du kan føle dig tryg give en bestemt person ret til at bruge præcis det, de skal, og det er ikke længere. Dog nogle gange ressource model til Azure ressourcer kan være kompliceret, og det kan være svært at forstå nøjagtigt, hvad du giver tilladelse til at.

Dette dokument fortæller dig, hvad du kan forvente, når du bruger nogle af rollerne i portalen Azure. Disse tre roller dækker alle ressourcetyper:

- Ejer  
- Bidragyder  
- Reader  

Ejere og bidragydere har fuld adgang til management oplevelsen, men en bidragyder kan ikke give adgang til andre brugere eller grupper. Ting, du får lidt mere interessant med læserrollen, så det er, hvor vi skal bruge mere tid. Du kan se [Rollebaseret adgangskontrol get-gang artikel](role-based-access-control-configure.md) få mere at vide om, hvordan du give adgang.

## <a name="app-service-workloads"></a>App-tjenesten arbejdsmængder

### <a name="write-access-capabilities"></a>Skriveadgang funktioner

Hvis du tildeler en bruger skrivebeskyttet adgang til en enkelt web-app, kan nogle funktioner er deaktiveret, ikke kan du forventer. De følgende funktioner kræver **skrive** adgang til en WebApp (bidragyder eller ejer), og er ikke tilgængelige i et scenarie med skrivebeskyttet tilstand.

- Kommandoer (fx start, stop osv.)
- Ændring af indstillinger som generelle konfiguration, indstillinger for skalering, indstillinger for sikkerhedskopiering og indstillinger for overvågning
- Få adgang til publicering legitimationsoplysninger og andre hemmeligheder som app-indstillinger og strenge
- Streaming logfiler
- Diagnosticeringslogfiler konfiguration
- Console (kommandoprompt)
- Aktive og seneste installationer (til lokale ciffer fortløbende installation)
- Anslået bruger
- Web-test
- Virtuelt netværk (kun synlige for en læser, hvis et virtuelt netværk som tidligere er blevet konfigureret af en bruger med skriveadgang).

Hvis du ikke har adgang til en af disse felter, skal du bede din administrator om bidragyder adgang til WebApp.

### <a name="dealing-with-related-resources"></a>Håndtere relaterede ressourcer

Onlines kompliceret ved tilstedeværelsen af et par forskellige ressourcer, der samspil. Her er en typisk ressourcegruppe med et par websteder:

![Web app-ressourcegruppe](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Som et resultat, hvis du giver en person adgang til lige online, meget af funktionaliteten på bladet websted på portalen Azure deaktiveres.

Disse elementer kræver **skrive** adgang til **App-serviceaftale** , der svarer til dit websted:  

- Visning af WebApp er priser niveau (gratis eller Standard)  
- Skala konfiguration (antallet af forekomster, virtuelt størrelse, autoskalering indstillinger)  
- Kvoter (lagerplads, båndbredde, CPU)  

Disse elementer kræver **skrive** adgang til hele **ressourcegruppe** , der indeholder dit websted:  

- SSL-certifikater og bindinger (dette er fordi SSL-certifikater kan deles mellem websteder i samme ressourcegruppe og geografisk placering)  
- Regler for påmindelser  
- Autoskalering indstillinger  
- Indsigt programkomponenter  
- Web-test  

## <a name="virtual-machine-workloads"></a>Virtuelt arbejdsmængder

Meget ud med webapps, nogle funktioner på bladet virtuelt kræver skrive-adgang til den virtuelle maskine eller til andre ressourcer i ressourcegruppen.

Virtuelle maskiner er relateret til domæne navne, virtuelle netværk, lagerplads konti og regler for påmindelser.

Disse elementer kræver **skrive** adgang til den **virtuelle maskine**:

- Slutpunkter  
- IP-adresser  
- Diske  
- Filtypenavne  

Dette kræver **skrive** adgang til både **virtuelt**og **ressourcegruppe** (sammen med domænenavnet), som det er i:  

- Tilgængelighed sæt  
- Indlæse ikke-opgjorte sæt  
- Regler for påmindelser  

Hvis du ikke har adgang til en af disse felter, skal du bede din administrator om bidragyder adgang til ressourcegruppen.

## <a name="see-more"></a>Se mere
- [Rolle baseret adgangskontrol](role-based-access-control-configure.md): Introduktion til RBAC i portalen Azure.
- [Indbyggede roller](role-based-access-built-in-roles.md): Få mere at vide om de roller, der leveres som standard i RBAC.
- [Brugerdefineret roller i Azure RBAC](role-based-access-control-custom-roles.md): Lær at oprette brugerdefinerede roller, så det passer til dine access-behov.
- [Oprette en access ændre rapporten Oversigt over](role-based-access-control-access-change-history-report.md): Hold styr på ændre rolletildelinger i RBAC.
