<properties
   pageTitle="Godkende med datalager sø ved hjælp af Active Directory | Microsoft Azure"
   description="Lær, hvordan du vil godkende med datalager sø ved hjælp af Active Directory"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Slutbrugere godkendelse med datalager sø ved hjælp af Azure Active Directory

> [AZURE.SELECTOR]
- [Tjeneste til godkendelse](data-lake-store-authenticate-using-active-directory.md)
- [Slutbrugere godkendelse](data-lake-store-end-user-authenticate-using-active-directory.md)


Azure datalager sø bruger Azure Active Directory for godkendelse. Før du opretter et program, der fungerer med Azure datalager sø eller Azure Data sø Analytics, skal du først beslutte, hvordan du vil have til at godkende dit program med Azure Active Directory (Azure AD). De to primære indstillinger er:

* Slutbrugere godkendelse, og 
* Tjeneste til godkendelse. 

Begge disse indstillinger resulterer i dit program, der leveres med token OAuth 2.0, som bliver knyttet til hver anmodninger om at Azure datalager sø eller Azure Data sø analyser.

I denne artikel taler om, hvordan oprette et Azure AD-webprogram til slutbrugere godkendelse. Finde en vejledning til Azure AD programkonfiguration tjeneste til godkendelse af se [- tjenester godkendelse med sø datalager ved hjælp af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Forudsætninger

* Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
* Dit abonnement-ID. Du kan hente det fra Azure-portalen. For eksempel er den tilgængelig fra bladet sø datalager konto.

    ![Få abonnement-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Domænenavnet Azure AD. Du kan hente den ved at holde musen musen i øverste højre hjørne af portalen Azure. Domænenavnet er **contoso.microsoft.com**fra skærmbilledet nedenfor, og GUID kantede parenteser er lejer-ID. 

    ![Få AAD domæne](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Slutbrugere godkendelse

Dette er den anbefalede fremgangsmåde, hvis du vil have en slutbruger til at logge på dit program via Azure AD. Dit program vil kunne få adgang til Azure ressourcer med det samme adgangsniveau som slutbruger, der er logget på. Dine slutbrugere skal angive deres legitimationsoplysninger med jævne mellemrum i rækkefølge for dit program til at vedligeholde adgang.

Resultatet af har slutbrugeren log på, er det, dit program er givet et adgangstoken og et Opdater token. Adgangstoken bliver knyttet til hver anmodninger om at sø datalager eller Data sø Analytics, og det er gyldige for en time som standard. Opdater tokenet kan bruges til at få en ny adgangstoken, og det er relevant for op til to uger som standard, hvis bruges jævnligt. Du kan bruge to forskellige metoder til at slutbrugerlicensaftale logge ind.

### <a name="using-the-oauth-20-pop-up"></a>Brug af pop op-vinduet OAuth 2.0

Programmet kan udløse en OAuth 2.0 godkendelse pop op-vindue, hvor slutbrugeren kan indtaste deres legitimationsoplysninger. Denne pop op-menuen fungerer også med processen Azure AD-to-faktor-godkendelse (2FA), hvis det er nødvendigt. 

>[AZURE.NOTE] Denne metode er endnu ikke understøttet i Azure AD godkendelse bibliotek (ADAL) for Python eller Java.

### <a name="directly-passing-in-user-credentials"></a>Overføre direkte i brugerlegitimationsoplysninger

Programmet kan direkte levere brugerlegitimationsoplysninger til Azure AD. Denne metode fungerer kun med organisatoriske ID-brugerkonti; det er ikke kompatibel med personal / "live-ID" brugerkonti, herunder dem, der ender med @outlook.com eller @live.com. Desuden er denne metode ikke kompatibel med brugerkonti, der kræver Azure AD-to-faktor-godkendelse (2FA).

### <a name="what-do-i-need-to-use-this-approach"></a>Hvad skal jeg bruge denne metode?

* Azure AD-domænenavn. Dette er allerede vises i påkrævede i denne artikel.

* Azure AD **-webprogrammet**

* Klient-ID for Azure AD-webprogrammet

* Svar URI for Azure AD-webprogrammet

* Angive delegerede tilladelser

Vejledning i at oprette en Azure AD-webprogrammet og konfigurere det til de krav, der er anført ovenfor, i afsnittet [oprette en Active Directory-program](#create-an-active-directory-application) nedenfor. 

## <a name="create-an-active-directory-application"></a>Oprette en Active Directory-program

I dette afsnit vi få mere at vide om, hvordan du konfigurerer et Azure AD-webprogram til slutbrugere godkendelse med Azure datalager sø ved hjælp af Azure Active Directory.


### <a name="step-1-create-an-azure-active-directory-application"></a>Trin 1: Oprette et Azure Active Directory-program

>[AZURE.NOTE] Nedenstående trin bruge Azure-portalen. Du kan også oprette et Azure AD-program ved hjælp af [Azure PowerShell](../resource-group-authenticate-service-principal.md) eller [Azure CLI](../resource-group-authenticate-service-principal-cli.md).

1. Log på kontoen Azure via [Klassisk portal](https://manage.windowsazure.com/).

2. Vælg **Active Directory** i venstre rude.

     ![Vælg Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
     
3. Vælg Active Directory, du vil bruge til at oprette det nye program. Hvis du har mere end én Active Directory, vil du som regel oprette programmet i den mappe, hvor dit abonnement er placeret. Du kan kun give adgang til ressourcen i dit abonnement på programmer i den samme mappe som dit abonnement.  

     ![Vælg mappe](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
    
    
3. For at få vist programmerne i mappen, klik på **programmer**.

     ![få vist programmer](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)

4. Hvis du ikke har oprettet et program i mappen før skal du se afsnittet noget svarende til følgende billede. Klik på **Tilføj et program**

     ![tilføje program](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)

     Eller klik på **Tilføj** i den nederste rude.

     ![tilføje](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)

6. Angiv et navn til programmet, og vælg typen program, du vil oprette. Oprette en **WEB APPLICATION og/eller WEB API** selvstudiet, og klik på knappen Næste.

     ![navn program](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)

7. Udfyld egenskaberne for din app. **Log på URL-adressen**til at give URI til et websted, der beskriver dit program. Eksistensen af webstedet valideres ikke. Angiv den URI, der identificerer dit program til **APP ID URI**.

     ![Egenskaber for tjenesteprogram](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)

    Klik på afkrydsningsfeltet for at fuldføre guiden og oprette programmet.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Trin 2: Få klient-id, svar til URI og angive delegeret tilladelser

1. Klik på fanen **Konfigurer** for at konfigurere dit program adgangskode.

     ![konfigurere program](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)

2. Kopiere **klient-ID**.
  
     ![klient-id](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)

3. Kopiere **Svar URI**under afsnittet **Single sign-on** .

    ![klient-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)

4. Klik på **Tilføj program** under **tilladelser til andre programmer**

    ![klient-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

5. Vælg **Azure Data sø** og **Windows** **Azure Service Management API**guiden **tilladelser til andre programmer** , og klik på markeringen.

6. **Delegerede tilladelser** for de nyligt tilføjede tjenester er som standard angivet til nul. Klik på **Delegerede tilladelser** ned til Azure Data sø og Windows Azure Management-tjenesten, og Vælg tilgængelige afkrydsningsfelterne for at angive værdier til 1. Resultatet ser sådan ud.

     ![klient-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

7. Klik på **Gem**.


## <a name="next-steps"></a>Næste trin

Du har oprettet en Azure AD-webprogrammet og indsamlet de oplysninger, du har brug for i din klientprogrammer, du opretter ved hjælp af .NET SDK, Java SDK osv i denne artikel. Nu kan du fortsætte til de følgende artikler, der taler om, hvordan du bruger Azure AD-webprogrammet til at godkende først med sø datalager og derefter udføre andre handlinger på store.

- [Introduktion til Azure Data sø Store ved hjælp af .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Introduktion til Azure Data sø Store ved hjælp af Java SDK](data-lake-store-get-started-java-sdk.md)
