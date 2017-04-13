<properties
    pageTitle="Svagheder, der er registreret af Azure Active Directory identitetsbeskyttelse | Microsoft Azure"
    description="Oversigt over de svagheder, der er registreret af Azure Active Directory identitetsbeskyttelse."
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, skyen app registrering, administration af programmer, sikkerhed, risikoen, risikoen niveau, sikkerhedsrisiko, sikkerhedspolitik"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Svagheder, der er registreret af Azure Active Directory identitetsbeskyttelse 

Svagheder er svagheder i dit miljø, som en hacker kan udnytter. Vi anbefaler, at du løser disse svagheder for at forbedre sikkerheden stilling i din organisation, og forhindre hackere i at udnytte dem.
<br><br>
![svagheder](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

De følgende afsnit giver dig et overblik over de svagheder, der rapporteres af beskyttelse af din identitet.

## <a name="multi-factor-authentication-registration-not-configured"></a>Multi-Factor authentication registrering ikke konfigureret 

Sikkerhedsrisikoen hjælper dig med at styre installationen af Azure Multi-Factor Authentication i din organisation. 

Azure Multi-Factor godkendelse giver en anden sikkerhedslag til brugergodkendelse. Det hjælper med at beskytte adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det leverer stærke godkendelse via et udvalg af nemt bekræftelse – telefonopkald, SMS-besked eller mobilapp besked eller bekræftelse kode og 3 part ed tokens.

Vi anbefaler, at du skal bruge Azure Multi-Factor Authentication til bruger logon. Multi-Factor authentication afspilles en vigtig rolle i risikoen-baserede betinget access politikker, der er tilgængelige via beskyttelse af din identitet.

Yderligere oplysninger finder du [Hvad er Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Ikke-administreret skyen apps

Sikkerhedsrisikoen hjælper dig med at identificere ikke-administreret skyen apps i din organisation.
 
IT-afdelinger ikke i moderne virksomheder ofte bekendt med alle de cloud-programmer, brugerne i organisationen bruger til at udføre deres arbejde. Det er nemt at se, hvorfor Administratorer vil er i tvivl om uautoriseret adgang til virksomhedsdata, uautoriseret videregivelse mulige data og andre sikkerhedsrisici. 

Vi anbefaler, at din organisation anvender skyen App registrering at finde ikke-administreret skyen programmer samt til at administrere disse programmer ved hjælp af Azure Active Directory.

Få mere at vide i [søge efter ikke-administreret skyen programmer med skyen App registrering](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Sikkerhedsadvarsler fra administration af privilegerede identitet

Sikkerhedsrisikoen hjælper dig med at finde og løse beskeder om privilegerede identiteter i din organisation.  

Hvis du vil aktivere brugere til at udføre privilegerede handlinger, virksomheder har brug for til at give brugere midlertidig eller permanent privilegerede adgang i Azure AD Azure- eller Office 365 ressourcer eller andre SaaS apps. Hver af disse privilegerede brugere øges angrebsoverfladen af din organisation. Sikkerhedsrisikoen hjælper dig med at identificere brugere med unødvendige privilegerede access, og tag nødvendige skridt for at reducere eller fjerne de dog udgøre risikoen. 

Vi anbefaler, at din organisation bruger Azure AD privilegerede identitet administration til at administrere, kontrol og overvåge følsomme identiteter og deres adgang til ressourcer i Azure AD samt andre Microsoft online services som Office 365 eller Microsoft Intune.

Få mere at vide i [Azure AD privilegerede identitet i administration](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Se også

 - [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md)
