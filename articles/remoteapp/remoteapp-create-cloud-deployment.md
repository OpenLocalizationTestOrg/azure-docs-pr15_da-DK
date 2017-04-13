<properties 
    pageTitle="Sådan oprettes en skyen samling af Azure RemoteApp | Microsoft Azure" 
    description="Få mere at vide, hvordan du opretter en installation af Azure RemoteApp, der gemmer dataene i Azure skyen." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Sådan oprettes en skyen samling af Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Der er to typer af [Azure RemoteApp af websteder](remoteapp-collections.md): 

- Skyen: findes helt i Azure. Du kan vælge at gemme alle data i skyen (så en kun skyen samling) eller for at oprette forbindelse din samling til en VNET og gemme dataene der.   
- Hybrid: omfatter et virtuelt netværk for lokal adgang - dette kræver brug af Azure AD og et lokalt Active Directory-miljø.

Dette selvstudium vejleder dig gennem processen med at oprette en skyen af websteder. Der er fire trin: 

1.  Oprette en Azure RemoteApp af websteder.
2.  Du kan også konfigurere katalogsynkronisering. Hvis du bruger Azure AD + Active Directory, du skal synkronisere brugere, kontakter og adgangskoder fra din lokale Active Directory til din Azure AD-lejer.
5.  Publicere apps.
6.  Konfigurere brugeradgang.


**Inden du går i gang**

Du skal gøre følgende, før oprettelse af gruppen af websteder:

