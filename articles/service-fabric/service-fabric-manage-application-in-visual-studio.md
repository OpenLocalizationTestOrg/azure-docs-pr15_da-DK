<properties
   pageTitle="Administrere dine programmer i Visual Studio | Microsoft Azure"
   description="Brug Visual Studio til at oprette, udvikle, pakke, installere og foretage fejlfinding af din Service-strukturen programmer og -tjenester."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Brug Visual Studio til at forenkle skrivning og administration af programmerne Service-strukturen

Du kan administrere dine Azure Service strukturen programmer og tjenester gennem Visual Studio. Når du har [oprettet dit udviklingsmiljø](service-fabric-get-started.md), kan du bruge Visual Studio til at oprette Service-strukturen programmer, tilføje tjenester eller pakke, Registrer og installerer programmer i din lokale udvikling klynge.

## <a name="deploy-your-service-fabric-application"></a>Installere Service-strukturen-program

Som standard kombinerer implementere et program følgende trin i en enkelt handling:

1. Oprette programpakken
2. Overførsel af programmet pakke til store på billede
3. Registrere programtypen
4. Fjerne en kører forekomster af tjenesteprogrammer
5. Oprette en ny forekomst af programmet

I Visual Studio, at trykke på **F5** også installere programmet og vedhæfte fejlfindingsværktøjet til alle forekomster af tjenesteprogrammer. Du kan bruge **Ctrl + F5** til at installere et program uden fejlfinding, eller du kan udgive til en lokal eller ekstern klynge ved hjælp af Publicer profil. Du kan finde flere oplysninger [Publicer et program til en ekstern klynge ved hjælp af Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Programmet fejlsikret tilstand

Som standard Visual Studio fjerner eksisterende forekomster af din programtype, når du stopper fejlfinding eller (Hvis du har installeret appen uden fejlfindingsværktøjet), når du Geninstaller programmet. Det er tilfældet, fjernes alle programmets data. Under fejlfinding lokalt, kan du beholde data, som du allerede har oprettet, når du tester en ny version af programmet, du vil beholde programmet kører eller ønskede efterfølgende fejlfinding sessioner for at opgradere programmet. Visual Studio Service-strukturen Tools giver en egenskab, der kaldes **Tilstanden fejlfinding af**, hvilke objekter om **F5** skal du fjerne programmet, holde det program, der kører, når en fejlfindingssession med slutter eller aktivere programmet til at blive opgraderet på efterfølgende fejlfinding sessioner, i stedet for fjernet og genaktiveres.

#### <a name="to-set-the-application-debug-mode-property"></a>Indstille egenskaben fejlfindingstilstand

1. Vælg **Egenskaber** programmet projektets genvejsmenuen (eller tryk på tasten **F4** ).
2. Indstil egenskaben **Fejlfinding af tilstand** i vinduet **Egenskaber** .

    ![Angive egenskaben applikation fejlfinding tilstand][debugmodeproperty]

Dette er de tilgængelige **Fejlfinding af tilstanden** indstillinger.

1. **Automatisk opgradering**: programmet fortsætter med at køre, når der afsluttes sessionen med fejlfinding. Næste **F5** behandler installationen som en opgradering ved hjælp af uovervågede automatisk tilstand til hurtigt for at opgradere programmet til en nyere version med en dato føjet. Opgraderingsprocessen bevarer alle data, du har angivet i en tidligere sessionen med fejlfinding.

2. **Holde program**: programmet bevarer kører i klyngen, når afsluttes sessionen med fejlfinding. På den næste **F5** programmet fjernes, og den indbyggede programmet installeres til klyngen.

3. **Fjerne programmet** bevirker, at programmet skal fjernes, når der afsluttes sessionen med fejlfinding.

For **Automatisk opgradere** data bevares ved at anvende programmet opgradering funktionerne i tjenesten strukturen, men den er indstillet til at optimere for ydeevnen i stedet for sikkerhed. Du kan finde flere oplysninger om at opgradere programmer, og hvordan du kan udføre en opgradering i et reelt tal miljø, se [Service-strukturen programmet opgradere](service-fabric-application-upgrade.md).

![Eksempel på ny version af programmet med dato, der er tilføjet][preservedata]

>[AZURE.NOTE] Denne egenskab findes ikke før version 1.1 af Service-strukturen Tools til Visual Studio. Før du 1.1, skal du bruge egenskaben **Bevare Data på begynder** for at opnå den samme funktionsmåde. Indstillingen "Holde programmet" blev introduceret i version 1.2 af Service-strukturen Tools til Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Tilføj en tjeneste til dit Service-strukturen program

Du kan tilføje nye tjenester til dit program du udvider funktionaliteten.  Tilføj tjenesten via menupunktet **Ny strukturen tjeneste …** for at sikre, at tjenesten er inkluderet i din programpakke.

![Tilføje en ny tjeneste strukturen i dit program][newservice]

Vælg en tjeneste-strukturen projekttype kan føjes til dit program, og Angiv et navn til tjenesten.  Se [vælge en ramme for din tjeneste](service-fabric-choose-framework.md) til at afgøre, hvilken tjenestetype til brug.

![Vælg typen af project-strukturen Service kan føjes til dit program][addserviceproject]

Den nye tjeneste, føjes til din løsning og eksisterende programpakke. Tjenestereferencer og en standardforekomst service, føjes til programmanifestet. Tjenesten er oprettet og næste gang du installerer programmet i gang.

![Den nye tjeneste, føjes til din progammanifest][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pakke programmets Service-strukturen

Du skal installere programmet og tjenester til en klynge, skal du oprette en programpakke.  Pakken arrangerer programmanifestet, service manifest(s) og andre nødvendige filer i et bestemt layout.  Visual Studio konfigurerer og administrerer pakken i programmet projektets mappe, i mappen 'pkg'.  Klikke på **pakken** i kontekstmenuen **programmet** opretter eller opdaterer pakken programmet på computeren.  Du vil gøre dette, hvis du installerer programmet ved hjælp af brugerdefinerede PowerShell-scripts.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Fjerne programmer og programmet typer med skyen Stifinder

Du kan udføre grundlæggende klynge management handlinger fra i Visual Studio i skyen Explorer, som du kan starte fra menuen **Vis** . Du kan for eksempel slette programmer og klargøring for programmet typer på lokale eller en fjerndatabase klynger.

![Fjern et program](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Se mere omfattende klynge management funktionalitet, [visualisere din klynge med tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

- [Tjenesten strukturen programmet model](service-fabric-application-model.md)
- [Tjenesten strukturen programmet installation](service-fabric-deploy-remove-applications.md)
- [Administrere programmets parametre for flere miljøer](service-fabric-manage-multiple-environment-app-configuration.md)
- [Fejlfinding programmets Service-strukturen](service-fabric-debugging-your-application.md)
- [Visualisere din klynge ved hjælp af tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
