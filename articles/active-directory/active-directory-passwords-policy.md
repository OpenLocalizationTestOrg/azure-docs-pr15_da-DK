<properties
    pageTitle="Adgangskodepolitikker og begrænsninger i Azure Active Directory | Microsoft Azure"
    description="I denne artikel beskrives de politikker, der gælder for adgangskoder i Azure Active Directory, herunder tilladte tegn, længde og udløb"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Adgangskodepolitikker og begrænsninger i Azure Active Directory

I denne artikel beskrives adgangskodepolitikker og kompleksitetskrav, der er knyttet til brugerkonti, der er gemt i Azure AD-biblioteket.

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName politikker, der gælder for alle brugerkonti

Hver brugerkonto, der skal til at logge på Azure AD-godkendelsessystem skal have en entydig bruger hovednavn (UPN) attributværdien, der er knyttet til den pågældende konto. Følgende tabel dispositioner de politikker, der gælder for både lokale Active Directory-leveres brugerkonti (synkroniseret til skyen) og til skyen kun brugerkonti.

|   Egenskaben           |     UserPrincipalName krav  |
|   ----------------------- |   ----------------------- |
|  Tilladte tegn    |  <ul> <li>A-Å</li> <li>a - å </li><li>0-9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Tegn, der er ikke tilladt  | <ul> <li>En hvilken som helst '@' tegn, der ikke adskillelse brugernavnet fra domænet.</li> <li>Må ikke indeholde et periode tegn '.' umiddelbart før den '@' symbol</li></ul> |
| Længde begrænsninger  |       <ul> <li>Samlede længde må ikke overskride 113 tegn</li><li>64 tegn foran den ‘@’ symbol</li><li>48 tegn efter den ‘@’ symbol</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Adgangskodepolitikker, gælder kun for skyen brugerkonti

I følgende tabel beskrives de indstillinger for tilgængelige adgangskodepolitik, der kan anvendes på brugerkonti, der oprettes og administreres i Azure AD.

|  Egenskaben       |    Krav          |
|   ----------------------- |   ----------------------- |
|  Tilladte tegn   |   <ul><li>A-Å</li><li>a - å </li><li>0-9</li> <li>@# $ % ^ & * - _ ! + = [] {} & #124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Tegn, der er ikke tilladt   |       <ul><li>Unicode-tegn</li><li>Mellemrum</li><li> **Stærke adgangskoder**: må ikke indeholde et prik tegn '.' umiddelbart før den '@' symbol</li></ul> |
|   Adgangskodebegrænsninger | <ul><li>8 tegn mindste og 16 tegn maksimale</li><li>**Stærke adgangskoder**: kræver 3 af 4 af følgende:<ul><li>Små bogstaver</li><li>Store bogstaver</li><li>Tal (0-9)</li><li>Symboler (se adgangskodebegrænsninger ovenfor)</li></ul></li></ul> |
| Adgangskode udløbet varighed      | <ul><li>Standardværdi: **90** dage </li><li>Værdi kan konfigureres ved hjælp af cmdlet'en Set-MsolPasswordPolicy til fra Azure Active Directory-modulet til Windows PowerShell.</li></ul> |
| Adgangskode udløbet meddelelse |  <ul><li>Standardværdi: **14** dage (før adgangskoden udløber)</li><li>Værdi kan konfigureres ved hjælp af cmdlet'en Set-MsolPasswordPolicy til.</li></ul> |
| Adgangskode udløbet |  <ul><li>Standardværdi: **Falsk** dage (angiver, udløbet af adgangskoden er aktiveret) </li><li>Værdi kan konfigureres ved hjælp af cmdlet'en Set-MsolUser til individuelle brugerkonti. </li></ul> |
|  Adgangskoder  | Sidste adgangskode kan ikke bruges igen. |
|  Adgangskode historik varighed | Uendelig |
|  Konto låsning | Efter 10 mislykkes logon forsøg (forkerte adgangskode), vil brugeren være låst ude til et minut. Yderligere låser forkerte logon forsøg de brugeren for at øge varigheden. |


## <a name="next-steps"></a>Næste trin

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [Administrere dine adgangskoder, hvor som helst](active-directory-passwords.md)
* [Hvordan fungerer administration af adgangskoder](active-directory-passwords-how-it-works.md)
* [Komme i gang med adgangskode af programadministration](active-directory-passwords-getting-started.md)
* [Tilpasse administration af adgangskoder](active-directory-passwords-customize.md)
* [Bedste fremgangsmåder for adgangskoder administration](active-directory-passwords-best-practices.md)
* [Sådan henter du funktionsdygtige indsigt med adgangskode Management rapporter](active-directory-passwords-get-insights.md)
* [Ofte stillede spørgsmål om administration af adgangskoder](active-directory-passwords-faq.md)
* [Fejlfinding i forbindelse med administration af adgangskoder](active-directory-passwords-troubleshoot.md)
* [Lær mere](active-directory-passwords-learn-more.md)
