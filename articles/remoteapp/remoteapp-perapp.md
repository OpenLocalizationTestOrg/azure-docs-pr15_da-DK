<properties
   pageTitle="Udgive programmer til individuelle brugere i en Azure RemoteApp af websteder (Preview) | Microsoft Azure"
   description="Få mere at vide, hvordan du kan publicere apps til individuelle brugere i stedet for afhængigt af grupper i Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Udgive programmer til individuelle brugere i en Azure RemoteApp af websteder (Preview)

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

I denne artikel forklarer, hvordan du udgive programmer til individuelle brugere i en Azure RemoteApp af websteder. Dette er nye funktioner i Azure RemoteApp i øjeblikket "private preview" og kun kan vælges Tidlige optagere til evalueringsformål.

Oprindeligt Azure RemoteApp er aktiveret kun én måde at "publicering" programmer: administratoren vil publicere apps fra billedet, og de skal være synlige for alle brugere i gruppen af websteder.

Et almindeligt scenarie er at medtage mange programmer i et enkelt billede og anvende en af websteder for at reducere omkostninger til administration. Ofte ikke alle programmer er relevante for alle brugere – administratorer foretrækker at publicere apps til de enkelte brugere, så de ikke kan se unødvendige programmer i deres programmet feed.

Dette er nu muligt i Azure RemoteApp – aktuelt som en begrænset preview-funktion. Her er en kort oversigt over de nye funktioner:

1. En samling kan angives i en af to måder:
 
  - oprindeligt "samling tilstand", hvor alle brugere i en af websteder kan se udgivne alle programmer. Dette er standardtilstanden.
  - den nye "programmet tilstand", hvor brugerne kun se programmer, der er eksplicit tildelt til dem.

2. I øjeblikket kan programtilstanden kun aktiveres ved hjælp af Azure RemoteApp PowerShell-cmdlet'er.

  - Når den er indstillet til programmet tilstand, kan bruger tildeling i gruppen af websteder ikke administreres via Azure-portalen. Bruger tildeling skal administreres via PowerShell-cmdlet'er.

3. Brugere kan kun se de programmer, der er publiceret direkte til dem. Men det kan stadig være muligt for en bruger til at starte de andre programmer, der er tilgængelige på billedet ved at åbne dem direkte i operativsystemet.
  - Denne funktion giver ikke en sikker låsning af programmer. Det begrænser kun synlighed i programmet på computeren feed.
  - Hvis du har brug for til at isolere brugere fra programmer, skal du bruge separate samlinger for den pågældende.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Sådan henter du Azure RemoteApp PowerShell-cmdlet'er

Hvis du vil prøve den nye preview-funktionalitet, skal du bruge Azure PowerShell-cmdletter. Det er i øjeblikket ikke muligt at bruge portalen Azure Management til at aktivere den nye publicering program-tilstand.

Først skal du sørge for, at du har det [Azure PowerShell-modulet](../powershell-install-configure.md) , der er installeret.

Derefter Start PowerShell console i administratortilstand og køre følgende cmdlet:

        Add-AzureAccount

Det vil bede dig om Azure brugernavn og adgangskode. Når du er logget, vil du kunne kører Azure RemoteApp-cmdletter i forhold til dine Azure abonnementer.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Sådan kontrollerer du, hvilken tilstand en samling er i

Kør følgende cmdlet:

        Get-AzureRemoteAppCollection <collectionName>

![Kontrollere tilstanden af websteder](./media/remoteapp-perapp/araacllelvel.png)

Egenskaben AclLevel kan have følgende værdier:

- Samling: den oprindelige publicering tilstand. Alle brugere se alle publicerede apps.
- Program: den nye publicering tilstand. Brugere vist kun de apps, der er publiceret direkte til dem.

## <a name="how-to-switch-to-application-publishing-mode"></a>Sådan skifter du til programmet publicering tilstand

Kør følgende cmdlet:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Program publicering tilstand bevares: først alle brugerne får vist alle de oprindelige publicerede apps.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Hvordan med listen over brugere, der kan se et bestemt program

Kør følgende cmdlet:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Dette viser en liste over alle brugere, der kan se programmet.

Bemærk: Du kan se de program aliases (kaldet "app alias" i ovenstående syntaks) ved at køre Get-AzureRemoteAppProgram - CollectionName <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Sådan tildeles et program til en bruger

Kør følgende cmdlet:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

Brugeren kan nu se programmet i Azure RemoteApp-klienten og vil kunne oprette forbindelse til den.

## <a name="how-to-remove-an-application-from-a-user"></a>Sådan fjernes et program fra en bruger

Kør følgende cmdlet:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Give feedback på
Vi sætter stor pris på din feedback og forslag vedrørende denne preview-funktion. Udfyld [undersøgelse](http://www.instant.ly/s/FDdrb) at fortælle os din mening.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>Var det ikke mulighed for at prøve funktionen Vis?
Hvis du ikke har deltaget i eksemplet endnu, skal du bruge denne [undersøgelse](http://www.instant.ly/s/AY83p) til at anmode om adgang.
