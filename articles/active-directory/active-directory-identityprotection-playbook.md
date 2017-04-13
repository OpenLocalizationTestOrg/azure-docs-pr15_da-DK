<properties
    pageTitle="Azure Active Directory identitetsbeskyttelse playbook | Microsoft Azure"
    description="Få mere at vide, hvordan Azure AD identitet beskyttelse gør det muligt at begrænse muligheden for en hacker at udnytte et kompromitteret identitet eller enhed og for at sikre en identitet eller en enhed, der tidligere blev mulig eller blevet konstateret, at være kompromitteret."
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

#<a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory identitetsbeskyttelse playbook 

Denne playbook hjælper dig med at:

- Udfylde data i miljøet beskyttelse af din identitet ved at simulere risikoen begivenheder og svagheder
- Konfigurere politikker for risikoen-baserede betinget adgang og teste virkningerne af disse politikker


## <a name="simulating-risk-events"></a>Simulere risikoen begivenheder

Dette afsnit indeholder trin for at simulere følgende risikoen begivenhed typer:

- Logon fra anonyme IP-adresser (nemt)
- Logon fra fremmede placeringer (moderat)
- Umuligt rejse til atypiske placeringer (svær)

Andre risikoen begivenheder kan ikke simuleret på en sikker måde.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logon fra anonyme IP-adresser

Denne risiko hændelsestype identificerer brugere, der har logget på fra en IP-adresse, der er identificeret som en anonym proxy IP-adresse. Disse proxyer, der bruges af personer, der skal skjules deres enhed IP-adresse og kan bruges til ondsindet angreb.

**For at efterligne et logon fra en anonym IP-adresse, skal du udføre følgende trin**:

