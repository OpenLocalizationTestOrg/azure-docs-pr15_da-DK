<properties
    pageTitle="Sådan oprettes en hybrid af websteder til Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter en installation af RemoteApp, der opretter forbindelse til dit interne netværk."
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

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Sådan oprettes en hybrid af websteder til Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Der er to typer af Azure RemoteApp af websteder:

- Skyen: findes helt i Azure. Du kan vælge at gemme alle data i skyen (så en kun skyen samling) eller for at oprette forbindelse din samling til en VNET og gemme dataene der.   
- Hybrid: omfatter et virtuelt netværk for lokal adgang - dette kræver brug af Azure AD og et lokalt Active Directory-miljø.

Ikke ved, at du har brug for? Se [, hvilken af websteder har du brug for til Azure RemoteApp](remoteapp-collections.md).

Dette selvstudium vejleder dig gennem processen med at oprette en hybrid af websteder. Der er otte trin:

1.  Bestemme, hvilket [billede](remoteapp-imageoptions.md) skal bruges til din samling. Du kan oprette en brugerdefineret afbildning eller bruge en af de Microsoft billeder, der følger med dit abonnement.
2. Konfigurere din virtuelt netværk. Se oplysninger om [Planlægning af VNET](remoteapp-planvnet.md) og [størrelse](remoteapp-vnetsizing.md) .
2.  Oprette en samling.
2.  Deltage i din samling til dit lokale domæne.
3.  Føje et billede af designskabelon til din samling.
4.  Konfigurere katalogsynkronisering. Azure RemoteApp kræver, at du integrerer med Azure Active Directory ved enten 1) konfiguration af Azure Active Directory-synkronisering med indstillingen synkronisering af adgangskoder eller 2) konfiguration af Azure Active Directory-synkronisering uden indstillingen synkronisering af adgangskoder, men bruger et domæne, der er knyttet til AD FS. Se [konfigurationsoplysninger til Active Directory med RemoteApp](remoteapp-ad.md).
5.  Publicere RemoteApp apps.
6.  Konfigurere brugeradgang.

**Inden du går i gang**

Du skal gøre følgende, før oprettelse af gruppen af websteder:

