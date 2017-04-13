<properties
    pageTitle="Oversigt over brugerdefinerede domænenavne i Azure Active Directory | Microsoft Azure"
    description="Beskriver en grundlæggende ramme for brug af brugerdefinerede domænenavne i Azure Active directory, herunder sammenslutning til single sign-on"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Oversigt over brugerdefinerede domænenavne i Azure Active Directory

Et domænenavn er en vigtig del af et id for mange directory-ressourcer: det er en del af en bruger navnet eller mailadressen for en bruger, en del af adressen for en gruppe, og kan være en del af ID URI-app til et program. En ressource i Azure Active Directory (Azure AD) kan indeholde et domænenavn, der allerede er godkendt til ejes af den mappe, hvor ressourcen. En global administrator kan udføre domæne administrationsopgaver i Azure AD.

Domænenavne i Azure AD er globalt entydige. Et domænenavn, kan anvendes af en enkelt Azure AD. Hvis et Azure AD-bibliotek har bekræftet et domænenavn, kan derefter ingen andre Azure AD-mappe bekræfte eller bruge den samme domænenavn.

## <a name="initial-and-custom-domain-names"></a>Indledende og brugerdefinerede domænenavne

Hver domænenavnet i Azure AD er enten en indledende domænenavn eller et brugerdefineret domænenavn.

Hver Azure AD leveres med et indledende domænenavn i formular contoso.onmicrosoft.com. Denne tredje niveau domænenavn, i dette eksempel "contoso.onmicrosoft.com", der blev oprettet, når mappen er blevet oprettet, typisk af administratoren der har oprettet kataloget. Det oprindelige domænenavn til en mappe kan ikke ændres eller slettes. Det oprindelige domænenavn, henvender mens fuldt funktionelle sig primært skal bruges som en metode til selvstart af et, før et brugerdefineret domænenavn er bekræftet.

I de fleste fremstilling miljøer, et katalog har mindst én bekræftet brugerdefineret domæne, som "contoso.com", og det er det brugerdefinerede domæne, der er synlige for slutbrugere. Et brugerdefineret domænenavn er et domænenavn, der er ejet og bruges af den pågældende organisation, som "contoso.com," til brug som vært for webstedet. Denne domænenavnet er velkendt for medarbejdere, fordi det er en del af det brugernavn, som de kan bruge til at logge på virksomhedens netværk, eller til at sende og hente mail.

Før den kan bruges af Azure AD, skal det brugerdefinerede domænenavn føjes til adresselisten og bekræftet.

## <a name="verified-and-unverified-domain-names"></a>Bekræftet og ikke-bekræftede domænenavne

Det oprindelige domænenavn til en mappe evalueres implicit som godkendt af Azure AD. Når en administrator føjer et brugerdefineret domænenavn til en Azure AD, er den som udgangspunkt i en ikke-bekræftede tilstand. Azure AD tillader ikke nogen directory ressourcer til at bruge en ikke-bekræftede domænenavn. Dette sikrer, at kun én mappe kan bruge et bestemt domænenavn, og, at organisationen bruger domænenavnet rent faktisk ejer pågældende domænenavn.

Azure AD bekræfter ejerskab af et domænenavn ved at søge efter en bestemt post i filen domain name service (DNS) zone for domænenavnet. For at bekræfte ejerskabet af et domænenavn, henter administrator DNS-posten fra Azure AD, Azure AD søger efter, og føjer den pågældende post til DNS zone file for domænenavnet. DNS zone file vedligeholdes af domæneregistrator for det pågældende domæne. Disse trin for at bekræfte et domæne er vist i artiklen for [at tilføje et brugerdefineret domæne til din Azure AD-mappe](active-directory-add-domain.md).

Tilføjelse af en DNS-post til filen zone for domænenavnet påvirker ikke andre domænetjenester som mail eller internettet vært.

## <a name="federated-and-managed-domain-names"></a>Samlet og administrerede domænenavne

Et brugerdefineret domænenavn i Azure AD kan konfigureres til at give brugerne en samlet logge i mellem dit lokale Active Directory og Azure AD-versionen. Konfigurere et domæne til sammenslutning kræver opdateringer til privilegerede ressourcer i Azure AD og også på din Windows Server Active Directory. Konfiguration af et organisationsnetværk domæne skal være fuldført, fra Azure AD-forbindelse eller ved hjælp af PowerShell. Sammenkæde et brugerdefineret domæne kan ikke startes fra Azure klassisk portalen. [Se denne video for at få mere at vide om at konfigurere AD FS for bruger logge på med Azure AD-forbindelse](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domæner, der ikke er er i organisationsnetværk kaldes nogle gange administrerede domæner. Det indledende domæne til et Azure AD-directory evalueres implicit som et administreret domæne.

## <a name="primary-domain-names"></a>Primære domænenavne

Det primære domænenavn til en mappe er navnet på det domæne, der er allerede markeret som standardværdi for den 'domæne' del af brugernavnet, når en administrator opretter en ny bruger i [Azure klassisk portal](https://manage.windowsazure.com/) eller en anden portal som Office 365-administrationsportalen. En mappe kan kun have ét primært domænenavn. En administrator kan ændre det primære domænenavn er et bekræftet brugerdefineret domæne, der ikke er i organisationsnetværk, eller til det indledende domæne.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Domænenavne i Azure AD og andre Microsoft Online Services

Et domænenavn skal kontrolleres i Azure AD før den kan bruges af en anden Microsoft-onlinetjeneste som Exchange Online, SharePoint Online og Intune. Disse andre tjenester kræver typisk en administrator til at tilføje en eller flere DNS-poster, der er specifikke for tjenesten.

En Azure-WebApp bruger sit eget system til at bekræfte ejerskab for et domæne. Et domæne skal kontrolleres til brug sammen med Azure AD, selvom den er blevet tidligere har kontrolleret til brug af en Azure WebApp i et abonnement, der er afhænger af den Azure AD. En Azure-WebApp kan bruge et domænenavn, der er blevet bekræftet i en anden mappe fra den mappe, der sikrer WebApp.

## <a name="managing-domain-names"></a>Administrere domænenavne

Domæne administrationsopgaver kan gennemføres, fra Azure klassisk portalen og fra PowerShell. Mange opgaver kan udføres ved hjælp af Azure AD Graph API (i offentlige preview).

-   [Tilføje og bekræfte et brugerdefineret domænenavn](active-directory-add-domain.md)

-   [Administrere domæner i portalen Azure klassisk](active-directory-add-manage-domain-names.md)

-   [Bruge PowerShell til at administrere domænenavne i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Brug af Azure AD Graph-API til at administrere domænenavne i Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
