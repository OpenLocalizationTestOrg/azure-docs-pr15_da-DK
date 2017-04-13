<properties 
    pageTitle="Almindelige skyen service administrationsopgaver | Microsoft Azure" 
    description="Lær, hvordan du administrerer skytjenester på portalen Azure. Disse eksempler bruger Azure portalen." 
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
    ms.date="08/02/2016"
    ms.author="adegeo"/>


# <a name="how-to-manage-cloud-services"></a>Sådan administreres Cloud Services

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-manage-portal.md)
- [Azure klassisk portal](cloud-services-how-to-manage.md)

Skybaseret tjeneste administreres i området **Cloud Services (klassisk)** i portalen Azure. I denne artikel beskrives nogle almindelige handlinger, skal du gøre mens du styrer dine skytjenester. Som indeholder opdatere, slette, skalering og fremme en faseinddelt installation for at fremstilling.

Du kan finde flere oplysninger om, hvordan du skalere skybaseret tjeneste er tilgængelig [her](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Sådan: opdatere en skybaseret tjeneste rolle eller installation

Hvis du vil opdatere programkode for skybaseret tjeneste, skal du bruge **opdatere** på bladet skybaseret tjeneste. Du kan opdatere en enkelt eller alle roller. Hvis du vil opdatere, kan du overføre en ny servicepakke eller tjeneste konfigurationsfil.

1. Vælg den skybaseret tjeneste, du vil opdatere [Azure-portalen][]. Dette trin åbner bladet skyen service forekomst.

2. Klik på knappen **Opdater** i bladet.

    ![Knappen Opdater](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Opdatere installationen med en ny tjeneste pakkefilen (.cspkg) og service konfigurationsfil (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Du kan også** opdatere etiketten installation og kontoen lagerplads. 

5. Hvis nogen roller har kun én forekomst af rolle, kan du vælge den **installation, selvom en eller flere roller indeholder en enkelt forekomst** til aktivering af opgraderingen til at fortsætte. 

    Azure kan kun garantere 99.95 procent tjenesten kører under en skybaseret tjenesteopdatering, hvis hver rolle har mindst to rolle forekomster (virtuelle maskiner). Et virtuelt behandler med to rolle forekomster klient-anmodninger, mens den anden er opdateret.

6. Markér **Start implementering** for at få opdateringen anvendes, når du er færdig med at overføre pakken.

7. Klik på **OK** for at begynde at opdatere tjenesten.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Sådan: Ombyt installationer rykke en faseinddelt installation for at fremstilling

Når du beslutter at installere en ny version af en skybaseret tjeneste, fase og teste din nye version i testmiljø din skybaseret tjeneste. Brug **Ombyt** til at skifte URL-adresser, som de to installationer er rettet og fremme en ny version til fremstilling. 

Du kan udskifte installationer fra siden **Cloud Services** eller dashboard.

1. Vælg den skybaseret tjeneste, du vil opdatere [Azure-portalen][]. Dette trin åbner bladet skyen service forekomst.

2. Klik på knappen **Ombyt** i bladet.

    ![Byt om cloud Services](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Følgende bekræftelsesmeddelelsen åbnes.

    ![Byt om cloud Services](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Når du har bekræftet oplysninger, skal du klikke på **OK** for at udskifte installationer.

    Installation Udskift sker hurtigt fordi det eneste, der ændrer er de virtuelle IP-adresser (VIPs) for installationer.

    Hvis du vil gemme Beregn omkostninger, kan du slette den midlertidige installation, når du har bekræftet, at din installationen produktionsmiljø fungerer som forventet.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Sådan: sammenkæde en ressource til en skybaseret tjeneste

Portalen Azure ikke sammenkæde ressourcer som den aktuelle Azure klassisk portal gør. I stedet installere yderligere ressourcer i samme ressourcegruppe bruges af Skytjenesten.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Sådan: slette installationer og en skybaseret tjeneste

Før du kan slette en skybaseret tjeneste, skal du slette alle eksisterende installation.

Hvis du vil gemme Beregn omkostninger, kan du slette den midlertidige installation, når du har bekræftet, at din installationen produktionsmiljø fungerer som forventet. Du er faktureret for Beregn omkostninger for udløst rolle forekomster, der er stoppet.

Brug følgende fremgangsmåde til at slette en installation eller en skybaseret tjeneste. 

1. Vælg den skybaseret tjeneste, du vil slette på [Azure-portalen][]. Dette trin åbner bladet skyen service forekomst.

2. Klik på knappen **Slet** i bladet.

    ![Byt om cloud Services](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Du kan slette hele skytjenesten ved at markere **skytjeneste og dens installationer** eller vælge den **produktionsmiljø** eller den **midlertidige installation**.

    ![Byt om cloud Services](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. Klik på knappen **Slet** nederst.

5. Hvis du vil slette skytjenesten, skal du klikke på **Slet skybaseret tjeneste**. Klik på **Ja**i bekræftelsesdialogboksen.

> [AZURE.NOTE]
> Når en skybaseret tjeneste slettes, og detaljeret overvågning er konfigureret, skal du slette dataene manuelt fra kontoen lagerplads. Du kan finde oplysninger om, hvor du kan finde målepunkter tabellerne [i denne](cloud-services-how-to-monitor.md) artikel.

[Azure-portalen]: https://portal.azure.com

## <a name="next-steps"></a>Næste trin

* [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure-portal.md).
* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy-portal.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name-portal.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate-portal.md).