- [Tilmelde dig](https://azure.microsoft.com/services/remoteapp/) til Azure RemoteApp. 
- Indsamle oplysninger om de brugere, du vil give adgang til. Det kan være enten Microsoft-kontooplysninger eller Active Directory arbejde kontooplysninger for brugere.
- Denne procedure antages det, at du er enten ved at bruge en af skabelonbillederne leveres som en del af dit abonnement eller, at du allerede har overført skabelon billedet, du vil bruge. Hvis du vil overføre en anden skabelonbillede, kan du gøre det fra siden billeder af webstedsskabeloner. Bare Klik på **Overfør skabelonbillede af en** og følge trinnene i guiden. 
- Vil du bruge Office 365 ProPlus billedet? Se oplysninger [her](remoteapp-officesubscription.md).
- Vil du give brugerdefinerede apps eller LOB programmer? Oprette et nyt [billede](remoteapp-imageoptions.md) og bruge det i skyen af websteder.
- Finde ud af, om du har brug at oprette forbindelse til en VNET. Hvis du vælger at oprette forbindelse til en VNET, skal du kontrollere den opfylder [størrelseshåndtag retningslinjer](remoteapp-vnetsizing.md) for og at det [kan oprette forbindelse til RemoteApp](remoteapp-vnet.md). Se [VNET planlægning artikel ](remoteapp-planvnet.md)kan finde flere oplysninger.
- Hvis du bruger en VNET, skal du beslutte, om du vil føje den til din lokale Active Directory-domæne.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Trin 1: Oprette en samling i skyen - med eller uden en VNET##


Brug følgende fremgangsmåde for at **oprette en kun skyen af websteder**:

1. Gå til siden RemoteApp i administrationsportalen.
2. Klik på **Ny > Hurtig oprettelse**.
3. Angiv et navn til din samling, og vælg dit område.
4. Vælg den plan, du vil bruge - standard eller grundlæggende.
5. Vælg skabelonen, der bruges til denne af websteder. 

    **Tip:** Dit abonnement på RemoteApp leveres med [billeder af webstedsskabeloner](remoteapp-images.md) , der indeholder Office 365 eller Office 2013 (til prøveabonnement brug) programmer, nogle publiceret (såsom Word) og andre klar til at publicere. Du kan også oprette et nyt [billede](remoteapp-imageoptions.md) og bruge den i skyen af websteder.


1. Klik på **Opret RemoteApp af websteder**.
    
    **Vigtige:** Det kan tage op til 30 minutter til at klargøre din samling.

Når din Azure RemoteApp af websteder er blevet oprettet, skal du dobbeltklikke på navnet på gruppen af websteder. Der kan få vist siden **Hurtig Start** – dette er, hvor du er færdig med at konfigurere samlingen.

Du kan følge nedenstående trin for at oprette en **skyen + VNET af websteder**:

1. Gå til siden Azure RemoteApp i administrationsportalen.
2. Klik på **nyt** > **oprette med VNET**.
3. Angiv et navn til din samling.
4. Vælg den plan, du vil bruge - standard eller grundlæggende.
5. Vælg den VNET, du allerede har oprettet. Ikke ved, hvordan du gør dette? Nu er trinnene i emnet [hybrid](remoteapp-create-hybrid-deployment.md) .
6. Beslutte, om du vil deltage i din samling til dit domæne. Hvis Ja, skal du bruge AD-forbindelse til at integrere Azure AD og Active Directory-miljø. Der er beskrevet i under i **trin 2**.
6. Klik på **Opret RemoteApp af websteder**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Trin 2: Konfigurer Active Directory-katalogsynkronisering (valgfrit) ##

Hvis du vil bruge Active Directory, kræver Azure RemoteApp katalogsynkronisering mellem Azure Active Directory og din lokale Active Directory for at synkronisere brugere, kontakter og adgangskoder til din Azure Active Directory-lejer. Se [Konfiguration af Active Directory til Azure RemoteApp](remoteapp-ad.md) oplysninger om planlægning. Du kan også gå direkte til [AD-forbindelse](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) for at få oplysninger.

## <a name="step-3-publish-apps"></a>Trin 3: Publicere apps ##

En Azure RemoteApp app er den app eller det program, du angiver, at dine brugere. Det er placeret i den skabelonbillede, du har overført til samlingen. Når en bruger åbner en app, appen vises til at køre i deres lokale miljø, men det kører virkelig i en virtuel maskine i Azure. 

Før dine brugere kan få adgang til apps, skal du publicere dem – publicere apps kan brugerne adgang til dem via klienten Fjernskrivebord.
 
Du kan udgive flere apps til din Azure RemoteApp af websteder. Klik på **Publicer** for at tilføje et program fra siden publicering. Du kan enten udgive fra menuen **Start** af billedet, skabelon eller ved at angive stien på skabelonbillede for appen. Hvis du vil tilføje fra menuen **Start** , Vælg app til at publicere. Hvis du vælger at angive en sti til app, kan du angive et navn til appen og stien til hvor den er installeret på skabelon billedet.

## <a name="step-4-configure-user-access"></a>Trin 4: Konfigurere brugeradgang ##

Nu hvor du har oprettet din samling, skal du føje de brugere, du vil kunne bruge dine eksterne ressourcer. Hvis du bruger Active Directory, de brugere, du kan give dig adgang for at kunne skal findes i Active Directory-lejeren, der er knyttet til abonnementet, du har brugt til at oprette denne samling.

1.  Klik på **Konfigurer brugeradgang**fra siden Hurtig Start. 
2.  Angiv arbejdskonto (fra Active Directory) eller Microsoft-konto, du vil give adgang til.

    **Noter:** 

    Sørg for, at du bruger den “user@domain.com” format.

    Hvis du bruger Office 365 ProPlus i af websteder, skal du bruge Active Directory-identiteter til dine brugere. Dette hjælper med at validere licenser. 

3.  Klik på **Gem**, når brugerne er godkendt.


## <a name="next-steps"></a>Næste trin ##

Det var det – du har oprettet og installeret din Azure RemoteApp skyen samling. Næste trin er at få dine brugere til at hente og installere klienten Fjernskrivebord. Du kan finde URL-adressen for-klienten på siden Azure RemoteApp Hurtig Start. Derefter har brugerne skal logge på klienten og få adgang til de apps, du har publiceret.

### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at 
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** for at foretage ændringer – dem, der kommer til os til gennemsyn, og klik derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.