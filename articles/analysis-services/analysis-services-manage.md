<properties
   pageTitle="Administrere Azure Analysis Services | Microsoft Azure"
   description="Få mere at vide, hvordan du administrerer en Analysis Services-server i Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="manage-analysis-services"></a>Administrere Analysis Services

Når du har oprettet en Analysis Services-server i Azure, kan der være nogle administration og styring opgaver, du skal udføre det samme eller et tidspunkt på et senere tidspunkt. For eksempel køre behandling til at opdatere data, styre, hvem der kan få adgang til modeller på din server eller overvåge serverens tilstand. Visse administrationsopgaver kan kun udføres Azure-portalen, andre i SQL Server Management Studio (SSMS), og nogle opgaver kan udføres på en.

## <a name="azure-portal"></a>Azure-portalen
[Azure-portalen](http://portal.azure.com/) er, hvor du kan oprette og slette servere, overvåge serverressourcer, ændre størrelse og administrere, hvem der har adgang til dine servere.  Hvis du har nogle af de problemer, kan du også sende en supportanmodning.

![Få servernavn i Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Oprette forbindelse til din server i Azure er ligesom at oprette forbindelse til en server-forekomst i din egen organisation. Fra SSMS, kan du udføre mange af de samme opgaver som proces data eller oprette et script behandling, administrere roller og bruge PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 En af de større forskelle er den godkendelse, du kan bruge til at oprette forbindelse til din server. Hvis du vil oprette forbindelse til din Azure Analysis Services-server, skal du vælge **Active Directory Password Authentication**.

### <a name="to-connect-with-ssms"></a>For at oprette forbindelse med SSMS
1. Før du opretter forbindelse, skal du finde navnet på serveren. **Azure** -portalen > server > **Oversigt over** > **servernavn**, kopiere navnet på serveren.

    ![Få servernavn i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

2. I SSMS > **Object Explorer**, klik på **Opret forbindelse** > **Analysis Services**.

3. I dialogboksen **Opret forbindelse til Server** i navnet på den server, og klik derefter på Indsæt i **godkendelse**skal du vælge et af følgende:

    **Active Directory integreret godkendelse** bruge enkeltlogon med Active Directory til Azure Active Directory federation.

    **Active Directory Password Authentication** at bruge en organisationskonto. For eksempel at oprette forbindelse fra et ikke-domæne tilsluttet sig computer.

    Bemærk: Hvis du ikke kan se Active Directory-godkendelse, kan du skal [aktivere Azure Active Directory-godkendelse](#enable-azure-active-directory-authentication) i SSMS.

    ![Oprette forbindelse i SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Da administration af din server i Azure ved hjælp af SSMS er samme måde, som administration af en lokal server, skal vi ikke gå til detaljer om her. Al hjælpen du har brug for kan findes i [Analysis Services forekomst Management](https://msdn.microsoft.com/library/hh230806.aspx) på MSDN.

## <a name="server-administrators"></a>Serveradministratorer
Du kan bruge **Analysis Services-administratorer** i bladet kontrolelement til din server i Azure portal eller SSMS til at administrere serveradministratorer. Analysis Services administratorer kan database serveradministratorer har rettigheder til almindelige database administrationsopgaver såsom at tilføje og fjerne databaser og administration af brugere. Som standard tilføjes den bruger, som opretter serveren Azure-portalen automatisk som en Analysis Services.

Du skal også vide:

-   Windows Live ID er ikke en understøttet identitetstype til Azure Analysis Services.  
-   Analysis Services administratorer skal være gyldige Azure Active Directory-brugere.
-   Hvis opretter en Azure Analysis Services-serveren via Azure ressourcestyring skabeloner, kræver en JSON matrix af brugere, der skal tilføjes som administratorer i Analysis Services-administratorer.

Analysis Services-administratorer kan være forskellige fra Azure ressource administrators, som kan administrere ressourcer til Azure-abonnementer. Dette bevarer kompatibilitet med eksisterende XMLA og TSML administrere funktionsmåder i Analysis Services og tillader dig at udskille opgaver mellem Azure ressourcestyring og Analysis Services database management.

Brug adgangskontrol (IAM) på bladet kontrolelement for at få vist alle roller og få adgang til typer for din Azure Analysis Services-ressource.

## <a name="database-users"></a>Databasebrugere
Azure Analysis Services-model databasebrugere skal være i din Azure Active Directory. Brugernavne, der er angivet for modeldatabasen skal være ved organisatoriske mailadresse eller UPN. Dette er forskellig fra lokale model databaser, der understøtter brugere af Windows domæne brugernavne.

Du kan tilføje brugere ved hjælp af [rolletildelinger i Azure Active Directory](../active-directory/role-based-access-control-configure.md) eller ved hjælp af [Tabular Model Scripting sprog](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) i SQL Server Management Studio.

**Eksempel på TMSL-script**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Aktivere Azure Active Directory-godkendelse
Aktivere funktionen Azure Active Directory-godkendelse til SSMS i registreringsdatabasen, skal du oprette en tekstfil med navnet EnableAAD.reg, og derefter kopiere og indsætte følgende:


```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Gem og kør derefter filen.



## <a name="next-steps"></a>Næste trin
Hvis du ikke har allerede installeret en tabelmodel på din nye server, er nu et godt tidspunkt. Se [Implementer til Azure Analysis Services](analysis-services-deploy.md)for at få mere for at vide.

Hvis du har installeret en model til din server, er du klar til at oprette forbindelse til den ved hjælp af en klient eller browser. For at få mere for at vide, kan du se [hente data fra Azure Analysis Services-serveren](analysis-services-connect.md).
