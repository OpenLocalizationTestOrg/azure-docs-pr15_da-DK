#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Konfigurere et brugerdefineret domænenavn til en Azure websted

Når du opretter et websted, Azure leverer et brugervenligt underdomæne på domænet, azurewebsites.net, så brugerne kan få adgang til dit websted ved hjælp af en URL, såsom http://&lt;mit websted >. azurewebsites.net. Hvis du konfigurerer dine websteder for delt eller standardtilstand, kan du knytte dit websted til dit eget domænenavn.

Du kan også kan du bruge Azure trafik Manager for at indlæse balance indgående trafik til dit websted. Du kan finde flere oplysninger om, hvordan trafik Manager fungerer med websteder, se [Styre Azure websteder trafik med Azure trafik Manager][trafficmanager].

> [AZURE.NOTE] Anvende procedurerne i denne opgave til Azure websteder; Se <a href="/develop/net/common-tasks/custom-dns/">konfigurere et brugerdefineret domænenavn i Azure</a>for Cloud Services.

> [AZURE.NOTE] Trinnene i denne opgave skal du konfigurere dine websteder for delt eller standardtilstand, som kan blive ændret, hvor meget du er faktureret for dit abonnement. Du kan få flere oplysninger i <a href="/pricing/details/web-sites/">Websteder priser detaljer</a> .

I denne artikel:

