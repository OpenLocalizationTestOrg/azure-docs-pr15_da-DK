## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Forberede til at godkende anmodninger om Azure Resource Manager

Du skal godkende alle de handlinger, du udfører på ressourcer ved hjælp af [Azure Resource Manager] [ lnk-authenticate-arm] med Azure Active Directory (AD). Den nemmeste måde at konfigurere dette er at bruge PowerShell eller Azure CLI.

Du skal installere [Azure PowerShell 1.0] [ lnk-powershell-install] eller en nyere version, før du fortsætter.

Nedenstående fremgangsmåde viser, hvordan du konfigurerer autentifikation af adgangskode til en AD-program ved hjælp af PowerShell. Kommandoerne kan køres i en standard PowerShell-session.

1. Log på for at abonnementet Azure ved hjælp af følgende kommando:

    ```
    Login-AzureRmAccount
    ```

2. Noter af **TenantId** og **SubscriptionId**. Du skal bruge dem senere.

3. Opret et nyt Azure Active Directory-program ved hjælp af følgende kommando, erstatter sted indehavere:

    - **{Navn}:** et visningsnavn for din ansøgning som **MySampleApp**
    - **{URL til hjemmeside}:** URL-adressen på startsiden for din app som **http://mysampleapp/home**. Denne URL-adresse, behøver ikke at pege på et virkeligt program.
    - **{Applikationsdatanavn}:** En entydig identifier som **http://mysampleapp**. Denne URL-adresse, behøver ikke at pege på et virkeligt program.
    - **{Adgangskode}:** En adgangskode, du vil bruge til at godkende med din app.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Noter **ApplicationId** for det program, du har oprettet. Du skal bruge dette senere.

5. Opret en ny service principal ved hjælp af følgende kommando, erstatter **{MyApplicationId}** med **ApplicationId** fra det forrige trin:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Opsætning af en rolletildeling, ved hjælp af følgende kommando, erstatter **{MyApplicationId}** med din **ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Du har nu oprettet det Azure AD-program, der giver dig mulighed at godkende fra dit brugerdefinerede C#-program. Du skal bruge følgende værdier senere i dette selvstudium:

- TenantId
- SubscriptionId
- ApplicationId
- Adgangskode

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
