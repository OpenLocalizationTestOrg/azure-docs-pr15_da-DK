<properties 
    pageTitle="Politikker for administration af Azure API | Microsoft Azure" 
    description="Lær at oprette, redigere og konfigurere politikker for administration af API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


#<a name="policies-in-azure-api-management"></a>Politikker for administration af Azure API

Politikker er i Azure API Management effektive findes i systemet, der tillader, at ændre funktionsmåden for API via konfiguration publisher. Politikker er en samling af sætninger, der udføres sekventielt på anmodningen eller svar på en API. Populære sætninger omfatter konvertering af filformat fra XML til JSON, og kald rente begrænse for at begrænse mængden af indgående opkald fra en udvikler. Der findes mange flere politikker af feltet.

Se [Politikreference][] til en komplet liste over politik sætninger og deres indstillinger.

Politikker, der anvendes i gatewayen, som er placeret mellem API forbruger- og administrerede API. Gatewayen modtager alle anmodninger og normalt videresender dem uændret til underliggende API. En politik kan dog anvende ændringer på både den indgående og udgående svar.

Politik udtryk kan bruges som attributten værdier eller tekstværdier i en af politikkerne for administration af API, medmindre politikken angiver noget andet. Politikker som [Kontrolflow][] og [angive variabel][] politikker er baseret på politik udtryk. Se [Avancerede politikker][] og [politik udtryk][]kan finde flere oplysninger.

## <a name="scopes"> </a>Hvordan du kan konfigurere politikker
Politikker kan konfigureres globalt eller i omfanget af et [produkt][], [API][] eller [handling][]. For at konfigurere en politik, skal du gå til politikker editor i publisher-portalen.

![Politikker menu][policies-menu]

Redigeringsprogrammet politikker består af tre afsnit: politikken scope (øverst), politikdefinitionen hvor politikker er redigeret (venstre) og sætningerne liste (højre):

![Politikker editor][policies-editor]

For at konfigurere en politik, skal du først markere det område, som politikken skal anvendes. I skærmbilledet nedenfor **Starter** er produktet markeret. Bemærk, at firkantet symbolet ud for politikkens navn angiver, at en politik anvendes allerede på dette niveau.

![Omfang][policies-scope]

Da der allerede er anvendt en politik, vises konfigurationen i visningen definition.

![Konfigurere][policies-configure]

Politikken vises skrivebeskyttet i første omgang. Klik på handlingen **Konfigurere politikken,** før du kan redigere definitionen.

![Rediger][policies-edit]

Politikdefinitionen er et simpelt XML-dokument, der beskriver en række indgående og udgående sætninger. XML-filen kan redigeres direkte i definitionsvinduet. En liste over sætninger, der overføres til højre og sætninger, der gælder for det aktuelle område er aktiveret og fremhævet; dokumenteret ved sætningen **Grænse ringe rente** i skærmbilledet ovenfor.

Klik på sætningen aktiveret føjer den relevante XML på sted, hvor markøren i visningen definition. 

>[AZURE.NOTE] Hvis politikken, som du vil tilføje ikke er aktiveret, kan du sikre dig, at du er i det korrekte område for den pågældende politik. Hver Politikerklæringen er beregnet til brug i visse områder og politik sektioner. Hvis du vil gennemse politik sektioner og områder for en politik, kan du læse afsnittet **brugen** for den pågældende politik i [Politikreference][].

En komplet liste over politik sætninger og deres indstillinger er tilgængelige i [Politikreference][].

For eksempel for at tilføje en ny sætning for at begrænse indgående anmodninger til angivne IP-adresser, skal du placere markøren lige inden indholdet af den `inbound` XML-element, og klik på sætningen **begrænset opkalds IP'er** .

![Begrænsning politikker][policies-restrict]

Dette føjer en XML-kodestykke til den `inbound` element, der giver vejledning i, hvordan du konfigurerer sætningen.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

For at begrænse indgående anmodninger og acceptere ændres kun dem fra en IP-adresse 1.2.3.4 XML på følgende måde:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Gem][policies-save]

Når fuldført konfiguration af sætningerne for politikken, klik på **Gem** og ændringerne bliver overført til API Management gateway med det samme.

##<a name="sections"> </a>Forstå konfiguration af politik

En politik er en række sætninger, der udføres i rækkefølge for en anmodning om og svar. Konfigurationen er opdelt korrekt i `inbound`, `backend`, `outbound`, og `on-error` sections som vist i følgende konfiguration.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Hvis der opstår en fejl under behandlingen af en forespørgsel, eventuelle resterende trin i den `inbound`, `backend`, eller `outbound` sektioner er ignoreret, og udførelse af springer til sætninger i den `on-error` sektion. Ved at placere politik sætninger i den `on-error` sektion, du kan gennemse fejlen ved hjælp af den `context.LastError` egenskab, undersøge og tilpasse den fejl svar ved hjælp af den `set-body` politikken, og konfigurere Hvad sker der, hvis der opstår fejl. Der findes fejlkoder indbyggede vejledning og for fejl, der kan opstå under behandling af politik sætninger. Se [fejlhåndtering i politikker for administration af API](https://msdn.microsoft.com/library/azure/mt629506.aspx)kan finde flere oplysninger.

Da politikker kan angives på forskellige niveauer (global, produkt, api og drift) gør muligt for dig at angive den rækkefølge, hvori politikdefinitionen sætninger udføre i forhold til den overordnede politik konfigurationen. 

Politik områder evalueres i følgende rækkefølge.

1. Global omfang
2. Produktomfang
3. API omfang
4. Handlingen omfang

Sætningerne grænserne evalueres efter placeringen af den `base` element, hvis den findes.

Eksempelvis hvis du har en politik på globalt niveau og en politik, der er konfigureret til en API, anvendes derefter når pågældende bestemt API bruges begge politikker. API administration giver mulighed for deterministisk sortering af kombinerede politik udtalelser via det grundlæggende element. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

I ovenstående eksempel politikdefinitionen på `cross-domain` sætningen skal udføre, før en højere politikker, som skal gennemgås efterfølges af den `find-and-replace` politik.

Hvis den samme politik vises to gange i Politikerklæringen, anvendes senest evalueres politikken. Du kan bruge dette til at tilsidesætte de politikker, der er defineret på et højere omfang. For at se politikkerne, der i det aktuelle område i politikeditoren skal du klikke på **genberegne gældende politik for markerede område**.

Bemærk, at globale politik har ingen overordnede politik og bruge den `<base>` element i det har ingen effekt. 

## <a name="next-steps"></a>Næste trin

Se følgende video i politik udtryk.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Politikreference]: api-management-policy-reference.md
[Produkt]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Handling]: api-management-howto-add-operations.md

[Avancerede politikker]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Kontrolflow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set-variabel]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Politik udtryk]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
