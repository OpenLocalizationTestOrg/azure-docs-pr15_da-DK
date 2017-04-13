<properties
    pageTitle="Hvordan du kan oprette og installere en skybaseret tjeneste | Microsoft Azure"
    description="Lær at oprette og installere en skybaseret tjeneste, ved hjælp af metoden Hurtig oprettelse i Azure."
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
    ms.date="09/06/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hvordan du kan oprette og installere en skybaseret tjeneste

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-create-deploy-portal.md)
- [Azure klassisk portal](cloud-services-how-to-create-deploy.md)

Azure klassisk portalen er der to metoder til at oprette og installere en skybaseret tjeneste: **Hurtig oprettelse** og **Oprette brugerdefinerede**.

Dette emne forklares det, hvordan du kan bruge metoden Hurtig oprettelse til at oprette en ny tjeneste i skyen og derefter bruge **overføre** til at overføre og implementere en skybaseret tjeneste pakke i Azure. Når du bruger denne metode, gør portalen Azure klassisk tilgængelige praktisk links for at fuldføre alle krav undervejs. Hvis du er klar til at installere skybaseret tjeneste, når du opretter den, kan du gøre begge ad gangen ved hjælp af **Brugerdefinerede oprette**.

> [AZURE.NOTE] Hvis du planlægger at udgive dit skybaseret tjeneste fra Visual Studio Team Services (VSTS), Brug Hurtig oprettelse, og derefter konfigurere VSTS udgivelse fra **Hurtig Start** eller dashboard. Du kan finde flere oplysninger, se [Fortløbende levering til Azure ved hjælp af Visual Studio Team Services][TFSTutorialForCloudService], eller se Hjælp til siden **Hurtig Start** .

## <a name="concepts"></a>Begreber
Tre komponenter er påkrævet for at installere et program som en skybaseret tjeneste i Azure:

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

- Hvis du vil installere en skybaseret tjeneste bruger, der Secure Sockets Layer (SSL) til kryptering af data, [konfigurere dit program](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) til SSL.

- Hvis du vil konfigurere Fjernskrivebord forbindelser til forekomster af rolle, [konfigurere rollerne](cloud-services-role-enable-remote-desktop.md) for Remote Desktop.

- Hvis du vil konfigurere detaljeret overvågning for skybaseret tjeneste, skal du aktivere Azure diagnosticering for skytjenesten. *Minimale overvågning* (standard overvågning niveau) bruger tællere i ydeevne indsamlede fra operativsystemerne host for rolle forekomster (virtuelle maskiner). "Detaljeret overvågning * indsamler flere statistikker baseret på ydelsesdata i rolle forekomster til at aktivere nærmere analyse af problemer, der opstår under behandling af programmet. For at finde ud af, hvordan du aktiverer Azure diagnosticering skal du se [Aktivere diagnosticering i Azure](cloud-services-dotnet-diagnostics.md).

Hvis du vil oprette en skybaseret tjeneste med installationer af web roller eller arbejder roller, skal du [oprette servicepakke](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Inden du går i gang

- Hvis du ikke har installeret Azure SDK, skal du klikke på **Installer Azure SDK** for at åbne [siden Azure overførsler](https://azure.microsoft.com/downloads/), og derefter kan du hente SDK for det sprog, du foretrækker at udvikle din kode. (Du skal have mulighed for at gøre dette senere).

- Hvis en hvilken som helst rolle forekomster kræver et certifikat, har oprettet certifikater. Skytjenester kræver en .pfx-fil med en privat nøgle. Du kan [overføre certifikater til Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) , når du opretter og installere skytjenesten.

- Hvis du planlægger at implementere skytjenesten til en forbindelse gruppe, oprette forbindelse gruppe. Du kan bruge en forbindelse gruppe skal installeres skybaseret tjeneste og andre Azure tjenester til den samme placering i et område. Du kan oprette gruppen forbindelse i området **netværk** i Azure klassisk portalen, på siden **forbindelse grupper** .


## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Sådan: oprette en skybaseret tjeneste, ved hjælp af Hurtig oprettelse

1. Klik på **Ny**i [Azure klassisk portal](http://manage.windowsazure.com/)>**beregne**>**Skytjeneste**>**Hurtig oprettelse**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Angiv et navn på underdomæne bruge i offentlig URL-adresse for at få adgang til din skybaseret tjeneste i fremstilling installationer i **URL-adresse**. URL-formatet til fremstilling installationer: http://*myURL*. cloudapp.net.

3. I **område eller forbindelse gruppe**, skal du vælge den geografiske område eller forbindelse gruppe til at udrulle skytjenesten til. Vælg en forbindelse gruppe, hvis du vil installere skybaseret tjeneste på samme placering, som andre Azure tjenester i et område.

4. Klik på **Opret skybaseret tjeneste**.

    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

    Du kan overvåge statussen for processen i meddelelsesområdet nederst i vinduet.

    Området **Cloud Services** åbnes med den nye skybaseret tjeneste, der vises. Når status ændres til oprettet, er oprettelse af skybaseret tjeneste fuldført.

    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Sådan: overføre et certifikat for en skybaseret tjeneste

1. [Azure klassisk portal](http://manage.windowsazure.com/), skal du klikke på **Cloud Services**, klik på navnet på skytjenesten og klik derefter på **certifikater**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Klik på enten **overføre et certifikat** eller **overføre**.

3. I **filen**skal du klikke på **Gennemse** for at markere certifikatet (.pfx-fil).

4. Angiv den private nøgle til certifikatet i feltet **adgangskode**.

5. Klik på **OK** (markering).

    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

    Du kan se status for overførslen i meddelelsesområdet vist nedenfor. Når overførslen er fuldført, føjes certifikatet til tabellen. Klik på OK for at lukke meddelelsen i meddelelsesområdet.

    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Sådan: installere en skybaseret tjeneste

1. Klik på **Cloud Services**i [Azure klassisk portalen](http://manage.windowsazure.com/), klik på navnet på skytjenesten, og klik **Dashboard**.

2. Klik på enten **overføre et nyt produktionsmiljø** eller **overføre**.

3. Angiv et navn til den nye installation – for eksempel MyCloudServicev4 **installation etiket**.

3. I **pakke**, klikke på **Gennemse** til at vælge service pakkefilen (.cspkg) til brug.

4. Klikke på **Gennemse** til at vælge tjenestens Konfigurer fil (.cscfg) til brug i **konfigurationen**.

5. Hvis skytjenesten omfatter en hvilken som helst roller med kun én forekomst, Markér afkrydsningsfeltet **installere selvom en eller flere roller indeholder en enkelt forekomst** for at aktivere installationen fortsætte.

    Azure kan kun garantere 99.95 procent adgang til tjenesten skyen under reparation og vedligeholdelse opdateringer, hvis hver rolle har mindst to forekomster. Hvis det er nødvendigt, kan du tilføje yderligere rolle forekomster på siden **skala** , når du installerer skytjenesten. Du kan finde yderligere oplysninger finder [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

6. Klik på **OK** (markering) for at starte den skybaseret tjeneste installation.

    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

    Du kan overvåge statussen for distributionen i meddelelsesområdet. Klik på OK for at skjule meddelelsen.

    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Bekræfte din installation blev fuldført

1. Klik på **Dashboard**.

    Status skal vise, at tjenesten er **kører**.

2. Klik på webstedets URL-adresse for at åbne din skybaseret tjeneste i en webbrowser under **Oversigt over**.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md
 
## <a name="next-steps"></a>Næste trin

* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name.md).
* [Administrer din skybaseret tjeneste](cloud-services-how-to-manage.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate.md).