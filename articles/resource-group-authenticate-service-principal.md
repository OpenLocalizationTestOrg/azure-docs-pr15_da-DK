<properties
   pageTitle="Oprette Azure service vigtigste med PowerShell | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure PowerShell til at oprette en Active Directory-program og service hovedstolen og give den adgang til ressourcer via rollebaseret adgangskontrol. Det viser, hvordan til at godkende program med en adgangskode eller et certifikat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Bruge Azure PowerShell til at oprette en tjeneste sikkerhedskonto få adgang til ressourcer

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Når du har et program eller script, som skal have adgang til ressourcer, ønsker du sandsynligvis ikke til at køre denne proces under dine egne legitimationsoplysninger. Du skal muligvis forskellige tilladelser, du vil for programmet, og du vil ikke programmet fortsætte med at bruge dine legitimationsoplysninger, hvis dine ansvarsområder ændrer. I stedet skal oprette du en identitet for det program, der omfatter legitimationsoplysninger og rolletildelinger. Hver gang appen kører, godkender selve med disse legitimationsoplysninger. Dette emne beskrives, hvordan du bruger [Azure PowerShell](powershell-install-configure.md) til at konfigurere alt, hvad du skal bruge til et program til at køre under sin egen legitimationsoplysninger og identitet.

Med PowerShell har du to muligheder for godkendelse af dit AD program:

 - adgangskode
 - certifikat

