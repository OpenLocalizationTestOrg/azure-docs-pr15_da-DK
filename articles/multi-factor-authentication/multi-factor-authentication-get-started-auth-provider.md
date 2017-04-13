<properties
    pageTitle="Komme i gang Azure Multi-Factor Auth udbyder | Microsoft Azure"
    description="Lær, hvordan du opretter en Azure Multi-Factor Auth-udbyder."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introduktion til en udbyder af Azure Multi-Factor Auth
Totrinsbekræftelse er som standard tilgængelig for globale administratorer, der har Azure Active Directory og Office 365-brugere. Men hvis du ønsker at drage fordel af [Avancerede funktioner](multi-factor-authentication-whats-next.md) derefter du skal købe den fulde version af Azure Multi-Factor Authentication (MFA).

> [AZURE.NOTE]  En udbyder af Azure Multi-Factor Auth bruges til at drage fordel af funktioner i den fulde version af Azure MFA. Det er for brugere, der **ikke har licenser via Azure MFA, Azure AD Premium eller EMS**.  Azure MFA, Azure AD Premium og EMS Medtag den fulde version af Azure MFA som standard.  Hvis du har licenser, behøver du ikke en Azure Multi-Factor Auth-udbyder.

En udbyder af Azure Multi-Factor Auth kræves for at hente SDK.

> [AZURE.IMPORTANT]  Oprette en Azure Multi-Factor Auth-udbyder for at hente SDK, selvom du har Azure MFA, AAD Premium eller EMS licenser.  Hvis du opretter en udbyder af Azure Multi-Factor Auth hertil og allerede har licenser, skal du oprette provideren med **Hver aktiveret bruger** modellen. Derefter link provideren til den mappe, der indeholder Azure MFA, Azure AD Premium eller EMS licenserne.  Dette sikrer, at du kun er faktureret Hvis du har flere forskellige brugere ved hjælp af SDK end antallet licenser, at du ejer.


## <a name="to-create-a-multi-factor-auth-provider"></a>Oprette en multi-Factor Auth udbyder

Brug følgende trin til at oprette en Azure Multi-Factor Auth-udbyder.

1. Log på [Azure klassisk portalen](https://manage.windowsazure.com) som administrator.
2. Vælg **Active Directory**i venstre side.
3. På siden Active Directory øverst skal du vælge **Multi-Factor godkendelsesprovidere**.
![Oprette en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Klik på **Ny**nederst skal.
![Oprette en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Under App tjenester, skal du vælge **Multi-Factor Auth udbyder**
![oprettelse af en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Vælg **Hurtig oprettelse**.
![Oprette en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Udfyld følgende felter, og vælg **Opret**.
    1. **Navn** – navnet på den Multi-Factor Auth udbyder.
    2. **Brugen Model** – om du vil aktivere individuelle brugere eller betale hver bekræftelse. Vælg en af to muligheder:
        - Per godkendelse – køb model, gebyrer per godkendelse. Typisk bruges til at scenarier, der bruger Azure Multi-Factor Authentication i et consumer adgang til computeren.
        - Per aktiveret bruger – aktiveret indkøb model, gebyrer per bruger. Typisk bruges til at medarbejderadgang til programmer som Office 365. Vælg denne indstilling, hvis du har nogle brugere, der er allerede licens til Azure MFA.
    2. **Directory** -feltet Azure Active Directory-lejer provideren Multi-Factor Authentication er knyttet til. Vær opmærksom på følgende:
        - Du behøver ikke en Azure AD-mappe til at oprette en multi-Factor Auth udbyder. Lad feltet være tomt, hvis du kun planlægger at bruge Azure Multi-Factor Authentication serveren eller SDK.
        - Provideren Multi-Factor Auth skal være knyttet til et Azure AD directory at drage fordel af de avancerede funktioner.
        - Azure AD-forbindelse, AAD Sync eller DirSync er kun et krav, hvis du synkroniserer dit lokale Active Directory-miljø med en Azure AD-mappe.  Hvis du kun bruge en Azure AD-mappe, der ikke er synkroniseret, og derefter dette ikke er påkrævet.
![Oprette en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Når du klikker på Opret, provideren Multi-Factor Authentication oprettes, og der vises en meddelelse om: **blev oprettet Multi-Factor Authentication udbyder**. Klik på **Ok**.
![Oprette en udbyder af MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