- [Tilmelde dig](https://azure.microsoft.com/services/remoteapp/) til Azure RemoteApp.
- Oprette en brugerkonto i Active Directory for at bruge som Azure RemoteApp tjenestekontoen. Begræns tilladelser for denne konto, så den kan kun deltage computere til domænet.
- Indsamle oplysninger om dit lokale netværk: IP-adresse, oplysninger og detaljer om VPN-enhed.
- Installer [Azure PowerShell](../powershell-install-configure.md) -modulet.
- Indsamle oplysninger om de brugere, du vil give adgang til. Du skal bruge Azure Active Directory brugerens hovednavn (for eksempel name@contoso.com) for hver bruger. Sørg for, at UPN stemmer overens med mellem Azure AD og Active Directory.
- Vælg skabelonen billedet. Billede af designskabelon en Azure RemoteApp indeholder de tilhørende apps og -programmer, du vil publicere for dine brugere. Du kan få flere oplysninger i [Azure RemoteApp billedindstillinger](remoteapp-imageoptions.md) .
- Vil du bruge Office 365 ProPlus billedet? Se oplysninger [her](remoteapp-officesubscription.md).
- [Konfiguration af Active Directory for RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Trin 1: Konfigurere dit virtuelle netværk
Du kan installere en hybrid af websteder, der bruger et eksisterende Azure virtuelle netværk, eller du kan oprette et nyt virtuelt netværk. Et virtuelt netværk kan dine brugere adgang til data i dit lokale netværk via RemoteApp eksterne ressourcer. Ved hjælp af en Azure virtuelt netværk giver samling direkte netværksadgang til andre Azure tjenester og virtuelle maskiner, der er installeret til det pågældende virtuelle netværk.

Kontrollér, at du gennemgå oplysningerne om [Planlægning af VNET](remoteapp-planvnet.md) og [VNET størrelse](remoteapp-vnetsizing.md) , før du opretter din VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Oprette en Azure VNET og føje den til din Active Directory-installation

Starte med at oprette et [virtuelt netværk](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Det gør du på fanen **netværk** i portalen Azure. Du skal bruge netværket virtuel tilsluttet den Active Directory-installation, der er synkroniseret til din Azure Active Directory-lejer.

Se [oprette et virtuelt netværk ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) kan finde flere oplysninger.

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Sørg for, at netværket virtuel er klar til Azure RemoteApp
Før du opretter din samling, Lad os Sørg for, at din nye virtuelle netværk er klar. Du kan kontrollere ved at gøre følgende:

1. Oprette en Azure virtuelt i undernet i det virtuelle netværk, du lige har oprettet for RemoteApp.
2. Bruge Fjernskrivebord til at oprette forbindelse til den virtuelle maskine. (Klik på **Opret forbindelse**).
3. Føje den til den samme Active Directory-installation, du vil bruge til RemoteApp.

Det virker? Virtuel netværket og undernet er klar til Azure RemoteApp!

Du kan finde flere oplysninger om oprettelse af Azure virtuelle maskiner og oprette forbindelse til dem med Remote Desktop [her](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Trin 2: Opret en Azure RemoteApp af websteder ##



1. Gå til siden Azure RemoteApp i [Azure-portalen](http://manage.windowsazure.com).
2. Klik på **Ny > Opret med VNET**.
3. Angiv et navn til din samling.
4. Vælg den plan, du vil bruge - standard eller grundlæggende.
5. Vælg din VNET fra rullemenuen listen og derefter dit undernet.
6. Vælg for at føje den til dit domæne.
5. Klik på **Opret RemoteApp af websteder**.

Når din Azure RemoteApp af websteder er blevet oprettet, skal du dobbeltklikke på navnet på gruppen af websteder. Der kan få vist siden **Hurtig Start** – dette er, hvor du er færdig med at konfigurere samlingen.

Gå noget galt? Se [oplysninger om fejlfinding hybrid af websteder](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Trin 3: Oprette din samling et Link til det lokale domæne ##


1. Klik på **Deltag i et lokalt domæne**på siden **Hurtig Start** .
2. Føje tjenestekonto Azure RemoteApp til dit lokale Active Directory-domæne. Du skal domænenavn, organisationsenhed, service-kontobrugernavn og adgangskode.

    Dette er de oplysninger, du indsamlede Hvis du har fulgt trinnene i [Konfigurere Active Directory til Azure RemoteApp](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Trin 4: Link til et Azure RemoteApp-billede ##

Billede af designskabelon en Azure RemoteApp indeholder de programmer, du vil dele med brugere. Du kan oprette et nyt [Billede af designskabelon](remoteapp-imageoptions.md) eller oprette et link til et eksisterende billede (én allerede har importeret eller overføres til Azure RemoteApp). Du kan også oprette et link til en af Azure RemoteApp [billeder af webstedsskabeloner](remoteapp-images.md) , der indeholder Office 365 eller Office 2013 (til prøveabonnement brug)-programmer.

Hvis du sender det nye billede, skal du angive navnet, og vælg en placering til billedet. På den næste side i guiden, skal du se en række PowerShell-cmdlet'er - kopi og køre følgende cmdletter fra en øgede Windows PowerShell-prompt til at overføre den angivne afbildning.

Hvis du opretter et link til en eksisterende skabelon afbildning, du skal blot angive billede navn, placering og tilknyttede Azure abonnement.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Trin 5: Konfigurere Active Directory katalogsynkronisering ##

Azure RemoteApp kræver, at du integrerer med Azure Active Directory ved enten 1) konfiguration af Azure Active Directory-synkronisering med indstillingen synkronisering af adgangskoder eller 2) konfiguration af Azure Active Directory-synkronisering uden indstillingen synkronisering af adgangskoder, men bruger et domæne, der er knyttet til AD FS.

Se [AD-forbindelse](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) - i denne artikel kan du konfigurere katalogintegration i trin 4.

Se [Oversigt over mappesynkronisering](http://msdn.microsoft.com//library/azure/hh967642.aspx) til planlægning af oplysninger og detaljeret vejledning.

## <a name="step-6-publish-apps"></a>Trin 6: Publicere apps ##

En Azure RemoteApp app er den app eller det program, du angiver, at dine brugere. Det er placeret i den skabelonbillede, du har overført til samlingen. Når en bruger har adgang til en app, det ser ud til at køre i deres lokale miljø, men det kører virkelig i Azure.

Før dine brugere kan få adgang til apps, skal du publicere dem – dette betyder, at dine brugere få adgang til dem via klienten Fjernskrivebord.

Du kan udgive flere apps til din samling. Klik på **Publicer** for at tilføje en app fra siden publicering. Du kan enten udgive fra menuen **Start** af billedet, skabelon eller ved at angive stien på skabelonbillede for appen. Hvis du vil tilføje fra menuen **Start** , vælg programmet, der skal tilføje. Hvis du vælger at angive en sti til app, kan du angive et navn til appen og stien til hvor den er installeret på skabelon billedet.

## <a name="step-7-configure-user-access"></a>Trin 7: Konfigurere brugeradgang ##

Nu hvor du har oprettet din samling, skal du føje de brugere, du vil kunne bruge dine eksterne ressourcer. De brugere, du kan give dig adgang for at kunne skal findes i Active Directory-lejeren, der er knyttet til abonnementet, du brugte til at oprette denne Azure RemoteApp af websteder.

1.  Klik på **Konfigurer brugeradgang**fra siden Hurtig Start.
2.  Angiv arbejdskonto (fra Active Directory) eller Microsoft-konto, du vil give adgang til.

    **Noter:**

    Sørg for, at du bruger den “user@domain.com” format.

    Hvis du bruger Office 365 ProPlus i af websteder, skal du bruge Active Directory-identiteter til dine brugere. Dette hjælper med at validere licenser.


3.  Klik på **Gem**, når brugerne er godkendt.


## <a name="next-steps"></a>Næste trin ##
Det var det – du har oprettet og installeret din Azure RemoteApp hybrid samling. Næste trin er at få dine brugere til at hente og installere klienten Fjernskrivebord. Du kan finde URL-adressen for-klienten på siden Azure RemoteApp Hurtig Start. Derefter har brugerne skal logge på klienten og få adgang til de apps, du har publiceret.



### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** for at foretage ændringer – dem, der kommer til os til gennemsyn, og klik derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.
