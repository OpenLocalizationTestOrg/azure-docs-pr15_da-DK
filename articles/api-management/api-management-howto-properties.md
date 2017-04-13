<properties 
    pageTitle="Sådan bruges egenskaber i politikker for administration af Azure-API" 
    description="Lær, hvordan du bruger egenskaber i politikker for administration af Azure-API." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Sådan bruges egenskaber i politikker for administration af Azure-API

Politikker for administration af API er effektive findes i systemet, der tillader, at ændre funktionsmåden for API via konfiguration publisher. Politikker er en samling af sætninger, der udføres sekventielt på anmodningen eller svar på en API. Politik sætninger kan opbygget ved hjælp af ordret tekstværdier, politik udtryk og egenskaber. 

Hver forekomst af API Management service har en egenskaber samling af nøgle/værdi-par, der er globalt for forekomsten af. Disse egenskaber kan bruges til at administrere konstant strengværdier på tværs af alle API konfiguration og politikker. Hver egenskab har følgende attributter.


| Attribut | Type            | Beskrivelse                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Navn      | streng          | Navnet på egenskaben. Det kan indeholde kun bogstaver, tal, periode, bindestreg, og understregningstegn. |
| Værdi     | streng          | Værdien af egenskaben. Du kan ikke være tom eller kun bestå af mellemrum.                           |
| Hemmeligt    | Boolesk værdi         | Bestemmer, om værdien er en hemmeligt og skal krypteres eller ej.                                |
| Mærker      | matrix af streng | Valgfrit koder, når kan bruges til at filtrere egenskabslisten til rådighed.                               |

Er konfigureret i publisher-portalen på fanen **Egenskaber** . I eksemplet nedenfor er tre konfigureret.

![Egenskaber][api-management-properties]

Egenskabsværdier kan indeholde Strengkonstanter og [politik udtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx). I følgende tabel vises de forrige tre eksempel egenskaber og de tilhørende attributter. Værdien af `ExpressionProperty` et politik udtryk, der returnerer en streng, der indeholder dags dato og det aktuelle klokkeslæt. Egenskaben `ContosoHeaderValue` er markeret som et hemmeligt, så dens værdi ikke vises.

| Navn               | Værdi                      | Hemmeligt | Mærker    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | FALSK  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | SAND   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | FALSK  |         |

## <a name="to-use-a-property"></a>Bruge en egenskab

Hvis du vil bruge en egenskab i en politik, skal du placere egenskabsnavn i et dobbelte par klammeparenteser som `{{ContosoHeader}}`, som vist i følgende eksempel.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

I dette eksempel `ContosoHeader` bruges som navnet på et sidehoved i en `set-header` politikken, og `ContosoHeaderValue` bruges som værdien af den pågældende kolonneoverskrift. Når denne politik evalueres under en anmodning om eller svar til gatewayen API Management `{{ContosoHeader}}` og `{{ContosoHeaderValue}}` erstattes med deres respektive egenskabsværdier.

Egenskaber kan bruges som fuldført attribut eller værdier for elementer som vist i det forrige eksempel, men de kan også indsættes eller kombineres med en del af et ordret tekstudtryk som vist i følgende eksempel:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Egenskaber kan også indeholde politik udtryk. I følgende eksempel på `ExpressionProperty` bruges.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Når denne politik evalueres, `{{ExpressionProperty}}` erstattes med dens værdi: `@(DateTime.Now.ToString())`. Da værdien er en politikudtryk, udtrykket evalueres og politikken skrider frem med dens udførelse af.

Du kan teste denne indstillinger på portalen udvikler ved at ringe til en handling, der har en politik med egenskaber i omfang. I eksemplet nedenfor kaldes en handling med de to forrige eksempel `set-header` politikker med egenskaber. Bemærk, at svaret indeholder to brugerdefinerede overskrifter, der blev konfigureret ved hjælp af politikker med egenskaber.

![Udvikler portal][api-management-send-results]

