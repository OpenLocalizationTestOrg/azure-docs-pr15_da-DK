<properties
    pageTitle="Forudsætninger for at få adgang til den Azure AD rapportering API. | Microsoft Azure"
    description="Få mere at vide om forudsætninger for at få adgang til den Azure AD API-rapportering"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Forudsætninger for at få adgang til den Azure AD API-rapportering 

[Azure AD rapportering API'er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) giver dig programmeringsmæssig adgang til data via et sæt RESTEN-baserede API'er. Du kan ringe til disse API'er fra en række programmeringssprog og værktøjer.

Rapportering API bruger [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) til at tillade adgang til internettet API'er. 

For at forberede din adgang til rapportering API, skal du:

1. Oprette et program i din Azure AD-lejer 

2. Give programmet relevante tilladelser til at få adgang til Azure AD-data

3. Indsamle indstillinger for søgekonfiguration fra adresselisten

For spørgsmål, problemer eller feedback, skal du kontakte [AAD rapportering Hjælp](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Oprette et Azure AD-program

For at konfigurere dit bibliotek for at få adgang til den Azure AD rapportering API, skal du logge på med en administratorkonto til Azure-abonnement, der også er medlem af den globale administratorrolle til mappe i din Azure AD-lejer Azure klassisk portalen.

> [AZURE.IMPORTANT] Programmer, der kører under legitimationsoplysninger med "administratortilladelser" således kan være meget effektiv, så sørg for at sikre det program-ID/hemmeligt legitimationsoplysninger.


1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Vælg mappen fra listen **active directory** .

3. Klik på **programmer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Klik på **Tilføj**på den nederste linje.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/03.png) 

5. På den **Hvad vil du gøre?** dialogboksen, klik på **Tilføj et program, udvikling af min organisation**. 

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/04.png) 


6. I dialogboksen **fortælle os om dit program** skal du udføre følgende trin: 

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/05.png) 

    en. Skriv et navn i tekstfeltet **navn** (f.eks.: rapportering API program).

    b. Vælg **webprogram og/eller web API**.

    c. Klik på **Næste**.


7. I dialogboksen **Egenskaber for App** skal du udføre følgende trin: 

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/06.png) 

    en. I tekstfeltet **Sign-on URL-adresse** skal du skrive `https://localhost`.

    b. Skriv i tekstfeltet **App ID URI** ```https://localhost/ReportingApiApp```.

    c. Klik på **udført**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Give dit program tilladelse til at bruge API

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com/), i den venstre navigationsrude.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Vælg mappen fra listen **active directory** .

3. Klik på **programmer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/02.png)


3. Vælg dit nyoprettede program i listen over programmer.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/07.png)

4. Klik på **Konfigurer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/08.png)


5. Klik på rullelisten **Tilladelser for tjenesteprogram** i sektionen **tilladelser til andre programmer** for ressourcen **Azure Active Directory** , og vælg derefter **læse directory data**.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/09.png)


5. Klik på **Gem**på den nederste linje.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Indsamle indstillinger for søgekonfiguration fra adresselisten

Dette afsnit beskrives, hvordan du får følgende indstillinger fra mappen:

- Domænenavn
- Klient-ID
- Klient hemmeligt

Du har brug for disse værdier, når du konfigurerer opkald til rapportering API. 


### <a name="get-your-domain-name"></a>Få dit domænenavn

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Vælg mappen fra listen **active directory** .

3. Klik på **domæner**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/11.png) 

4. I kolonnen **Domain Name** skal du kopiere dit domænenavn.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Få programmets klient-ID

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Vælg mappen fra listen **active directory** .

3. Klik på **programmer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Vælg dit nyoprettede program i listen over programmer.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/07.png)

4. Klik på **Konfigurer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/08.png)

4. Kopier din **klient-ID**.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Få programmets klient hemmeligt

For at få dit program klient hemmeligt, skal du oprette en ny nøgle og gemme dens værdi ved at gemme den nye nøgle, fordi det ikke er muligt at hente denne værdi senere længere.

1. Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Vælg mappen fra listen **active directory** .

3. Klik på **programmer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Vælg dit nyoprettede program i listen over programmer.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/07.png)

4. Klik på **Konfigurer**i menuen øverst.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/08.png)

5. I sektionen **nøgler** skal du udføre følgende trin: 

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/14.png)

    en. Vælg en varighed på listen varighed

    b. Klik på **Gem**på den nederste linje.

    ![Registrering af program](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Kopiér den nøgleværdi.

## <a name="next-steps"></a>Næste trin

- Vil du få adgang til dataene fra den Azure AD rapportering API i en programmeringsmæssig måde? Se [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md).

- Hvis du gerne vil vide mere om rapportering af Azure Active Directory, kan du se [Azure Active Directory rapportering Guide](active-directory-reporting-guide.md).  
