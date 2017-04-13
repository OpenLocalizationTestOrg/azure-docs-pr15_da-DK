<properties
   pageTitle="Administrere identitet i Azure | Microsoft Azure"
   description="Forklarer, og sammenligner de forskellige metoder til administration af identitet i hybride systemer, der strækker sig over grænsen på-lokale/skyen med Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Administrere identitet i Azure

I de fleste enterprise-systemer baseret på Windows, bruger du Active Directory (AD) til at levere identitet management services til dine programmer. AD fungerer godt i et lokalt miljø, men når du udvider din netværksinfrastruktur i skyen, har du nogle vigtige beslutninger at gøre med hensyn til Sådan administreres identitet. Skal du udvide dine lokale domæner for at inkorporere FOS i skyen? Skal du oprette nye domæner i skyen, og hvis det er tilfældet, hvordan? Skal du implementerer din egen skov i skyen, eller skal du gøre brug af Azure Active Directory (AAD)?

I denne artikel beskrives nogle almindelige indstillinger for møde de udfordringer, som dette scenario, og hjælper med at du bestemme, hvilken løsning der bedst opfylder dine behov, der er baseret på dine krav.

## <a name="using-azure-active-directory"></a>Brug af Azure Active Directory

Du kan bruge AAD til at oprette en AD-domæne i skyen og knytte den til et lokalt AD-domæne. AAD gør det muligt at konfigurere single sign-on (SSO) til brugere, der kører programmer, der er åbnet via skyen.

[! [0]][0]

AAD er en enkle måde at implementere et sikkerhedsdomæne i skyen. Det bruges af mange Microsoft-programmer, som Microsoft Office 365. 

Fordelene ved at bruge AAD:

- Der er ingen grund til at bevare en Active Directory-infrastruktur i skyen. AAD er helt administrerede og vedligeholdes af Microsoft.

- AAD giver de samme oplysninger om identitet, der er tilgængelige i det lokale miljø.

- Godkendelse kan forekomme i Azure, reducere behovet for eksterne programmer og brugere til at kontakte det lokale domæne.

Punkter du bør overveje, når du bruger AAD:

- Identitet services er begrænset til brugere og grupper. Du har ikke mulighed for at godkende tjenesten og computerkonti.

- Du skal konfigurere forbindelse med dit lokale domæne til at holde mappen AAD synkroniseret. 

- Du er ansvarlig for publicering af programmer, der kan få adgang til brugere i skyen gennem AAD.