1.  Hent [Kommissoriet Browser](https://www.torproject.org/projects/torbrowser.html.en).
2.  Ved hjælp af browseren Tor, gå til [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Angive legitimationsoplysninger for den konto, der skal vises i rapporten **logon fra anonyme IP-adresser** .

Log på, vises på dashboardet til beskyttelse af din identitet i fem minutter. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Logon fra fremmede steder

Fremmede placeringer risikoen er en metode til beregning i realtid-logon, finder tidligere logon placeringer (IP-adresse, længde / breddegrader og ASN) til at bestemme nye / fremmede placeringer. Systemet gemmer forrige IP'er, længde / breddegrader og ASN'er for en bruger og finder disse skal være velkendte placeringer. En logonadresse betragtes fremmede, hvis den logonadresse ikke svarer til nogen af de eksisterende velkendte placeringer.

Beskyttelse af Azure Active Directory din identitet:  

 - har en indledende learning periode for 14 dage, hvor det ikke markere alle nye placeringer som fremmede placeringer.
 - ignorerer logon fra velkendte enheder og placeringer, der geografisk er tæt på en eksisterende velkendte placering.

For at efterligne fremmede placeringer, skal du logge på fra en placering og en enhed, hvor kontoen ikke har logget på fra før. 


**For at efterligne et logon fra en fremmede placering, skal du udføre følgende trin**:

1.  Vælg en konto, der har mindst en 14 dage logon oversigt. 

2.  Benyt en:
    
    en. Mens du bruger en VPN-forbindelse, skal du gå til [https://myapps.microsoft.com](https://myapps.microsoft.com) og angive legitimationsoplysninger for den konto, du vil simulere hændelsen risikoen for.

    b. Bed en kollega i et andet sted til at logge på ved hjælp af firmaets legitimationsoplysninger (anbefales ikke).

Log på, vises på dashboardet til beskyttelse af din identitet i fem minutter.
 
### <a name="impossible-travel-to-atypical-location"></a>Umuligt rejse til atypiske placering
Det er svært at simulere betingelsen umuligt rejse, fordi algoritmen bruger maskine læ til luger ud falsk-positive som umuligt rejse fra velkendte enheder eller logon fra VPN, der bruges af andre brugere i kataloget. Derudover kræver algoritmen en logon-oversigt over 3-14 dage for brugeren, før den begynder genererende risikoen begivenheder.

**For at efterligne en umuligt rejse til atypiske placering, skal du udføre følgende trin**:

1.  Ved hjælp af browseren standard, gå til [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Angive legitimationsoplysninger for den konto, du vil oprette en umuligt rejse risikohændelse for.

3.  Ændre din brugeragent. Du kan ændre brugeragent i Internet Explorer fra udviklerværktøjer eller ændre din brugeragent i Firefox eller Chrome ved hjælp af en brugeragent omskifter tilføjelsesprogram.

4.  Ændre din IP-adresse. Du kan ændre din IP-adresse ved hjælp af en VPN-forbindelse, et tilføjelsesprogram til kommissoriet, eller rotering en ny maskine i Azure i et andet datacenter.

5.  Logon til [https://myapps.microsoft.com](https://myapps.microsoft.com) med de samme legitimationsoplysninger som før og efter et par minutter efter den forrige logon.

Log på vises på dashboardet til beskyttelse af din identitet i 2-4 timer.<br>
På grund af komplekse maskinen læ modeller involveret, er der mulighed for det ikke få valgte op.<br> Du vil gentage disse trin for flere Azure AD-konti.


## <a name="simulating-vulnerabilities"></a>Simulere svagheder 
Svagheder er svagheder i et Azure AD-miljø, som en forkert Agent kan udnytter. I øjeblikket er 3 typer svagheder fået i Azure AD identitet beskyttelse, der udnytter andre funktioner i Azure AD. Disse svagheder vises på dashboardet til beskyttelse af din identitet automatisk når disse funktioner er konfigureret.

-   Azure AD [multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD [Skyen App registrering](active-directory-cloudappdiscovery-whatis.md).
-   Azure AD [privilegerede identitet administration](active-directory-privileged-identity-management-configure.md). 



##<a name="user-compromise-risk"></a>Bruger sammensat risikoen

**Hvis du vil teste bruger sammensat risikoen, skal du udføre følgende trin**:

1.  Log på [https://portal.azure.com](https://portal.azure.com) med legitimationsoplysninger for global administrator for din lejer.

2.  Gå til **beskyttelse af din identitet**. 

3.  Klik på **Indstillinger**på bladet primære **Azure AD identitet beskyttelse** . 

4.  Klik på **bruger forringer risikoen**under **sikkerhedsregler**på bladet **Portal-indstillinger** . 

5.  Deaktivere **aktivere regel** på bladet **logge på risikoen** , og klik derefter på **Gem** indstillinger.

6.  For en given brugerkonto simulere en fremmede placeringer eller anonyme IP-risikohændelse. Dette vil ændre niveauet bruger risikoen for den pågældende bruger til **mediet**.

7.  Vent et par minutter, og derefter kontrollere, at brugerniveau for din bruger er **mediet**.

8.  Gå til bladet **Portal-indstillinger** .

9.  Vælg **aktiveret** på bladet **Bruger sammensat risikoen** under **aktivere regel**. 

10. Vælg en af følgende indstillinger:

    en. Hvis du vil blokere, ved at vælge **mellem** under **Bloker logge på**.

    b. For at gennemtvinge ændring af sikker adgangskode, skal du vælge **mediet** under **Kræv Multi-Factor authentication**.

13. Klik på **Gem**.

14. Nu kan du teste risikoen-baserede betinget adgang ved at logge på ved hjælp af en bruger med en øgede risiko niveau. Hvis brugeren risikoen er Medium, er blokeres enten på din logonadresse afhængigt af konfigurationen af din politik eller du er nødt til at ændre din adgangskode. 
<br><br>
![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Log på risikoen

 
**Du kan teste logger i risikoen ved at udføre følgende trin:**

1.  Log på [https://portal.azure.com](https://portal.azure.com) med legitimationsoplysninger for global administrator for din lejer.

2.  Gå til **beskyttelse af din identitet**.

3.  Klik på **Indstillinger**på bladet primære **Azure AD identitet beskyttelse** . 

4.  Klik på **Log på risikoen**under **sikkerhedsregler**på bladet **Portal-indstillinger** .

5.  Vælg **på** under **aktivere regel**bladet **logge på risikoen **. 

7.  Vælg en af følgende indstillinger:

    en. Hvis du vil blokere, vælge **mellem** under **Bloker logge på**

    b. For at gennemtvinge ændring af sikker adgangskode, skal du vælge **mediet** under **Kræv Multi-Factor authentication**.

8.  Hvis du vil blokere, vælge mellem under Bloker Log på.

9.  Vælg **mellem** under **kræver godkendelse i flere niveauer**for at gennemtvinge Multi-Factor authentication.

10. Klik på **Gem**.

11. Nu kan du teste risikoen-baserede betinget adgang ved at simulere fremmede placeringer eller anonyme IP-risikoen begivenheder, fordi de er begge **mediet** risikoen begivenheder.

<br>
![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Se også

 - [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md)