Hvis du ser på [API inspektion spor](api-management-howto-api-inspector.md) til et opkald, der indeholder de to forrige eksempel politikker med egenskaber, kan du se to `set-header` politikker med de egenskabsværdier, der er indsat samt evaluering af udtryk i politik for den egenskab, der indeholdt Politikudtrykket.

![API inspektion sporing][api-management-api-inspector-trace]

Bemærk, mens egenskabsværdier kan indeholde politik udtryk, ikke kan egenskabsværdier indeholder andre egenskaber. Hvis tekst, der indeholder en egenskab reference bruges til en egenskabsværdi, såsom `Property value text {{MyProperty}}`, egenskaben referencen ikke erstattes og medtages som en del af egenskabsværdien for.

## <a name="to-create-a-property"></a>Til at oprette en egenskab

Hvis du vil oprette en egenskab, skal du klikke på **Tilføj egenskab** under fanen **Egenskaber** .

![Tilføj egenskab][api-management-properties-add-property-menu]

**Navn** og en **værdi** er påkrævede værdier. Hvis denne egenskabsværdi er et hemmeligt, Markér afkrydsningsfeltet **dette er et hemmeligt** . Angiv en eller flere valgfrit mærker for at hjælpe med at organisere dine egenskaber, og klik på **Gem**.

![Tilføj egenskab][api-management-properties-add-property]

Når en ny egenskab er gemt, tekstfeltet **Søg egenskaben** udfyldes med navnet på den nye egenskab, og den nye egenskab vises. Fjern markeringen i tekstfeltet **Søg egenskab** for at få vist alle egenskaber, og tryk på enter.

![Egenskaber][api-management-properties-property-saved]

Du kan finde oplysninger om oprettelse af en egenskab ved hjælp af REST-API, [oprette en egenskab ved hjælp af REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Redigere en egenskab

Hvis du vil redigere en egenskab, skal du klikke på **Rediger** ud for egenskaben til at redigere.

![Redigere en egenskab][api-management-properties-edit]

Foretag de ønskede ændringer, og klik på **Gem**. Hvis du ændrer navnet, opdateres automatisk alle politikker, som henviser til egenskaben for at bruge det nye navn.

![Redigere en egenskab][api-management-properties-edit-property]

Finde oplysninger om redigering af en egenskab ved hjælp af REST-API, kan du se [redigere en egenskab ved hjælp af REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Slette en egenskab

Hvis du vil slette en egenskab, skal du klikke på **Slet** ud for egenskaben for at slette.

![Slette egenskab][api-management-properties-delete]

Klik på **Ja, Slet den** for at bekræfte.

![Bekræft sletning][api-management-delete-confirm]

>[AZURE.IMPORTANT] Hvis egenskaben refereres til af politikker, kan du ikke slette den korrekt, indtil du fjerner egenskaben fra alle politikker, der bruger den.

Oplysninger om sletning af en egenskab ved hjælp af REST-API, kan du se [slette en egenskab ved hjælp af REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Til søgning og filtrering egenskaber

Fanen **Egenskaber** omfatter søgning og filtrering funktioner kan hjælpe dig med at administrere din egenskaber. Du kan filtrere egenskabslisten ved egenskabsnavn, Skriv et søgeord i tekstfeltet **Søg egenskab** . Fjern markeringen i tekstfeltet **Søg egenskab** for at få vist alle egenskaber, og tryk på enter.

![Søgning][api-management-properties-search]

For at filtrere egenskabslisten ved at mærke værdier, skal du angive et eller flere mærker i tekstfeltet **Filtrer efter mærker** . For at få vist alle egenskaber, fjern markeringen i **filtrere efter mærker** tekstfeltet og tryk på enter.

![Filter][api-management-properties-filter]

## <a name="next-steps"></a>Næste trin

-   Få mere at vide mere om at arbejde med politikker
    -   [Politikker for administration af API](api-management-howto-policies.md)
    -   [Politikreference](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Politik udtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Se en video oversigt

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

