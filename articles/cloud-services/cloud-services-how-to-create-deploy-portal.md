<properties
    pageTitle="Hvordan du kan oprette og installere en skybaseret tjeneste | Microsoft Azure"
    description="Lær at oprette og installere en skybaseret tjeneste, ved hjælp af portalen Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hvordan du kan oprette og installere en skybaseret tjeneste

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-create-deploy-portal.md)
- [Azure klassisk portal](cloud-services-how-to-create-deploy.md)

Portalen Azure er der to metoder til at oprette og installere en skybaseret tjeneste: *Hurtig oprettelse* og *Oprette brugerdefinerede*.

I denne artikel beskrives det, hvordan du kan bruge metoden Hurtig oprettelse til at oprette en ny tjeneste i skyen og derefter bruge **overføre** til at overføre og implementere en skybaseret tjeneste pakke i Azure. Når du bruger denne metode, gør portalen Azure tilgængelige praktisk links for at fuldføre alle krav undervejs. Hvis du er klar til at installere skybaseret tjeneste, når du opretter den, kan du gøre begge ad gangen ved hjælp af brugerdefinerede oprette.

> [AZURE.NOTE] Hvis du planlægger at udgive dit skybaseret tjeneste fra Visual Studio Team Services (VSTS), Brug Hurtig oprettelse, og derefter konfigurere VSTS udgivelse fra den Azure Hurtig start eller dashboard. Du kan finde flere oplysninger, se [Fortløbende levering til Azure ved hjælp af Visual Studio Team Services][TFSTutorialForCloudService], eller se Hjælp til siden **Hurtig Start** .

## <a name="concepts"></a>Begreber
Tre komponenter, der kræves for at installere et program som en skybaseret tjeneste i Azure:

- **Tjenestedefinitionen**  
  Skybaseret tjeneste definitionsfil (.csdef) definerer service modellen, herunder antallet af roller.

- **Tjenestekonfiguration**  
  Skybaseret tjeneste konfigurationsfil (.cscfg) indeholder indstillinger for søgekonfiguration til skyen service og individuelle roller, herunder antallet af forekomster af rollen.

- **Servicepakke**  
  Servicepakke (.cspkg) indeholder programkoden og konfigurationer og definitionsfil tjeneste.

Du kan læse mere om disse og hvordan du opretter en pakke [her](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Forberede din app
Før du kan installere en skybaseret tjeneste, skal du oprette servicepakke skyen (.cspkg) fra din programkode og en skybaseret tjeneste konfigurationsfil (.cscfg). Azure SDK indeholder værktøjer til forberedelse af filerne påkrævet installation. Du kan installere SDK fra [Azure Downloads](https://azure.microsoft.com/downloads/) -siden, på det sprog, du foretrækker at udvikle din programkode.

Tre skyen servicefunktioner kræver specielle konfigurationer, før du eksporterer en servicepakke:

- Hvis du vil installere en skybaseret tjeneste bruger, der Secure Sockets Layer (SSL) til kryptering af data, [konfigurere dit program](cloud-services-configure-ssl-certificate-portal.md#modify) til SSL.

- Hvis du vil konfigurere Fjernskrivebord forbindelser til forekomster af rolle, [konfigurere rollerne](cloud-services-role-enable-remote-desktop.md) for Remote Desktop. Dette kan kun udføres i portalen klassisk.

- Hvis du vil konfigurere detaljeret overvågning for skybaseret tjeneste, skal du aktivere Azure diagnosticering for skytjenesten. *Minimale overvågning* (standard overvågning niveau) bruger tællere i ydeevne indsamlede fra operativsystemerne host for rolle forekomster (virtuelle maskiner). *Detaljeret overvågning* indsamler flere statistikker baseret på ydelsesdata i rolle forekomster til at aktivere nærmere analyse af problemer, der opstår under behandling af programmet. For at finde ud af, hvordan du aktiverer Azure diagnosticering skal du se [aktivere diagnosticering i Azure](cloud-services-dotnet-diagnostics.md).

Hvis du vil oprette en skybaseret tjeneste med installationer af web roller eller arbejder roller, skal du [oprette servicepakke](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Inden du går i gang

- Hvis du ikke har installeret Azure SDK, skal du klikke på **Installer Azure SDK** for at åbne [siden Azure overførsler](https://azure.microsoft.com/downloads/), og derefter kan du hente SDK for det sprog, du foretrækker at udvikle din kode. (Du skal have mulighed for at gøre dette senere).

- Hvis en hvilken som helst rolle forekomster kræver et certifikat, har oprettet certifikater. Skytjenester kræver en .pfx-fil med en privat nøgle. [Du kan overføre certifikater til Azure]() som du opretter og installerer skytjenesten.

- Hvis du planlægger at implementere skytjenesten til en forbindelse gruppe, oprette forbindelse gruppe. Du kan bruge en forbindelse gruppe skal installeres skybaseret tjeneste og andre Azure tjenester til den samme placering i et område. Du kan oprette gruppen forbindelse i området **netværk** i Azure klassisk portalen, på siden **forbindelse grupper** .


## <a name="create-and-deploy"></a>Oprette og installere

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Ny > virtuelle maskiner**, og Rul ned til, og klik på **Skybaseret tjeneste**.

    ![Publicere dit skytjeneste](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Nederst på oplysningssiden, der vises, skal du klikke på **Opret**. 
4. Angiv en værdi for **DNS-navn**i bladet nye **Skybaseret tjeneste** .
5. Opret en ny **Ressourcegruppe** eller Vælg et eksisterende dokument.
6. Vælg en **placering**.
7. Klik på **pakken**. Dette åbner bladet **overføre en pakke** . Udfyld de obligatoriske felter.  

     Hvis et af dine roller indeholder en enkelt forekomst, sikre, at **installere selvom en eller flere roller indeholder en enkelt forekomst** er markeret.

8. Sørg for, at **Start installation** er markeret.
9. Klik på **OK** , lukkes bladet **overføre en pakke** .
10. Hvis du ikke har en hvilken som helst certifikater for at tilføje, klik på **Opret**.

    ![Publicere dit skytjeneste](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Overføre et certifikat

Hvis din installationspakke blev [konfigureret til at bruge certifikater](cloud-services-configure-ssl-certificate-portal.md#modify), kan du overføre certifikatet nu.

1. Vælg **certifikater**, og vælg SSL-certifikat .pfx-fil på bladet **Tilføj certifikater** , og derefter angive den **adgangskode** for certifikatet,
2. Klik på **Vedhæft certifikat**, og klik derefter på **OK** i bladet **Tilføj certifikater** .
3. Klik på **Opret** på bladet **Skybaseret tjeneste** . Når installationen er nået frem til **klar** status, kan du fortsætte til næste trin.

    ![Publicere dit skytjeneste](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>Bekræfte din installation blev fuldført

1. Klik på forekomsten af skyen.

    Status skal vise, at tjenesten er **kører**.

2. Klik på **Webstedets URL-adresse** for at åbne din skybaseret tjeneste i en webbrowser under **Essentials**.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Næste trin

* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure-portal.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name-portal.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage-portal.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate-portal.md).