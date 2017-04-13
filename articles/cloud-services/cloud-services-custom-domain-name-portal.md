<properties
    pageTitle="Konfigurere et brugerdefineret domænenavn i Cloud Services | Microsoft Azure"
    description="Lær at fremvise din Azure-program eller dine data på internettet på et brugerdefineret domæne ved at konfigurere DNS-indstillinger.  Disse eksempler bruger Azure portalen."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurere et brugerdefineret domænenavn til en Azure skybaseret tjeneste

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-custom-domain-name-portal.md)
- [Azure klassisk portal](cloud-services-custom-domain-name.md)

Når du opretter en skybaseret tjeneste, tildeler Azure den til et underdomæne til **cloudapp.net**. Eksempelvis hvis skybaseret tjeneste har navnet "contoso", skal brugerne kunne få adgang til dit program på en URL, såsom http://contoso.cloudapp.net. Azure tildeler også en virtuel IP-adresse.

Du kan dog også fremvise dit program på dit eget domænenavn, som **contoso.com**. I denne artikel forklares, hvordan reservere eller konfigurere et brugerdefineret domænenavn til skybaseret tjeneste web roller.

Vil du allerede undestand hvilke CNAME og A-poster? [Gå videre fra explaination](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Fremgangsmåderne i denne opgave gælder for Azure Cloud Services. Få vist [denne](../app-service-web/web-sites-custom-domain-name.md)for App-tjenester. Lagerplads-konto, skal du se [denne](../storage/storage-custom-domain-name.md).

<p/>

> [AZURE.TIP]
> Kom i gang hurtigere – Brug den nye Azure [gennemgang af](http://support.microsoft.com/kb/2990804)!  Det gør at tilknytte et brugerdefineret domænenavn og sikring af kommunikation (SSL) med Azure Cloud Services eller Azure websteder en Fastgør.

## <a name="understand-cname-and-a-records"></a>Forstå CNAME og A poster

CNAME (eller alias records) og en poster begge gør det muligt at knytte et domænenavn til en bestemt server (eller en tjeneste i dette tilfælde) men de fungerer anderledes. Der er også nogle bestemte overvejelser, når du bruger en poster med Azure Cloud services, som du bør overveje, før du beslutter, hvor du kan bruge.

### <a name="cname-or-alias-record"></a>CNAME eller Alias post

En CNAME-post knytter et *bestemt* domæne, som **contoso.com** eller **www.contoso.com**, til et vedtaget domænenavn. I dette tilfælde canonical domænenavnet er den **[Mitprogr] .cloudapp .net** domænenavn for dit Azure hostet programmet på computeren. Når oprettet, CNAME opretter et alias for den **[Mitprogr] .cloudapp .net**. CNAME-posten oversætter til IP-adressen på dit **[Mitprogr] .cloudapp .net** service automatisk, så hvis IP-adressen på skytjenesten ændres, ikke behøver du foretage dig noget.

> [AZURE.NOTE]
> Nogle domæneregistratorer kun muligt at tilknytte underdomæner, når du bruger en CNAME-post, som www.contoso.com og ikke rod navne, som contoso.com. Se dokumentationen til fra din domæneregistrator, [Wikipedia posten på CNAME-post](http://en.wikipedia.org/wiki/CNAME_record)eller [IETF domænenavne - implementering og specifikation af](http://tools.ietf.org/html/rfc1035) dokumentet kan finde flere oplysninger om CNAME-poster.

### <a name="a-record"></a>En post

En *A* -post knytter et domæne, som **contoso.com** eller **www.contoso.com**, *eller et jokertegn domæne* såsom ** \*. contoso.com**, til en IP-adresse. Hvis det er en Azure skybaseret tjeneste, den virtuelle IP-adresse til tjenesten. Så den primære fordel ved en A-post over en CNAME-post er, at du kan have én post, der bruger et jokertegn, f.eks \* **. contoso.com**, som skal håndtere anmodninger om flere underdomæner som **mail.contoso.com**, **login.contoso.com**eller **www.contso.com**.

> [AZURE.NOTE]
> Da en A-post er knyttet til en statisk IP-adresse, ikke kan der automatisk fortolkes ændringer til IP-adressen på dit skybaseret tjeneste. IP-adresse bruges af din skybaseret tjeneste, der er allokeret første gang du installerer på en tom slot (enten fremstilling eller midlertidige.) Hvis du sletter installationen for slotten, IP-adressen er udgivet af Azure og alle fremtidige installationer til pladsen kan tildeles en ny IP-adresse.
>
> IP-adressen for en given installation slot (fremstilling eller midlertidige) bevares praktisk, når du skifter mellem midlertidige og fremstilling installationer eller udføre en lokal opgradering af en eksisterende installation. Du kan finde flere oplysninger om at udføre disse handlinger, se, [hvordan du administrerer skytjenester](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Tilføje en CNAME-post til dit eget domæne

Hvis du vil oprette en CNAME-post, skal du tilføje en ny post i tabellen DNS for dit brugerdefinerede domæne ved hjælp af værktøjerne, der leveres af din domæneregistrator. Hver registrator har en ligner, men nogle lidt andre metode til at angive en CNAME-post, men begreberne er de samme.

1. Benyt en af disse metoder til at finde den **. cloudapp.net** domænenavn, der er tildelt til din skybaseret tjeneste.

    * Log på [Azure-portalen], Vælg din skybaseret tjeneste, se afsnittet **Essentials** og derefter finde posten **Webstedets URL-adresse** .

        ![Oversigt over sektion, der viser webstedets URL-adresse][csurl]
            
        **ELLER**
  
    * Installere og konfigurere [Azure Powershell](../powershell-install-configure.md), og derefter bruge følgende kommando:

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Gem det domænenavn, der bruges i den URL-adresse, der returneres af begge metoder, som du skal bruge den, når du opretter en CNAME-post.

1.  Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**.

2.  Find nu, hvor du kan markere eller skrive CNAME'S. Du kan være nødvendigt at vælge posttypen fra en slip ned, eller gå til en side, avancerede indstillinger. Du skal se efter ordene **CNAME** **Alias**eller **underdomæner**.

3.  Du skal også angive domæne eller underdomæne alias for CNAME, såsom **www** , hvis du vil oprette et alias for **www.customdomain.com**. Hvis du vil oprette et alias for roddomænet, kan det være angivet som den '**@**' symbolet i din domæneregistrator DNS-værktøjer.

4. Derefter skal angive du et vedtaget værtsnavn, som er dit program **cloudapp.net** domæne i dette tilfælde.

For eksempel videresender følgende CNAME-post al trafik fra **www.contoso.com** til **contoso.cloudapp.net**, det brugerdefinerede domænenavn for dit udløst program:

| Alias/Host name/underdomæne | Vedtaget domæne     |
| ------------------------- | -------------------- |
| www                       | Contoso.cloudapp.NET |

> [AZURE.NOTE]
En besøgende **www.contoso.com** ser aldrig værten SAND (contoso.cloudapp.net), så processen viderestilling er usynlige til slutbrugeren.

> Eksemplet ovenfor gælder kun for trafik på **www** underdomæne. Da du ikke bruge jokertegn i CNAME-poster, skal du oprette én CNAME for hvert domæne/underdomæne. Hvis du vil dirigere trafik fra underdomæner, f.eks *. contoso.com, til din cloudapp.net adresse, du kan konfigurere en * *Omdirigere URL-adressen* * eller * *URL-adressen Videresend** post på dine DNS-indstillinger, eller oprette en A-post.


## <a name="add-an-a-record-for-your-custom-domain"></a>Tilføje en A-post for dit brugerdefinerede domæne

Hvis du vil oprette en A-post, skal du først finde den virtuelle IP-adresse på skybaseret tjeneste. Tilføj derefter en ny post i tabellen DNS for dit brugerdefinerede domæne ved hjælp af værktøjerne, der leveres af din domæneregistrator. Hver registrator har en ligner, men nogle lidt andre metode til at angive en A-post, men begreberne er de samme.

1. Brug en af følgende metoder til at få IP-adressen på dit skybaseret tjeneste.

    * Logon til [Azure-portalen], Vælg din skybaseret tjeneste, se afsnittet **Essentials** og find den **offentlige IP-adresser** post.

        ![Oversigt over sektion, der viser VIP][vip]

        **ELLER**

    * Installere og konfigurere [Azure Powershell](../powershell-install-configure.md), og derefter bruge følgende kommando:

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Gem IP-adressen, som du skal bruge den, når du opretter en A-post.

1.  Log på DNS-udbyderens websted, og gå til siden til at administrere DNS. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**.

2.  Find nu, hvor du kan markere eller skrive en post. Du kan være nødvendigt at vælge posttypen fra en slip ned, eller gå til en side, avancerede indstillinger.

3. Vælg eller Angiv domæne eller underdomæne, der anvender en A-post. For eksempel vælge **www** , hvis du vil oprette et alias for **www.customdomain.com**. Hvis du vil oprette et jokertegn post for alle underdomæner, skal du angive '__*__'. Dette vil dække alle underdomæner som **mail.customdomain.com**, **login.customdomain.com**og **www.customdomain.com**.

    Hvis du vil oprette en A-post for roddomænet, kan det være angivet som den '**@**' symbolet i din domæneregistrator DNS-værktøjer.

4. Angiv din skytjeneste IP-adresse i det angivne felt. Dette knytter posten domain bruges i posten med implementeringen af denne skyen service IP-adresse.

For eksempel følgende en post videresender al trafik fra **contoso.com** til **137.135.70.239**, IP-adressen på dit udløst program:

| Host name/underdomæne | IP-adresse     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


I dette eksempel vises, oprette en A-post for roddomænet. Hvis du vil oprette et jokertegn post dækker alle underdomæner, skal du angive '__*__"som underdomænet.

>[AZURE.WARNING]
>IP-adresser i Azure er dynamiske som standard. Du vil sandsynligvis [reserverede IP-adresse](../virtual-network/virtual-networks-reserved-public-ip.md) bruges til at sikre, at din IP-adresse ikke ændres.

## <a name="next-steps"></a>Næste trin

* [Sådan administreres Cloud Services](cloud-services-how-to-manage.md)
* [Hvordan du knytter CDN indhold til et brugerdefineret domæne](../cdn/cdn-map-content-to-custom-domain.md)
* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure-portal.md).
* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy-portal.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure-portalen]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 