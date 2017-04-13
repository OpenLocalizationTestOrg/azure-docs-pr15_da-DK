<properties
   pageTitle="Csv-filformat til Azure Active Directory B2B samarbejde preview | Microsoft Azure"
   description="Azure Active Directory-B2B understøtter din tværs af firmaer relationer ved at aktivere forretningsforbindelser selektivt adgang til din virksomhedens programmer"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Azure AD B2B samarbejde preview: CSV-filformat

Den foreløbige version af Azure AD B2B samarbejde kræver en CSV-fil, der angiver partner brugeroplysninger overføres via Azure AD-portalen. Csv-filen skal indeholde de nødvendige etiketter nedenfor og valgfrie felter efter behov. Ændre csv-eksempelfilen (nedenfor) uden at ændre stavekontrol af etiketterne i den første række.

>[AZURE.NOTE] Den første række af navne (såsom mail, vist navn og osv.) er nødvendig for csv-fil, der er blevet fortolket. Stavekontrol skal svarer til de felter, der er angivet nedenfor. Disse navne identificere indholdet nedenunder. For valgfrie felter, der ikke er nødvendige, fjernes deres etiketter fra csv-filen. Hele kolonnen kan være tomt.

## <a name="required-fields-br"></a>Obligatoriske felter: <br/>
**Mail:** E-mail-adressen på den inviterede bruger. <br/>
**Vist navn:** Vist navn for den inviterede bruger (typisk første og sidste navn).<br/>


## <a name="optional-fields-br"></a>Valgfrie felter: <br/>

**InvitationText:** Tilpasse invitation mail tekst efter app branding og før linket indløsningskurs.<br/>
**InvitedToApplications:** Nedenstående handlingsværdier til virksomhedens programmer til at tildele brugere. Nedenstående handlingsværdier er størrelse i PowerShell ved at ringe til`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:** ObjectIDs for grupper at føje brugeren til. ObjectIDs er størrelse i PowerShell ved at ringe til`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:** URL-adressen til at dirigere en inviterede bruger efter Inviter accept. Dette skal være en virksomhedens URL-adresse (såsom [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Hvis denne valgfrit felt ikke er angivet, er den inviterede bruger ført til panelet App adgang, hvor vedkommende kan navigere til dine valgte virksomhedens apps. Webadressen til App Access Panel er i formularen `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: e-mail-adresse til at kopiere sendt invitationen. Hvis feltet CcEmailAddress bruges, kan denne invitation ikke bruges til mail-godkendt bruger eller oprettelse af lejer.<br/>
**Sprog:** Sprog for invitation via mail og indløsningskurs oplevelse med "da" (på engelsk) som standard, når du ikke er angivet. De andre 10 understøttede sprog koder er:<br/>
1. de: tysk<br/>
2. ES: spansk<br/>
3. fransk: fransk<br/>
4. det: italiensk<br/>
5. Ja: japansk<br/>
6. ko: koreansk<br/>
7. pt-BR: portugisisk (Brasilien)<br/>
8. RU: russisk<br/>
9. zh HANS: forenklet kinesisk<br/>
10. zh HANT: kinesisk (traditionelt)<br/>

## <a name="sample-csv-file"></a>Csv-eksempelfilen
Her er et eksempel kan du ændre CSV.

>[AZURE.NOTE] Kopiere og sætte det ind i Notesblok og gemme den med filtypenavnet '.csv'. Derefter Rediger i Excel. Det kan være struktureret til en tabel med etiketter i den første række.

> Tilføje flere valgfrie felter i Excel ved at angive navnet og udfylde kolonnen nedenunder.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Relaterede artikler
Gennemse vores andre artikler på Azure AD B2B samarbejde

- [Hvad er Azure AD B2B samarbejde?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Sådan fungerer det](active-directory-b2b-how-it-works.md)
- [Detaljeret gennemgang](active-directory-b2b-detailed-walkthrough.md)
- [Eksterne brugere token format](active-directory-b2b-references-external-user-token-format.md)
- [Eksterne brugere objekt attributændringer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle preview-begrænsninger](active-directory-b2b-current-preview-limitations.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
