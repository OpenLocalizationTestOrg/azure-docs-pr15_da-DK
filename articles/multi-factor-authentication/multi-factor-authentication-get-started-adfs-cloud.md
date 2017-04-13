<properties
    pageTitle="Secure skyen ressourcer med Azure MFA og AD FS"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA og AD FS i skyen."
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

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Sikring af skyen ressourcer med Azure Multi-Factor Authentication og AD FS

Hvis din organisation er knyttet til Azure Active Directory, kan du bruge Azure Multi-Factor Authentication eller Active Directory Federation Services til at sikre ressourcer, som Azure AD adgang til. Benyt følgende fremgangsmåde til at sikre Azure Active Directory-ressourcer med Azure Multi-Factor Authentication eller Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Secure Azure AD ressourcer ved hjælp af AD FS

For at sikre dine skyen ressource, skal du først aktivere en konto for brugere og derefter konfigurere en regel for krav. Følg denne fremgangsmåde for at gennemgå trinnene:

1. Følg trinnene beskrevet i [Turn-on Multi-Factor godkendelse til brugerne](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) for at aktivere en konto.
2. Start af AD FS-administrationskonsollen.
![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Gå til **Stole part har tillid til** , og højreklik på den stole part har tillid til. Klik på **Rediger Kræv regler...**
4. Klik på **Tilføj regel...**
5. Vælg **Send krav ved hjælp af en brugerdefineret regel** på rullelisten, og klik på **Næste**.
6. Angiv et navn til reglen krav.
7. Under brugerdefineret regel: tilføje følgende tekst:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Tilsvarende krav:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Klik på **OK** og derefter **Udfør**. Luk AD FS-administrationskonsollen.

Brugere kan derefter fuldføre logge på ved hjælp af metoden lokalt (såsom chipkort).

## <a name="trusted-ips-for-federated-users"></a>Der er tillid til IP-adresser for brugere, der er medlem af organisationsnetværk
Der er tillid til IP'er Tillad bypass totrinsbekræftelse-administratorer for bestemte IP-adresser eller medlem af organisationsnetværket brugere, der har anmodninger om kommer fra i deres egen intranet. I nedenstående afsnit beskrives, hvordan du konfigurerer Azure Multi-Factor Authentication der er tillid til IP'er med organisationsnetværket brugere og bypass totrinsbekræftelse, når en anmodning om stammer fra inden for et medlem af organisationsnetværket brugere intranet. Dette opnås ved at konfigurere AD FS for at bruge en pass-through eller filtrere en indgående krav-skabelon med typen i virksomhedens netværk krav.

I dette eksempel bruger Office 365 for vores stole part har tillid til.

### <a name="configure-the-ad-fs-claims-rules"></a>Konfigurere AD FS krav regler

Det første, du vi skal gøre, er at konfigurere AD FS-krav. Vi opretter to krav regler, én for typen i virksomhedens netværk krav og en ekstra én for bevarelse af vores brugere, der er logget på.

1. Åbn AD FS Management.
2. Vælg **Stole part har tillid til**, i venstre side.
3. Højreklik på **Microsoft Office 365 identitet Platform** og vælg **Rediger … Kræv regler** 
 ![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Klik på udstedelse transformere regler **Tilføj regel.** 
 ![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Vælg **overfører via eller Filtrer et indgående krav** på rullelisten transformere Kræv regel guiden Tilføj, og klik på **Næste**.
![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Giv reglen et navn i feltet ud for Kræv regelnavn. For eksempel: InsideCorpNet.
7. På rullelisten ud for indgående gøre krav på type, og vælg **I virksomhedens netværk**.
![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Klik på **Udfør**.
9. Klik på **Tilføj regel**udstedelse transformere regler.
10. Vælg **Send krav ved hjælp af en brugerdefineret regel** fra rullelisten transformere Kræv regel guiden Tilføj, og klik på **Næste**.
11. I feltet under Kræv regelnavn: Angiv *Holde brugere logget på*.
12. Angiv i feltet brugerdefinerede regel:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Klik på **Udfør**.
14. Klik på **Anvend**.
15. Klik på **Ok**.
16. Luk AD FS Management.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Konfigurere Azure Multi-Factor Authentication IP'er med organisationsnetværket brugere, der er tillid til
Nu, hvor tjenesten krav er på plads, kan vi konfigurere der er tillid til IP-adresser.

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **Active Directory**i venstre side.
3. Vælg den mappe, hvor du vil konfigurere der er tillid til IP'er under mappe.
4. Klik på den mappe, du har valgt, **Konfigurer**.
5. Klik på **Administrer Tjenesteindstillinger**i afsnittet Multi-Factor authentication.
6. Vælg på siden Tjenesteindstillinger under der er tillid til IP'er **springe MFA authentication anmodninger om fra organisationsnetværket brugere på min intranet.** 
 ![Skyen](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Klik på **Gem**.
8. Klik på **Luk**, når opdateringerne, der er anvendt.


Det var det! På dette tidspunkt har samlet Office 365-brugere kun bruge MFA, når et krav stammer fra uden for virksomhedens intranet.
