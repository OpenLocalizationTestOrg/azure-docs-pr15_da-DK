
<properties
    pageTitle="Ændre Azure Active Directory-leje i Azure RemoteApp | Microsoft Azure"
    description="Lær, hvordan du kan ændre den Azure Active Directory-lejer, der er knyttet til Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Ændre Azure Active Directory-leje i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp bruger Azure Active Directory (Azure AD) til at tillade brugeradgang. Kun Azure AD-lejer, som du kan bruge i Azure RemoteApp er den, der er knyttet til Azure abonnement. Du kan få vist det tilknyttede abonnement på siden **Indstillinger** i portalen. Se kolonnen **mappe** på fanen **abonnementer** .

> [AZURE.NOTE] For denne ændring lykkes, skal du først fjerne alle brugere fra de eksisterende Azure Active Directory fra alle Azure RemoteApp af websteder. For at gøre dette, gå til portalen Azure, gå til fanen **Azure RemoteApp** og åbne hver Azure RemoteApp af websteder. Gå til fanen **brugere** og fjerne brugere, der hører til din aktuelle Azure Active Directory-lejer. Gentag for alle eksisterende Azure RemoteApp af websteder. Uden at gøre dette, kan du kan ikke oprette eller programrettelse af websteder.

Hvis du vil bruge en anden lejer, skal du bruge disse trin til at ændre tilknytningen til dit abonnement:

1. Fjerne alle Azure AD-brugere, som du har givet adgang til Azure RemoteApp af websteder på portalen. (Se note over vejledning om, hvordan du gør dette).


2. Angive en Microsoft-konto (tidligere kaldet et Live-ID) som tjenesteadministratoren. (Ikke ved, hvis du allerede er service-administrator? Du kan finde ud af, ved at klikke på **Indstillinger -> administratorer**.) Nu er her, hvordan du ændrer, som:
    1. Klik på brugeren i øverste højre hjørne, og klik derefter på **få vist min faktura**.
    2. Klik på abonnementet. Klik derefter på den nye side, Rul ned, og klikke på **Rediger abonnementsoplysninger** i højre. (Midterste nederst til højre, hvis den type hjælper dig med at finde det.)
    3. Skriv Microsoft-kontoen for den bruger, der skal være administrator tjeneste.

3. Nu, log af portalen, og derefter logge på igen med den Microsoft-konto, du har angivet i det forrige trin.


4. Klik på **Ny -> App Services -> Active Directory -> Directory -> brugerdefineret oprette**.
5. Vælg **Brug eksisterende bibliotek**under **Directory**. Vi at skulle logge Du af portalen nu, så Vælg **jeg er klar til at være logget af nu**.
6. Logge på igen til portalen som global administrator på den mappe, du vil tilføje. (Hvis du ikke allerede global administrator, du vil være efter en runde af logge på og log derefter ud.)
7. Du bliver bedt om når du logger på hvis du vil bruge din eksisterende AD-lejer med dit abonnement. Klik på **Fortsæt**, og klik derefter på **Log af nu**.
5. Logge på igen, og gå tilbage til **Indstillinger -> abonnementer**. Vælg dit abonnement, og klik derefter på **Rediger Directory**. Vælg den Azure AD-lejer, som du vil bruge.



Du kan nu bruge de nye Azure AD-lejer til at styre adgangen til Azure abonnementet og konfigurere brugeradgang i Azure RemoteApp.
