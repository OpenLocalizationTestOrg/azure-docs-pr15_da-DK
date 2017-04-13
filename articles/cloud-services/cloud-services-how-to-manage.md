<properties 
    pageTitle="Almindelige skyen service management opgaver (klassisk) | Microsoft Azure" 
    description="Lær, hvordan du administrerer skytjenester på portalen Azure klassisk." 
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
    ms.date="08/10/2016"
    ms.author="adegeo"/>





# <a name="how-to-manage-cloud-services"></a>Sådan administreres Cloud Services

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-manage-portal.md)
- [Azure klassisk portal](cloud-services-how-to-manage.md)

Du kan opdatere en tjeneste rolle eller en installation, fremme en faseinddelt installation for at fremstilling, sammenkæde ressourcer til skybaseret tjeneste, så du kan se Ressourceafhængigheder og tilpasse ressourcerne, der sammen og slette en skybaseret tjeneste eller en installation i området **Cloud Services** i portalen Azure klassisk.


## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Sådan: opdatere en skybaseret tjeneste rolle eller installation

Hvis du vil opdatere programkode for skybaseret tjeneste, du brug **opdatere** på dashboard, **Cloud Services** side eller **forekomster** side. Du kan opdatere en enkelt eller alle roller. Du skal overføre en ny tjeneste pakke og service konfigurationsfil.

