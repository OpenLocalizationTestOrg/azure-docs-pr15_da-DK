Domain Name System (DNS) bruges til at finde ting på internettet. For eksempel, når du angiver en adresse i din browser, eller klik på et link på en webside, bruger den DNS til at oversætte domænet til en IP-adresse. IP-adressen er slags som en adresse, men det er ikke meget human fulde. For eksempel er meget nemmere at huske et DNS-navn som **contoso.com** , end det er at huske en IP-adresse som 192.168.1.88 eller 2001:0:4137:1f67:24a2:3888:9cce:fea3.

DNS-systemet er baseret på *poster*. Poster knytte et bestemt *navn*, som **contoso.com**til en IP-adresse eller et andet DNS-navn. Når et program, som en webbrowser, søger efter et navn i DNS, finder posten, og bruger den den peger på adresse. Hvis den peger på værdien er en IP-adresse, bruge browseren den pågældende værdi. Hvis den peger på et andet DNS-navn, har programmet til at gøre opløsning igen. I sidste ende afsluttes alle navneoversættelse i en IP-adresse.

Når du opretter et websted, der Azure, tildeles automatisk et DNS-navn til webstedet. Dette navn tager form af ** &lt;yoursitename&gt;. azurewebsites.net**. Når du tilføjer dit websted som et slutpunkt Azure trafik Manager, webstedet derefter er tilgængelig via den ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domæne.

> [AZURE.NOTE] Når webstedet er konfigureret som et slutpunkt trafik Manager, du vil bruge den **. trafficmanager.net** adresse, når du opretter DNS-poster.

> Du kan kun bruge CNAME-poster med trafik Manager

Der findes også flere typer poster, hver med deres egne funktioner og begrænsninger, men til websteder, der er konfigureret til at som trafik Manager slutpunkter, vi kun sig om en; *CNAME* -poster.

###<a name="cname-or-alias-record"></a>CNAME eller Alias post

En CNAME-post knytter et *bestemt* DNS-navn, som **mail.contoso.com** eller **www.contoso.com**, til et andet (vedtaget) domænenavn. Hvis det er Azure websteder ved hjælp af trafik Manager, vedtaget domænenavnet er den ** &lt;myapp >. trafficmanager.net** domænenavn af profilens trafik Manager. Når oprettet, CNAME opretter et alias for den ** &lt;myapp >. trafficmanager.net** domænenavn. CNAME-posten oversætter til IP-adressen på dit ** &lt;myapp >. trafficmanager.net** domænenavn automatisk, så hvis IP-adressen til webstedet, ændres, ikke behøver du foretage dig noget.

Når trafik modtages på trafik Manager, dirigerer den trafik til dit websted ved hjælp af den metode, den er konfigureret til til justering af belastning. Dette er helt gennemsigtig for besøgende på webstedet. De kan kun se det brugerdefinerede domænenavn i deres browser.

> [AZURE.NOTE] Nogle domæneregistratorer kun muligt at tilknytte underdomæner, når du bruger en CNAME-post, som **www.contoso.com**og ikke rod navne, som **contoso.com**. Se dokumentationen til fra din domæneregistrator, <a href="http://en.wikipedia.org/wiki/CNAME_record">Wikipedia posten på CNAME-post</a>eller <a href="http://tools.ietf.org/html/rfc1035">IETF domænenavne - implementering og specifikation af</a> dokumentet kan finde flere oplysninger om CNAME-poster.