Oplysninger, skal du læse [Implementering af Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Brug af Active Directory i skyen knyttet til en lokal skov

Du kan hoste lokale AD katalogtjenester (AD DS), men i et hybridt scenarie, hvor elementer i et program er placeret i Azure kan det være mere effektivt at gentage denne funktionalitet og AD lager til skyen. Denne metode kan hjælpe med at reducere ventetid forårsaget ved at sende godkendelse og lokale godkendelse af anmodninger om fra skyen tilbage til AD DS kører i det lokale miljø. 

[! [1]][1]

Denne fremgangsmåde kræver, at du opretter dit eget domæne i skyen og deltage i den til den lokale skov. Du opretter FOS Hvis du vil placere AD DS tjenesterne.

Fordelene ved at bruge et andet domæne i skyen:

- Giver mulighed for at godkende brugeren, service og computer konti lokalt og i skyen.

- Giver adgang til de samme identitetsoplysninger, der er tilgængelige i det lokale miljø.

- Der er ingen grund til at administrere en separat AD-skov domænet i skyen kan tilhøre den lokale skov.

- Du kan anvende Gruppepolitik, der er defineret af lokale Gruppepolitikobjekt objekter til domænet i skyen.

Overvejelser ved brug af et andet domæne i skyen:

- Skal du oprette og administrere dine egne AD DS serverne og domænet i skyen.

- Der kan være nogle synkronisering ventetid mellem Domæneservere i skyen og servere, som kører i det lokale miljø.

Oplysninger om, hvordan du konfigurerer denne arkitektur finder du [Udvide Active Directory Directory Services (tilføjer) til Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Brug af Active Directory med et separat område

En organisation, der kører lokale Active Directory (AD) muligvis et område, som omfatter mange forskellige domæner. Du kan bruge domæner til at levere isolationsniveauet mellem funktionelle områder, der skal holdes adskilt, muligvis af sikkerhedsmæssige årsager, men du kan dele oplysninger mellem domæner ved at etablere tillidsforhold.

[! [2]][2]

En organisation, der anvender forskellige domæner kan drage fordel af Azure ved at flytte en eller flere af disse domæner ind i et separat område i skyen. Alternativt kan en organisation vil beholde alle skyen ressourcer logisk adskiller sig fra disse venteposition i det lokale miljø, og gemme oplysninger om skyen ressourcer i deres egen mappe, som en del af et område, der er også opbevares i skyen.

Fordelene ved at bruge et separat område i skyen:

- Du kan implementere lokale identiteter og adskille kun Azure identiteter.

- Der er ikke nødvendigt at gentage fra lokale AD skov til Azure, reducere virkningerne af netværksventetid.

Overvejelser i forbindelse med:

- Godkendelse for lokale identiteter i skyen udfører ekstra netværk *hop* til en lokal installation AD-servere.

- Kræver, at du kan implementere din egen AD DS servere og skov i skyen, og Opret de relevante tillidsforhold mellem områder.

Dokumentet, [opretter en Active Directory Directory Services (tilføjer) ressourceskov i Azure] [ adds-forest-in-azure] beskriver, hvordan du kan implementere denne metode mere detaljeret.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>Brug af Active Directory Federation Services (ADFS) med Azure

ADFS kan køre i det lokale miljø, men i et hybridt scenarie, hvor programmer er placeret i Azure kan det være mere effektivt at implementere denne funktionalitet i skyen, som vist nedenfor.

[! [3]][3]

Denne arkitektur er særligt nyttigt for:

- Løsninger, der anvender samlet tilladelse til at fremvise webprogrammer til partnerorganisationer.

- Systemer, der understøtter adgang fra en webbrowser, der kører uden for virksomhedens firewall.

- Systemer, så brugerne kan få adgang til webprogrammer ved at oprette forbindelse fra autoriserede eksterne enheder som eksterne computere, notesbøger og andre mobile enheder. 

Fordelene ved at bruge ADFS med Azure:

- Du kan udnytte krav-aware programmer.

- Den giver mulighed for at have tillid til eksterne partnere til godkendelse.

- Det giver kompatibilitet med stor mængde godkendelsesprotokoller.

Overvejelser ved brug af ADFS med Azure:

- Det kræver, at du vil implementere din egen tilføjer, ADFS og ADFS Web Application Proxy-servere i skyen.

- Denne arkitektur kan være kompleks til at konfigurere.

Oplysninger, skal du læse [Implementere ADFS Active Directory Federation Services () i Azure][adfs-in-azure].

## <a name="next-steps"></a>Næste trin

Nedenstående ressourcer beskriver, hvordan at implementere arkitekturer, der er beskrevet i denne artikel.

- [Implementering af Azure Active Directory][implementing-aad]
- [Hvis du vil udvide katalogtjenester Active Directory (tilføjer) til Azure][extending-adds]
- [Oprette en Active Directory Directory Services (tilføjer) ressourceskov i Azure][adds-forest-in-azure]
- [Implementering af Active Directory Federation Services (ADFS) i Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Skyen identitet arkitektur ved hjælp af Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Secure hybrid netværksarkitektur med Active Directory"
[2]: ./media/guidance-identity/figure3.png "Secure hybrid netværksarkitektur med separat AD domæner og områder"
[3]: ./media/guidance-identity/figure4.png "Secure hybrid netværksarkitektur med ADFS"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md