Dette emne viser, hvordan du bruger begge muligheder i PowerShell. Hvis du vil logge på Azure fra en programming ramme (sådanne Python, fonetisk eller Node.js), kan godkendelse af adgangskode være en god løsning. Inden du beslutter, om du vil bruge en adgangskode eller et certifikat, i afsnittet [eksempelprogrammer](#sample-applications) eksempler på godkendelse i forskellige rammer.

## <a name="active-directory-concepts"></a>Active Directory-begreber

I denne artikel kan oprette du to objekter - programmet Active Directory (AD) og tjenesten vigtigste. AD-programmet er den globale repræsentation af dit program. Den indeholder legitimationsoplysninger (et program-id og en adgangskode eller certifikat). Tjenesten vigtigste er den lokale repræsentation af dit program i et Active Directory. Den indeholder rolletildelingen. Dette emne fokuserer på et enkelt lejer-program, hvor programmet er beregnet til at køre i kun én organisation. Du bruger typisk enkelt lejer programmer for line of business programmer, der kører i din organisation. I et enkelt lejer program har du én AD app og én tjeneste hovedstolen.

Du måske spekulerer du over - Hvorfor skal jeg bruge begge objekter? Denne fremgangsmåde giver mere mening, når du finder flere lejer programmer. Du bruger typisk med flere lejer programmer for software som en tjeneste (SaaS) programmer, hvor dit program kører i mange forskellige abonnementer. Flere lejer programmer har du ét AD app og flere principper for tjenesten (ét i hver Active Directory, der giver adgang til appen). For at konfigurere et med flere lejer program skal du se [Developer's guide til godkendelse med Azure ressourcestyring API](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Nødvendige tilladelser

For at fuldføre dette emne, skal du har tilstrækkelige tilladelser i både din Azure Active Directory og abonnementet Azure. Du skal være specifikt, kan du oprette en app i Active Directory og tildele service hovedstolen til en rolle. 

I din Active Directory, skal kontoen være administrator (som **Global administrator** eller **Bruger administrator**). Hvis din konto er tildelt rollen **bruger** , skal du få en administrator til at ændre dine tilladelser.

I dit abonnement, kontoen skal have `Microsoft.Authorization/*/Write` access, der tildeles via [ejer](./active-directory/role-based-access-built-in-roles.md#owner) rollen eller [bruger Access](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) -administratorrolle. Hvis din konto er tildelt rollen **bidragyder** , modtager du en fejl, når du forsøger at tildele service hovedstolen til en rolle. Administratoren abonnement skal igen, give dig adgang.

Fortsæt nu til en sektion til [adgangskode](#create-service-principal-with-password) eller [certifikat](#create-service-principal-with-certificate) godkendelse.

## <a name="create-service-principal-with-password"></a>Oprette service vigtigste med adgangskode

I dette afsnit, skal udføre du disse trin for at:

- oprette AD-program med en adgangskode
- oprette hovedstolen service
- tildele rollen Læser til tjenesten hovedstolen

Du kan hurtigt udføre disse trin, kan du se følgende tre cmdletter. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Lad os gennemgå trinnene mere omhyggeligt for at sikre at du har forstået processen.

1. Log på din konto.

        Add-AzureRmAccount

1. Oprette et nyt Active Directory-program ved at give et vist navn, den URI, der beskriver dit program, de URI'er, der identificerer dine programmer og adgangskoden til dit program-id.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     For enkelt lejer programmer, er URI'er ikke godkendt.
     
     Hvis din konto ikke har de [nødvendige tilladelser](#required-permissions) på Active Directory, du får vist en fejlmeddelelse, der angiver "Authentication_Unauthorized" eller "Intet abonnement fundet i konteksten".

1. Undersøge det nye program-objekt. 

        $app
        
     Bemærk især egenskaben **ApplicationId** , som skal bruges til at oprette principper for tjenesten, rolletildelinger og indhente adgangstoken.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Oprette en tjeneste hovedstolen for dit program ved overførsel af program-id af Active Directory-programmet.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Give service vigtigste tilladelser på dit abonnement. I dette eksempel kan du tilføje tjenesten hovedstolen til rollen **læser** , som giver tilladelse til at læse alle ressourcer i abonnementet. Finde andre roller [RBAC: indbyggede roller](./active-directory/role-based-access-built-in-roles.md). Angiv **ApplicationId** , som du brugte, da oprettelse af programmet til parameteren **ServicePrincipalName** . 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Hvis din konto ikke har tilstrækkelige tilladelser til at tildele en rolle, får du vist en fejlmeddelelse. Vises en meddelelse om din konto **har ikke tilladelse til at udføre handlingen 'Microsoft.Authorization/roleAssignments/write' over område ' / abonnementer / {guid}'**. 

Det var det! AD-programmet og service hovedstolen er konfigureret. Næste afsnit viser, hvordan du logger på med legitimationsoplysningerne via PowerShell. Hvis du vil bruge legitimationsoplysninger i din kode programmet, kan du hoppe til [eksempler på programmer](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Angive legitimationsoplysninger via PowerShell

Nu skal du logge på som programmet til at udføre handlinger.

1. Oprette et **PSCredential** objekt, der indeholder dine legitimationsoplysninger ved at køre kommandoen **Get-legitimationsoplysninger** . Du skal bruge **ApplicationId** inden du kører denne kommando så skal du kontrollere, at du har, kan sætte den ind.

        $creds = Get-Credential

2. Du bliver bedt om at angive dine legitimationsoplysninger. Brug **ApplicationId** , som du brugte, da oprettelse af programmet for brugernavnet. Om adgangskoden, kan bruge den, du har angivet ved oprettelse af kontoen.

     ![Angiv legitimationsoplysninger](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Når du logger på som en tjeneste sikkerhedskonto, skal du give mappen lejer id til din AD app. En lejer er en forekomst af Active Directory. Hvis du kun har ét abonnement, kan du bruge:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Hvis du har mere end ét abonnement, skal du angive det abonnement, hvor din Active Directory er placeret. Se, [hvordan Azure abonnementer er knyttet til Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md), kan finde flere oplysninger.

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Log på som tjeneste hovedstolen, ved at angive, at denne konto er en tjeneste hovedstolen og ved at indsende objektet legitimationsoplysninger. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Du er nu godkendt som den primære tjeneste for Active Directory-program, du har oprettet.

### <a name="save-access-token-to-simplify-log-in"></a>Gemme adgangstoken for at forenkle logge ind

For at undgå angiver de vigtigste service-legitimationsoplysninger, hver gang de skal logge på, kan du gemme adgangstoken.

1. For at bruge den aktuelle adgangstoken i en nyere session skal du gemme profilen.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Åbn profilen, og Undersøg dens indhold. Bemærk, at den indeholder et adgangstoken. 
        
2. I stedet for manuelt at logge på igen, skal du blot indlæse profilen.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Adgangstoken udløber, så ved hjælp af en gemt profil fungerer kun for, så længe Tokenet er gyldig.
        
## <a name="create-service-principal-with-certificate"></a>Oprette service vigtigste med certifikat

I dette afsnit, skal udføre du disse trin for at:

- oprette et selvsigneret certifikat
- oprette AD-program med certifikatet
- oprette hovedstolen service
- tildele rollen Læser til tjenesten hovedstolen

Du kan hurtigt udføre disse trin med Azure PowerShell 2.0 på Windows 10 eller Windows Server 2016 Technical Preview, kan du se følgende cmdletter. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Lad os gennemgå trinnene mere omhyggeligt for at sikre at du har forstået processen. I denne artikel viser også, hvordan du udfører opgaverne, når der bruges tidligere versioner af Azure PowerShell eller operativsystemer.

### <a name="create-the-self-signed-certificate"></a>Oprette et selvsigneret certifikat

Versionen af PowerShell, der er tilgængelige med Windows 10 og Windows Server 2016 Technical Preview har en opdateret **Ny SelfSignedCertificate** -cmdlet til at oprette et selvsigneret certifikat. Tidligere operativsystemer har Cmdletten ny SelfSignedCertificate, men det giver ikke de parametre, bruges til dette emne. I stedet skal du importere et modul for at oprette certifikatet. Dette emne viser to metoder til at oprette det certifikat, der er baseret på operativsystemet, du har. 

- Hvis du har **Windows 10 eller Windows Server 2016 Technical Preview**, skal du køre følgende kommando for at oprette et selvsigneret certifikat: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Hvis du **ikke har Windows 10 eller Windows Server 2016 Technical Preview**, skal du hente den [selvsigneret certifikat generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) fra Microsoft Script Center. Udtrække indholdet og importere cmdlet du har brug for.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Opret derefter certifikatet.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Du har dit certifikat og kan fortsætte med at oprette din AD app.

### <a name="create-the-active-directory-app-and-service-principal"></a>Oprette tjenesten Active Directory app og vigtigste

1. Hent den nøgleværdi fra certifikatet.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Log på din Azure-konto.

        Add-AzureRmAccount

3. Oprette et nyt Active Directory-program ved at give et vist navn, den URI, der beskriver dit program, de URI'er, der identificerer dine programmer og adgangskoden til dit program-id.

     Hvis du har Azure PowerShell 2.0 (August 2016 eller nyere) Brug følgende cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Hvis du har Azure PowerShell 1.0, kan du bruge følgende cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    For enkelt lejer programmer, er URI'er ikke godkendt.
    
    Hvis din konto ikke har de [nødvendige tilladelser](#required-permissions) på Active Directory, du får vist en fejlmeddelelse, der angiver "Authentication_Unauthorized" eller "Intet abonnement fundet i konteksten".
        
    Undersøge det nye program-objekt. 

        $app

    Bemærk egenskaben **ApplicationId** , som skal bruges til at oprette principper for tjenesten, rolletildelinger og indhente access tokens.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Oprette en tjeneste hovedstolen for dit program ved overførsel af program-id af Active Directory-programmet.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Give service vigtigste tilladelser på dit abonnement. I dette eksempel kan du tilføje tjenesten hovedstolen til rollen **læser** , som giver tilladelse til at læse alle ressourcer i abonnementet. Finde andre roller [RBAC: indbyggede roller](./active-directory/role-based-access-built-in-roles.md). Angiv **ApplicationId** , som du brugte, da oprettelse af programmet til parameteren **ServicePrincipalName** .

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Hvis din konto ikke har tilstrækkelige tilladelser til at tildele en rolle, får du vist en fejlmeddelelse. Vises en meddelelse om din konto **har ikke tilladelse til at udføre handlingen 'Microsoft.Authorization/roleAssignments/write' over omfang ' / abonnementer / {guid}'**.

Det var det! AD-programmet og service hovedstolen er konfigureret. Næste afsnit viser, hvordan du logger på med certifikat via PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Angive certifikat via automatiseret PowerShell-script

Når du logger på som en tjeneste sikkerhedskonto, skal du give mappen lejer id til din AD app. En lejer er en forekomst af Active Directory. Hvis du kun har ét abonnement, kan du bruge:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Hvis du har mere end ét abonnement, skal du angive det abonnement, hvor din Active Directory er placeret. Se [administrere din Azure AD-mappe](./active-directory/active-directory-administer.md)kan finde flere oplysninger.

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

For at godkende i dit script, angive kontoen, der er en tjeneste, der er primære og levere certifikat miniature, program-id og lejer-id. Hvis du vil automatisere dit script, du kan gemme disse værdier som miljøvariabler og hente dem under kørsel af, eller du kan medtage dem i dit script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Du er nu godkendt som den primære tjeneste for Active Directory-program, du har oprettet.

## <a name="sample-applications"></a>Eksempler på programmer

Følgende eksempelprogrammer viser, hvordan du logge på som tjeneste hovedstolen.

**.NET**

- [Installere en SSH aktiveret VM med en skabelon med .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Administrere Azure ressourcer og grupper med .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Komme i gang med ressourcer - installere ved hjælp af Azure ressourcestyring skabelon - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introduktion til ressourcer - administrere ressourcegruppe - i Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Installere en SSH aktiveret VM med en skabelon i Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Administrere Azure ressource og ressourcegrupper med Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Installere en SSH aktiveret VM med en skabelon i Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Administrere Azure ressourcer og grupper med Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Fonetisk**

- [Installere en SSH aktiveret VM med en skabelon i fonetisk](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Administrere Azure ressource og ressourcegrupper med fonetisk](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Næste trin
  
- Detaljeret vejledning under integrere et program i Azure til administration af ressourcer, skal du se [Developer's guide til godkendelse med Azure ressourcestyring API](resource-manager-api-authentication.md).
- Finde [programmet og Service hovedstolen objekter](./active-directory/active-directory-application-objects.md)på en mere detaljeret forklaring af programmer og principper for tjenesten. 
- Du kan finde flere oplysninger om Active Directory-godkendelse [Godkendelse scenarier, hvor Azure AD](./active-directory/active-directory-authentication-scenarios.md).



