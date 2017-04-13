<properties
   pageTitle="Oprette service vigtigste med Azure CLI | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure CLI til at oprette en Active Directory-program og service hovedstolen og give den adgang til ressourcer via rollebaseret adgangskontrol. Det viser, hvordan til at godkende program med en adgangskode eller et certifikat."
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Brug Azure CLI til at oprette en tjeneste sikkerhedskonto få adgang til ressourcer

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Når du har et program eller script, som skal have adgang til ressourcer, ønsker du sandsynligvis ikke til at køre denne proces under dine egne legitimationsoplysninger. Du skal muligvis forskellige tilladelser, du vil for programmet, og du vil ikke programmet fortsætte med at bruge dine legitimationsoplysninger, hvis dine ansvarsområder ændrer. I stedet skal oprette du en identitet for det program, der omfatter legitimationsoplysninger og rolletildelinger. Hver gang appen kører, godkender selve med disse legitimationsoplysninger. Dette emne beskrives, hvordan du bruger [Azure CLI til Mac, Linux og Windows](xplat-cli-install.md) til at konfigurere et program til at køre under sin egen legitimationsoplysninger og identitet.

Med Azure CLI har du to muligheder for godkendelse af dit AD program:

 - adgangskode
 - certifikat

Dette emne viser, hvordan du bruger begge muligheder i Azure CLI. Hvis du vil logge på Azure fra en programming ramme (sådanne Python, fonetisk eller Node.js), kan godkendelse af adgangskode være en god løsning. Inden du beslutter, om du vil bruge en adgangskode eller et certifikat, i afsnittet [eksempelprogrammer](#sample-applications) eksempler på godkendelse i forskellige rammer.

## <a name="active-directory-concepts"></a>Active Directory-begreber

I denne artikel kan oprette du to objekter - programmet Active Directory (AD) og tjenesten vigtigste. AD-programmet er den globale repræsentation af dit program. Den indeholder legitimationsoplysninger (et program-id og en adgangskode eller certifikat). Tjenesten vigtigste er den lokale repræsentation af dit program i et Active Directory. Den indeholder rolletildelingen. Dette emne fokuserer på et enkelt lejer-program, hvor programmet er beregnet til at køre i kun én organisation. Du bruger typisk enkelt lejer programmer for line of business programmer, der kører i din organisation. I et enkelt lejer program har du én AD app og én tjeneste hovedstolen.

Du måske spekulerer du over - Hvorfor skal jeg bruge begge objekter? Denne fremgangsmåde giver mere mening, når du finder flere lejer programmer. Du bruger typisk med flere lejer programmer for software som en tjeneste (SaaS) programmer, hvor dit program kører i mange forskellige abonnementer. Flere lejer programmer har du ét AD app og flere principper for tjenesten (ét i hver Active Directory, der giver adgang til appen). For at konfigurere et med flere lejer program skal du se [Developer's guide til godkendelse med Azure ressourcestyring API](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Nødvendige tilladelser

For at fuldføre dette emne, skal du har tilstrækkelige tilladelser i både din Azure Active Directory og abonnementet Azure. Du skal være specifikt, kan du oprette en app i Active Directory og tildele service hovedstolen til en rolle. 

I din Active Directory, skal kontoen være administrator (som **Global administrator** eller **Bruger administrator**). Hvis din konto er tildelt rollen **bruger** , skal du få en administrator til at ændre dine tilladelser.

I dit abonnement, kontoen skal have `Microsoft.Authorization/*/Write` access, der tildeles via [ejer](./active-directory/role-based-access-built-in-roles.md#owner) rollen eller [bruger Access](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) -administratorrolle. Hvis din konto er tildelt rollen **bidragyder** , modtager du en fejl, når du forsøger at tildele service hovedstolen til en rolle. Administratoren abonnement skal igen, give dig adgang.

Fortsæt nu til en sektion til [adgangskode](#create-service-principal-with-password) eller [certifikat](#create-service-principal-with-certificate) godkendelse.

## <a name="create-service-principal-with-password"></a>Oprette service vigtigste med adgangskode

I dette afsnit, skal du udføre trinnene for at oprette AD-program med en adgangskode, og tildele rollen Læser til tjenesten vigtigste.

Lad os gennemgå disse trin.

1. Log på din konto.

        azure login

1. Har du to muligheder for at oprette AD-programmet. Du kan enten oprette programmet AD og tjenesten vigtigste i ét trin, eller du kan oprette dem separat. Oprette dem i ét trin, hvis det ikke er nødvendigt at angive en startside og id URI'er for din app. Oprette dem separat, hvis du vil angive disse værdier til en WebApp. Begge muligheder er vist i dette trin.

     - Angiv navnet på appen og en adgangskode for at oprette tjenesten AD programmet og vigtigste i ét trin, som vist i følgende kommando:
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Angiv navnet på app, en startside URI, URI'er-id og en adgangskode, hvis du vil oprette programmet AD separat, som vist i følgende kommando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         Kommandoen foregående returnerer en sikkerhedskontrol værdi. For at oprette en tjeneste sikkerhedskonto skal du angive den pågældende værdi som en parameter i følgende kommando:
     
            azure ad sp create -a <AppId>
     
     Hvis din konto ikke har de [nødvendige tilladelser](#required-permissions) på Active Directory, du får vist en fejlmeddelelse, der angiver "Authentication_Unauthorized" eller "Intet abonnement fundet i konteksten".
    
     Den nye tjeneste hovedstolen returneres for begge muligheder. **Objekt-Id** bruges, når gives tilladelser. Guid vises med **Tjenestens hovednavne** bruges, når brugeren logger på. Denne guid er den samme værdi som app-id. I eksempelprogrammerne kaldes denne værdi for **Klient-ID**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Give service vigtigste tilladelser på dit abonnement. I dette eksempel kan du tilføje tjenesten hovedstolen til rollen **læser** , som giver tilladelse til at læse alle ressourcer i abonnementet. Finde andre roller [RBAC: indbyggede roller](./active-directory/role-based-access-built-in-roles.md). Angiv **ObjectId** , som du brugte, da oprettelse af programmet til parameteren **ServicePrincipalName** . 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Hvis din konto ikke har tilstrækkelige tilladelser til at tildele en rolle, får du vist en fejlmeddelelse. Vises en meddelelse om din konto **har ikke tilladelse til at udføre handlingen 'Microsoft.Authorization/roleAssignments/write' over omfang ' / abonnementer / {guid}'**. 

Det var det! AD-programmet og service hovedstolen er konfigureret. Næste afsnit viser, hvordan du logger på med legitimationsoplysningerne via Azure CLI. Hvis du vil bruge legitimationsoplysninger i din kode programmet, behøver du ikke fortsætte med dette emne. Du kan springe til [eksempler på programmer](#sample-applications) eksempler på, at logge på med dit program-id og din adgangskode. 

### <a name="provide-credentials-through-azure-cli"></a>Angive legitimationsoplysninger via Azure CLI

Nu skal du logge på som programmet til at udføre handlinger.

1. Når du logger på som en tjeneste sikkerhedskonto, skal du give mappen lejer id til din AD app. En lejer er en forekomst af Active Directory. For at hente lejer-id'et til dit aktuelt godkendte abonnement skal du bruge:

        azure account show

     Der returneres:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Hvis du har brug at få et andet abonnement lejer id, kan du bruge følgende kommando:

        azure account show -s {subscription-id}

2. Hvis du vil hente klient-id skal bruges til at logge på, skal du bruge:

        azure ad sp show -c exampleapp --json

     Værdien, der skal bruges til at logge på er angivet i de vigtigste tjenestenavne guid.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Log på som tjeneste hovedstolen.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Du bliver bedt om adgangskoden. Angive den adgangskode, du har angivet, når du opretter AD programmet.

        info:    Executing command login
        Password: ********

Du er nu godkendt som service hovedstolen for tjenesten hovedstolen, du har oprettet.

## <a name="create-service-principal-with-certificate"></a>Oprette service vigtigste med certifikat

I dette afsnit, skal udføre du disse trin for at:

- oprette et selvsigneret certifikat
- oprette AD-program med certifikatet og hovedstolen service
- tildele rollen Læser til tjenesten hovedstolen

For at fuldføre disse trin, skal du have [OpenSSL](http://www.openssl.org/) installeret.

1. Oprette et selvsigneret certifikat.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Kombinere offentlige og private nøgler.

        cat privkey.pem cert.pem > examplecert.pem

3. Åbn filen **examplecert.pem** , og se efter den lange række tegn mellem **– BEGYNDE certifikat –** og **– AFSLUTTE certifikat---**. Kopiere certifikatets data. Du kan overføre disse data som en parameter ved oprettelse af tjenesten vigtigste.

1. Log på din konto.

        azure login

1. Har du to muligheder for at oprette AD-programmet. Du kan enten oprette programmet AD og tjenesten vigtigste i ét trin, eller du kan oprette dem separat. Oprette dem i ét trin, hvis det ikke er nødvendigt at angive en startside og id URI'er for din app. Oprette dem separat, hvis du vil angive disse værdier til en WebApp. Begge muligheder er vist i dette trin.

     - For at oprette tjenesten AD programmet og vigtigste i ét trin skal du angive navnet på appen og certifikatets data, som vist i følgende kommando:
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Angiv navnet på app, en startside URI, id URI'er og certifikatets data, hvis du vil oprette programmet AD separat, som vist i følgende kommando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         Kommandoen foregående returnerer en sikkerhedskontrol værdi. For at oprette en tjeneste sikkerhedskonto skal du angive den pågældende værdi som en parameter i følgende kommando:
     
            azure ad sp create -a <AppId>
  
     Hvis din konto ikke har de [nødvendige tilladelser](#required-permissions) på Active Directory, du får vist en fejlmeddelelse, der angiver "Authentication_Unauthorized" eller "Intet abonnement fundet i konteksten".
    
     Den nye tjeneste hovedstolen returneres for begge muligheder. Objekt-id'et bruges, når gives tilladelser. Guid vises med **Tjenestens hovednavne** bruges, når brugeren logger på. Denne guid er den samme værdi som app-id. I eksempelprogrammerne kaldes denne værdi for **Klient-ID**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Give service vigtigste tilladelser på dit abonnement. I dette eksempel kan du tilføje tjenesten hovedstolen til rollen **læser** , som giver tilladelse til at læse alle ressourcer i abonnementet. Finde andre roller [RBAC: indbyggede roller](./active-directory/role-based-access-built-in-roles.md). Angiv **ObjectId** , som du brugte, da oprettelse af programmet til parameteren **ServicePrincipalName** . 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Hvis din konto ikke har tilstrækkelige tilladelser til at tildele en rolle, får du vist en fejlmeddelelse. Vises en meddelelse om din konto **har ikke tilladelse til at udføre handlingen 'Microsoft.Authorization/roleAssignments/write' over omfang ' / abonnementer / {guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Angive certifikat via automatiseret Azure CLI script

Nu skal du logge på som programmet til at udføre handlinger.

1. Når du logger på som en tjeneste sikkerhedskonto, skal du give mappen lejer id til din AD app. En lejer er en forekomst af Active Directory. For at hente lejer-id'et til dit aktuelt godkendte abonnement skal du bruge:

        azure account show

     Der returneres:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Hvis du har brug at få et andet abonnement lejer id, kan du bruge følgende kommando:

        azure account show -s {subscription-id}

1. Hvis du vil hente certifikat miniature, og fjern unødvendige tegn, skal du bruge:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Som returnerer en miniatureværdi på ligner:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Hvis du vil hente klient-id skal bruges til at logge på, skal du bruge:

        azure ad sp show -c exampleapp

     Værdien, der skal bruges til at logge på er angivet i de vigtigste tjenestenavne guid.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Log på som tjeneste hovedstolen.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- For at få flere oplysninger om brug af certifikater og Azure CLI skal du se [certifikat-baseret godkendelse med Azure Service principper fra Linux-kommandolinjen](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