-   [Forstå CNAME og A poster](#understanding-records)
-   [Konfigurere dine websteder for delte eller standard tilstand](#bkmk_configsharedmode)
-   [Tilføj dine websteder til trafik administrator](#trafficmanager)
-   [Tilføje en CNAME for dit brugerdefinerede domæne](#bkmk_configurecname)
-   [Tilføje en A-post for dit brugerdefinerede domæne](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Forstå CNAME og A poster</h2>

CNAME (eller alias records) og en poster begge gør det muligt at knytte et domænenavn til et websted, men de kan fungere forskelligt.

###<a name="cname-or-alias-record"></a>CNAME eller Alias post

En CNAME-post knytter et *bestemt* domæne, som **contoso.com** eller **www.contoso.com**, til et vedtaget domænenavn. I dette tilfælde vedtaget domænenavnet er enten den ** &lt;myapp >. azurewebsites.net** domænenavn på webstedet Azure eller ** &lt;myapp >. trafficmgr.com** domænenavn af profilens trafik Manager. Når oprettet, CNAME opretter et alias for den ** &lt;myapp >. azurewebsites.net** eller ** &lt;myapp >. trafficmgr.com** domænenavn. CNAME-posten oversætter til IP-adressen på dit ** &lt;myapp >. azurewebsites.net** eller ** &lt;myapp >. trafficmgr.com** domænenavn automatisk, så hvis IP-adressen til webstedet, ændres, ikke behøver du foretage dig noget.

> [AZURE.NOTE] Nogle domæneregistratorer kun muligt at tilknytte underdomæner, når du bruger en CNAME-post, som www.contoso.com og ikke rod navne, som contoso.com. Se dokumentationen til fra din domæneregistrator, <a href="http://en.wikipedia.org/wiki/CNAME_record">Wikipedia posten på CNAME-post</a>eller <a href="http://tools.ietf.org/html/rfc1035">IETF domænenavne - implementering og specifikation af</a> dokumentet kan finde flere oplysninger om CNAME-poster.

###<a name="a-record"></a>En post

En A-post knytter et domæne, som **contoso.com** eller **www.contoso.com**, *eller et jokertegn domæne* såsom ** \*. contoso.com**, til en IP-adresse. Hvis det er et websted, Azure skal adresse enten den virtuelle IP-adresse til tjenesten eller en bestemt IP-, du har købt til dit websted. Så den primære fordel ved en A-post over en CNAME-post er, at du kan have én post, der bruger et jokertegn, f.eks * **. contoso.com**, som skal håndtere anmodninger om flere underdomæner såsom * *mail.contoso.com**, * *login.contoso.com**, eller * *www.contso.com**.

> [AZURE.NOTE] Da en A-post er knyttet til en statisk IP-adresse, ikke kan der automatisk fortolkes ændringer til IP-adressen på dit websted. En IP-adresse til brug sammen med en poster leveres, når du konfigurerer brugerdefinerede domænenavnsindstillinger til dit websted Denne værdi kan dog ændre, hvis du sletter og genskabe dit websted eller ændre tilstanden websted bagest for at frigøre.

> [AZURE.NOTE] En poster kan ikke bruges til med trafik Manager justering af belastning. Du kan finde flere oplysninger under [Styre Azure websteder trafik med Azure trafik Manager][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Konfigurere dine websteder for delte eller standard tilstand</h2>

Angive et brugerdefineret domænenavn på et websted er kun tilgængelig for delt og Standard tilstande efter Azure websteder. Inden du skifter et websted fra den gratis websted til delt eller Standard websted tilstand, skal du først fjerne udgifter bogstaver i stedet for dit websted abonnement. Flere oplysninger om delt og Standard tilstand priser, se [Priser detaljer][PricingDetails].

1. I din browser skal du åbne [Management Portal][portal].
2. Fanen **websteder** , skal du klikke på navnet på dit websted.

    ![][standardmode1]

3. Klik på fanen **SKALA** .

    ![][standardmode2]


4. Angiv tilstanden websted ved at klikke på **delt**i sektionen **Generelt** .

    ![][standardmode3]

    > [AZURE.NOTE] Hvis du vil bruge trafik Manager med dette websted, skal du bruge Vælg standardtilstand i stedet for delt.

5. Klik på **Gem**.
6. Når du bliver bedt om forøgelsen omkostninger for delt tilstand (eller til standardtilstand, hvis du vælger Standard), skal du klikke på **Ja** , hvis du accepterer.

    <!--![][standardmode4]-->

    **Bemærk!**<br />
   Hvis du får vist fejlmeddelelsen "Konfiguration af skala til websted 'websted name' mislykkedes", kan du bruge knappen detaljer til at få flere oplysninger.

<a name="trafficmanager"></a><h2>(Valgfrit) Tilføj dine websteder til trafik administrator</h2>

Hvis du vil bruge dit websted med trafik Manager, kan du udføre følgende trin.

1. Hvis du ikke allerede har en trafik Manager profil, kan du bruge oplysninger i [oprette en trafik Manager profil ved hjælp af Hurtig oprettelse] [ createprofile] til at oprette en. Bemærk de **. trafficmgr.com** domænenavn, der er knyttet til din profil trafik Manager. Dette vil blive brugt på et senere tidspunkt.

2. Brug oplysningerne i [Tilføj eller Slet slutpunkter] [ addendpoint] til at tilføje dit websted som et slutpunkt i din profil trafik Manager.

    > [AZURE.NOTE] Hvis webstedet ikke vises, når du tilføjer et slutpunkt skal du kontrollere, at den er konfigureret til Standard-tilstand. Hvis du vil arbejde med trafik Manager skal du bruge standardtilstand til dit websted.

3. Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**.

4. Find nu, hvor du kan markere eller skrive CNAME-poster. Du kan være nødvendigt at vælge posttypen fra en slip ned, eller gå til en side, avancerede indstillinger. Du skal se efter ordene **CNAME** **Alias**eller **underdomæner**.

5. Du skal også angive domæne eller underdomæne aliasset for CNAME. For eksempel **www** Hvis du vil oprette et alias for **www.customdomain.com**.

5. Du skal også angive et værtsnavn, der er vedtaget domænenavnet for denne CNAME alias. Dette er den **. trafficmgr.com** navn til dit websted.

For eksempel videresender følgende CNAME-post al trafik fra **www.contoso.com** til **contoso.trafficmgr.com**, domænenavn for et websted:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Host name/underdomæne</strong></td>
<td><strong>Vedtaget domæne</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.trafficmgr.com</td>
</tr>
</table>

En besøgende **www.contoso.com** ser aldrig værten SAND (contoso.azurewebsite.net), så processen viderestilling er usynlige til slutbrugeren.

> [AZURE.NOTE] Hvis du bruger trafik Manager med et websted, skal du ikke følge trinnene i de følgende afsnit '**Tilføj en CNAME for dit brugerdefinerede domæne**' og '**Tilføj en A-post for dit brugerdefinerede domæne**'. Den CNAME-post, der er oprettet i de forrige trin omdirigere indgående trafik til trafik Manager, som derefter dirigerer trafik til websted endpoint(s).

<a name="bkmk_configurecname"></a><h2>Tilføje en CNAME for dit brugerdefinerede domæne</h2>

Hvis du vil oprette en CNAME-post, skal du tilføje en ny post i tabellen DNS for dit brugerdefinerede domæne ved hjælp af værktøjer, der leveres af din domæneregistrator. Hver registrator har en ligner, men nogle lidt andre metode til at angive en CNAME-post, men begreberne er de samme.

1. Benyt en af disse metoder til at finde den **. azurewebsite.net** domænenavn, der er tildelt til dit websted.

    * Logge på [Azure Management Portal][portal], Vælg dit websted, Vælg **Dashboard**og derefter finde posten **Webstedets URL-adresse** i sektionen **Oversigt over** .

    * Installere og konfigurere [Azure Powershell](/manage/install-and-configure-windows-powershell/), og derefter bruge følgende kommando:

            get-azurewebsite yoursitename | select hostnames

    * Installere og konfigurere [Azure Command Line Interface](/manage/install-and-configure-cli/), og derefter bruge følgende kommando:

            azure site domain list yoursitename

    Gemme dette **. azurewebsite.net** navn, som der skal bruges i de følgende trin.

3. Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**.

4. Find nu, hvor du kan markere eller skrive CNAME-poster. Du kan være nødvendigt at vælge posttypen fra en slip ned, eller gå til en side, avancerede indstillinger. Du skal se efter ordene **CNAME** **Alias**eller **underdomæner**.

5. Du skal også angive domæne eller underdomæne aliasset for CNAME. For eksempel **www** Hvis du vil oprette et alias for **www.customdomain.com**. Hvis du vil oprette et alias for roddomænet, kan det være angivet som den '**@**' symbolet i din domæneregistrator DNS-værktøjer.

5. Du skal også angive et værtsnavn, der er vedtaget domænenavnet for denne CNAME alias. Dette er den **. azurewebsite.net** navn til dit websted.

For eksempel videresender følgende CNAME-post al trafik fra **www.contoso.com** til **contoso.azurewebsite.net**, domænenavn for et websted:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Host name/underdomæne</strong></td>
<td><strong>Vedtaget domæne</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.azurewebsite.NET</td>
</tr>
</table>

En besøgende **www.contoso.com** ser aldrig værten SAND (contoso.azurewebsite.net), så processen viderestilling er usynlige til slutbrugeren.

> [AZURE.NOTE] Eksemplet ovenfor gælder kun for trafik på __www__ underdomæne. Da du ikke bruge jokertegn i CNAME-poster, skal du oprette én CNAME for hvert domæne/underdomæne. Hvis du vil dirigere trafik fra underdomæner, såsom *. contoso.com, til din azurewebsite.net adresse, du kan konfigurere en __URL-adressen omdirigere__ eller __URL-adressen Videresend__ post i dine DNS-indstillinger, eller oprette en A-post.

> [AZURE.NOTE] Det kan tage lidt tid, før din CNAME at overføre via DNS-systemet. Du kan ikke angive CNAME for det offentlige websted, indtil CNAME har overført. Du kan bruge en tjeneste, såsom <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> til at kontrollere, at CNAME er tilgængelig.

###<a name="add-the-domain-name-to-your-website"></a>Tilføje domænenavnet på dit websted

Når du har overført CNAME-posten for det domænenavn, skal du knytte den til dit websted. Du kan tilføje det brugerdefinerede domænenavn, der er defineret af CNAME-posten til dit websted ved hjælp af enten Azure kommandolinjen (Azure CLI) eller ved hjælp af portalen Azure administration.

**Tilføje et domænenavn, ved hjælp af værktøjerne kommandolinjen**

Installere og konfigurere [Azure kommandolinjen](/manage/install-and-configure-cli/), og derefter bruge følgende kommando:

    azure site domain add customdomain yoursitename

For eksempel tilføje følgende et brugerdefineret domænenavn af **www.contoso.com** på webstedet **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Du kan bekræfte, at det brugerdefinerede domænenavn blev føjet til det offentlige websted ved hjælp af følgende kommando:

    azure site domain list yoursitename

På listen, der returneres af denne kommando skal indeholde det brugerdefinerede domænenavn, såvel som standard **. azurewebsite.net** post.

**Tilføje et domænenavn, ved hjælp af portalen Azure administration**

1. I din browser skal du åbne [Azure Management Portal][portal].

2. I fanen **websteder** , klik på navnet på dit websted, Vælg **Dashboard**og derefter vælge **Administrer domæner** fra bunden af siden.

    ![][setcname2]

6. I tekstfeltet **DOMAIN NAMES** skal du skrive navnet på det domæne, du har konfigureret.

    ![][setcname3]

6. Klik på markeringen i afkrydsningsfeltet for at acceptere domænenavnet.

Når konfigurationen er fuldført, vises det brugerdefinerede domænenavn i sektionen **domain names** på siden **konfiguration** af dit websted.

<a name="bkmk_configurearecord"></a><h2>Tilføje en A-post for dit brugerdefinerede domæne</h2>

Hvis du vil oprette en A-post, skal du først finde IP-adressen på dit websted. Derefter skal du tilføje en post i tabellen DNS for dit brugerdefinerede domæne ved hjælp af værktøjerne, der leveres af din domæneregistrator. Hver registrator har en ligner, men nogle lidt andre metode til at angive en A-post, men begreberne er de samme. Ud over at oprette en A-post, skal du også oprette en CNAME-post, der bruger Azure at bekræfte A-posten.

1. I din browser skal du åbne [Azure Management Portal][portal].

2. I fanen **websteder** , klik på navnet på dit websted, Vælg **Dashboard**og derefter vælge **Administrer domæner** fra bunden af skærmen.

    ![][setcname2]

5. Find **Feltet IP-adressen skal bruges, når du konfigurerer en poster**i dialogboksen **Administrer brugerdefinerede domæner** . Kopiér IP-adressen. Dette vil blive brugt, når du opretter A-posten.

5. Bemærk awverify domænenavn i slutningen af teksten øverst i dialogboksen i dialogboksen **Administrer brugerdefinerede domæner** . Det skal være **awverify.mysite.azurewebsites.net** hvor **Mit websted** er navnet på dit websted. Kopiere det, som det er navnet på det domæne, bruges ved oprettelse af bekræftelsen CNAME-post.

6. Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**.

6. Find, hvor du kan markere eller skrive A and CNAME-poster. Du kan være nødvendigt at vælge posttypen fra en slip ned, eller gå til en side, avancerede indstillinger.

7. Udfør følgende trin for at oprette posten:

    1. Vælg eller Angiv domæne eller underdomæne, der anvender A-posten. For eksempel vælge **www** , hvis du vil oprette et alias for **www.customdomain.com**. Hvis du vil oprette et jokertegn post for alle underdomæner, skal du angive '__*__'. Dette vil dække alle underdomæner som **mail.customdomain.com**, **login.customdomain.com**og **www.customdomain.com**.

        Hvis du vil oprette en A-post for roddomænet, kan det være angivet som den '**@**' symbolet i din domæneregistrator DNS-værktøjer.

    2. Angiv din skytjeneste IP-adresse i det angivne felt. Dette knytter posten domain bruges i posten med implementeringen af denne skyen service IP-adresse.

        For eksempel følgende en post videresender al trafik fra **contoso.com** til **137.135.70.239**, IP-adressen på vores installerede program:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Host name/underdomæne</strong></td>
        <td><strong>IP-adresse</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        I dette eksempel vises, oprette en A-post for roddomænet. Hvis du vil oprette et jokertegn post dækker alle underdomæner, skal du angive '__*__' som underdomænet.

7. Opret derefter en CNAME-post, der indeholder et alias for **awverify**og et til **awverify.mysite.azurewebsites.net** , som du hentede tidligere vedtaget domæne.

    > [AZURE.NOTE] Mens et alias for awverify fungerer måske for nogle udbyder, kan andre kræver fuld alias domænenavnet af awverify.www.customdomainname.com eller awverify.customdomainname.com.

    For eksempel opretter følgende en CNAME-post, Azure kan bruge til at kontrollere konfigurationen af A-post.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Alias/Host name/underdomæne</strong></td>
    <td><strong>Vedtaget domæne</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Det kan tage lidt tid, før awverify CNAME at overføre via DNS-systemet. Du kan ikke angive det brugerdefinerede domænenavn, der er defineret af A-posten for det offentlige websted, indtil awverify CNAME har overført. Du kan bruge en tjeneste, såsom <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> til at kontrollere, at CNAME er tilgængelig.

###<a name="add-the-domain-name-to-your-website"></a>Tilføje domænenavnet på dit websted

Når du har overført **awverify** CNAME-posten for det domænenavn, kan du derefter knytte dit eget domæne, der er defineret af posten med dit offentlige websted. Du kan tilføje det brugerdefinerede domænenavn, der er defineret af A-post til dit websted ved hjælp af enten den Azure CLI eller ved hjælp af portalen Azure administration.

**Tilføje et domænenavn, der bruger kommandolinjen Azure (Azure CLI)**

Installere og konfigurere [Azure CLI](/manage/install-and-configure-cli/), og derefter bruge følgende kommando:

    azure site domain add customdomain yoursitename

For eksempel tilføje følgende et brugerdefineret domænenavn af **contoso.com** på webstedet **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Du kan bekræfte, at det brugerdefinerede domænenavn blev føjet til det offentlige websted ved hjælp af følgende kommando:

    azure site domain list yoursitename

På listen, der returneres af denne kommando skal indeholde det brugerdefinerede domænenavn, såvel som standard **. azurewebsite.net** post.

**Tilføje et domænenavn, ved hjælp af portalen Azure administration**

1. I din browser skal du åbne [Azure Management Portal][portal].

2. I fanen **websteder** , klik på navnet på dit websted, Vælg **Dashboard**og derefter vælge **Administrer domæner** fra bunden af siden.

    ![][setcname2]

6. I tekstfeltet **DOMAIN NAMES** skal du skrive navnet på det domæne, du har konfigureret.

    ![][setcname3]

6. Klik på markeringen i afkrydsningsfeltet for at acceptere domænenavnet.

Når konfigurationen er fuldført, vises det brugerdefinerede domænenavn i sektionen **domain names** på siden **konfiguration** af dit websted.

> [AZURE.NOTE] Når du har føjet det brugerdefinerede domænenavn, der er defineret af A-post til dit websted, kan du fjerne den awverify CNAME-post ved hjælp af værktøjerne leveres af din domæneregistrator. Men hvis du vil tilføje en anden A post i fremtiden, er du nødt til at genskabe awverify post, før du kan knytte navnet på det nye domæne, der er defineret af den nye en post med det offentlige websted.

## <a name="next-steps"></a>Næste trin

-   [Sådan administreres websteder](/manage/services/web-sites/how-to-manage-websites/)

-   [Konfigurere et SSL-certifikat til websteder](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