1. Klik på **Opdater**i [Azure klassisk portal](https://manage.windowsazure.com/)på dashboard, **Cloud Services** side eller **forekomster** side.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Angiv et navn til at identificere installationen (for eksempel mycloudservice4) i **installation etiket**. Du finder installation etiketten under **Hurtig start** på dashboardet.

3. I **pakke**, skal du klikke på **Gennemse** for at overføre pakkefilen service (.cspkg).

4. I **konfiguration**, klikke på **Gennemse** til at overføre filen til konfiguration af service (.cscfg).

5. Vælg **alle** i **rolle**, hvis du vil opgradere alle roller i skytjenesten. Vælg den rolle, du vil opdatere for at udføre en enkelt rolle opdatering. Selvom du vælger en bestemt rolle til at opdatere, anvendes opdateringerne i konfigurationsfil service på alle roller.

6. Hvis opdateringen ændres antallet af roller eller størrelsen på en hvilken som helst rolle, skal du vælge afkrydsningsfeltet **Tillad Opdater, hvis rolle størrelser eller antallet af roller ændres** for at aktivere opdateringen fortsætte. 

    Vær opmærksom på, hvis du ændrer størrelsen på en rolle (det vil sige, størrelsen på en virtuel maskine, der er vært en forekomst af rolle) eller antallet af roller, hver rolle forekomst (virtuelt) skal være igen scannede og lokale data, går tabt.

7. Hvis nogen service roller har kun én forekomst af rolle, Vælg den **opdatere selvom en eller flere rolle indeholder en enkelt forekomst afkrydsningsfelt for** at aktivere opgraderingen kan fortsætte. 

    Azure kan kun garantere 99.95 procent tjenesten kører under en skybaseret tjenesteopdatering, hvis hver rolle har mindst to rolle forekomster (virtuelle maskiner). Der gør det muligt for et virtuelt til at behandle klient-anmodninger, mens den anden opdateres.

8. Klik på **OK** (markering) for at begynde at opdatere tjenesten.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Sådan: Ombyt installationer rykke en faseinddelt installation for at fremstilling

Brug **Ombyt** rykke en midlertidig installation af en skybaseret tjeneste til fremstilling. Når du beslutter at installere en ny version af en skybaseret tjeneste, kan du fase og teste din nye version i dit skyen service midlertidige miljø, mens dine kunder bruger den aktuelle udgave af fremstilling. Når du er klar til at hæve den nye version til fremstilling, kan du bruge **Ombyt** til at skifte de URL-adresser, som de to installationer er rettet. 

Du kan udskifte installationer fra siden **Cloud Services** eller dashboard.

1. Klik på **Cloud Services**i [Azure klassisk portal](https://manage.windowsazure.com/).

2. Klik på skytjenesten for at markere den på listen over skytjenester.

3. Klik på **Skift**.

    Følgende bekræftelsesmeddelelsen åbnes.

    ![Byt om cloud Services](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Når du har bekræftet oplysninger, skal du klikke på **Ja** for at udskifte installationer.

    Installation Udskift sker hurtigt fordi det eneste, der ændrer er de virtuelle IP-adresser (VIPs) for installationer.

    Hvis du vil gemme Beregn omkostninger, kan du slette installationen i det midlertidige miljø, når du er sikker på, at den nye produktionsmiljø fungerer som forventet.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Sådan: sammenkæde en ressource til en skybaseret tjeneste

For at vise din skyen tjenestens afhængigheder på andre ressourcer, kan du sammenkæde en forekomst af Azure SQL-Database eller en lagerplads til skybaseret tjeneste. Du kan oprette et link og fjerne sammenkædningen af ressourcer på siden **Sammenkædede ressourcer** og derefter overvåge deres brugen på dashboardet til skyen tjeneste. Hvis en sammenkædet lagerplads firma har overvågning slået til, kan du overvåge Samlet antal anmodninger på dashboardet til skyen tjeneste.

Bruge **linket** til at sammenkæde en ny eller eksisterende SQL-Database forekomst eller lagerplads konto til din skybaseret tjeneste. Du kan derefter skalere databasen sammen med rollen skybaseret tjeneste, der bruger det på siden **skala** . (En lagerplads konto skaleres automatisk som brugen øges.) Du kan finde flere oplysninger, se, [hvordan skalere en skybaseret tjeneste og sammenkædede ressourcer](cloud-services-how-to-scale.md). 

Du kan også overvåge, administrere og skalere databasen i noden **databaser** i portalen Azure klassisk. 

"Sammenkædning" en ressource i denne mening Forbind ikke din app til ressourcen. Hvis du opretter en ny database ved hjælp af **linket**, skal du føje strengene forbindelse til din programkode og derefter opgradere skytjenesten. Du skal også tilføje strenge, hvis din app anvender ressourcer i en sammenkædet lagerplads konto.

Følgende procedure beskriver, hvordan sammenkæde en ny forekomst af SQL-Database, installeres på en ny server i SQL-Database til en skybaseret tjeneste.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Sammenkæde en forekomst af SQL-Database til en skybaseret tjeneste

1. Klik på **Cloud Services**i [Azure klassisk portal](http://manage.windowsazure.com/). Klik derefter på navnet på skytjeneste til at åbne dashboard.

2. Klik på **sammenkædet ressourcer**.

    Siden **Sammenkædede ressourcer** åbnes.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Klik på **Link en ressource** eller **Link**.

    Guiden **Link** starter.

    ![Linket Side1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Klik på **Opret en ny ressource** eller **kæde en eksisterende ressource**.

5. Vælg typen ressourcer til at oprette et link. Klik på **SQL-Database**i [Azure klassisk portal](http://manage.windowsazure.com/). (Portalen Preview Azure klassisk understøtter ikke sammenkædning en lagerplads konto til en skybaseret tjeneste.)

6. For at fuldføre konfigurationen af databasen skal du følge instruktionerne i Hjælp til området **SQL-databaser** i portalen Azure klassisk.

    Du kan følge statussen for sammenkædningen i meddelelsesområdet.

    ![Status for sammenkædning](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Når sammenkædning er fuldført, kan du overvåge statussen for den sammenkædede ressource på dashboardet til skyen service. Finde oplysninger om skalering en sammenkædet SQL-Database, se, [hvordan skalere en skybaseret tjeneste og sammenkædede ressourcer](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>At fjerne sammenkædningen en sammenkædet ressource

1. Klik på **Cloud Services**i [Azure klassisk portal](http://manage.windowsazure.com/). Klik derefter på navnet på skytjeneste til at åbne dashboard.

2. Klik på **Sammenkædet ressourcer**, og klik på ressourcen.

3. Klik på **Ophæv**. Klik på **Ja** i bekræftelsesdialogboksen.

    Sammenkædningen en SQL-Database har ingen indflydelse på databasen eller programmets forbindelser til databasen. Du kan stadig administrere databasen i området **SQL-databaser** i portalen Azure klassisk.



## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Sådan: slette installationer og en skybaseret tjeneste

Før du kan slette en skybaseret tjeneste, skal du slette alle eksisterende installation.

For at gemme Beregn omkostninger, kan du slette dine midlertidige installation, når du har bekræftet, at din installationen produktionsmiljø fungerer som forventet. Du er fakturerede Beregn omkostninger for rolle forekomster, selvom en skybaseret tjeneste ikke kører.

Brug følgende fremgangsmåde til at slette en installation eller en skybaseret tjeneste. 

1. Klik på **Cloud Services**i [Azure klassisk portal](http://manage.windowsazure.com/).

2. Vælg skytjenesten, og klik derefter på **Slet**. (For at vælge en skybaseret tjeneste uden at åbne dashboard, klik et vilkårligt sted undtagen navn i posten skyen service).

    Hvis du har en installation i midlertidige eller fremstilling, får du vist en menu med valgmuligheder, der ligner den følgende, i bunden af vinduet. Før du kan slette skytjenesten, skal du slette alle eksisterende installationer.

    ![Slette Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Hvis du vil slette en installation, skal du klikke på **Slet produktionsmiljø** eller **Slet midlertidige installation**. Klik på **Ja**i bekræftelsesdialogboksen. 

4. Hvis du vil slette skytjenesten, Gentag trin 3, hvis det er nødvendigt at slette andre installationen.

5. Hvis du vil slette skytjenesten, skal du klikke på **Slet skybaseret tjeneste**. Klik på **Ja**i bekræftelsesdialogboksen.

> [AZURE.NOTE]
> Hvis detaljeret overvågning er konfigureret for din skybaseret tjeneste, sletter Azure ikke den overvågningsdata fra kontoen lagerplads når du sletter skytjenesten. Du skal slette data manuelt. Finde oplysninger om, hvor du kan finde tabellerne målepunkter "Sådan: Access detaljeret overvågningsdata uden for portalen Azure klassisk" i [Sådan skærm Cloud Services](cloud-services-how-to-monitor.md).

## <a name="next-steps"></a>Næste trin

 * [Generelle konfiguration af din skybaseret tjeneste](cloud-services-how-to-configure.md).
* Lær, hvordan du kan [installere en skybaseret tjeneste](cloud-services-how-to-create-deploy.md).
* Konfigurere et [brugerdefineret domænenavn](cloud-services-custom-domain-name.md).
* Konfigurere [ssl-certifikater](cloud-services-configure-ssl-certificate.md).
