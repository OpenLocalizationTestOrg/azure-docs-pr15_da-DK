1. Log på på [Azure-portalen].

2. Klik på **+ Ny** > **Web + Mobile** > **Mobile-App**, Angiv derefter et navn på din Mobile-App backend-version.

3. Vælg en eksisterende ressourcegruppe til **Ressourcegruppe**, eller Opret en ny (med det samme navn som din app). 
 
    Du kan vælge en anden App Service-plan eller oprette en ny. Få mere at vide om App Services planer og hvordan du opretter en ny plan i en anden priser klasse og se [Azure App Service planer dybden oversigt](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)på den ønskede placering.

4. **App-serviceaftale**til er standardplanen (i [Standard niveau](https://azure.microsoft.com/pricing/details/app-service/)) markeret. Du kan også vælge en anden plan, eller [Opret en ny](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Indstillinger for den App-serviceaftale bestemmer [placering, funktioner, omkostninger og beregne ressourcer, der](https://azure.microsoft.com/pricing/details/app-service/) er knyttet til din app. 

    Når du beslutter dig for planen, skal du klikke på **Opret**. Dette opretter Mobile-App backend-version. 
    
6. Klik på **Hurtig start** i bladet **Indstillinger** for den nye Mobile-App back end-> din klient app platform > **forbinde en database**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Klik på **SQL-Database**i bladet **Tilføj dataforbindelse**  > **Opret en ny database**, Skriv databasen **navn**, Vælg et priser trin, og klik derefter på **Server**.  Du kan genbruge den nye database. Hvis du allerede har en database på samme placering, kan du i stedet vælge **Brug en eksisterende database**. Brug af en database på en anden placering anbefales ikke på grund af båndbredde omkostninger og højere ventetid.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Skriv et entydigt servernavn i feltet **servernavn** i bladet **nye server** , giver en logon og adgangskode, markere **Tillad azure services til at få adgang til server**, og klik på **OK**. Dette opretter den nye database.

9. Tilbage i bladet **Tilføj dataforbindelse** , skal du klikke på **forbindelsesstreng**, Skriv værdierne logon og adgangskode til databasen og klik på **OK**. Vent et par minutter til databasen til at være installeret, før du fortsætter.

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com/
