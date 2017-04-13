<properties
   pageTitle="Identitet administration til multiprofiler programmer | Microsoft Azure"
   description="Bedste fremgangsmåder for administration af godkendelse, autorisation og identitet i multiprofiler apps."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Identitet administration til multiprofiler programmer i Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Denne serie beskriver bedste fremgangsmåder for multitenancy, når du bruger Azure AD til godkendelse og identitet.

Når du opretter en multiprofiler programmet, en af de første udfordringer administrere bruger-id'er, fordi nu hver bruger hører til en lejer. Eksempel:

- Brugere logge på med deres egne legitimationsoplysninger.
- Brugere skal have adgang til deres organisations dataene, men ikke data, der hører til andre lejere.
- En organisation kan tilmelde dig programmet, og derefter tildele programmet roller for dets medlemmer.

Azure Active Directory (Azure AD) har nogle fantastiske funktioner, der understøtter alle af følgende scenarier.

Hvis du vil følge denne serie af artikler, vi også oprettet en komplet, [Start til slut implementering] [ tailspin] af en multiprofiler app. Artiklerne afspejler det, vi har lært i gang med at opbygge programmet. For at komme i gang med programmet, skal du se [køre programmet undersøgelser](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Her er listen over artikler i denne serie:

- [Introduktion til administration af identitet til multiprofiler programmer](guidance-multitenant-identity-intro.md)
- [Om programmet Hold undersøgelser](guidance-multitenant-identity-tailspin.md)
- [Godkendelse ved hjælp af Azure AD og OpenID forbinde](guidance-multitenant-identity-authenticate.md)
- [Arbejde med Kræv-baserede identiteter](guidance-multitenant-identity-claims.md)
- [Tilmelding og lejer onboarding](guidance-multitenant-identity-signup.md)
- [Programmet roller](guidance-multitenant-identity-app-roles.md)
- [Rollebaseret og ressource-baseret godkendelse](guidance-multitenant-identity-authorize.md)
- [Sikring af en back end-web API](guidance-multitenant-identity-web-api.md)
- [Cachelagring OAuth2 access tokens](guidance-multitenant-identity-token-cache.md)
- [En sammenslutning til en kundes AD FS til multiprofiler apps i Azure](guidance-multitenant-identity-adfs.md)
- [Brug af klienten program til at få adgangstokens fra Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Bruger nøgle samling af legitimationsoplysninger til at beskytte programmet hemmeligheder](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
