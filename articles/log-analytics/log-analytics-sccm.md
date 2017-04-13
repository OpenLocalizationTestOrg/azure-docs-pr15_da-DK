<properties
    pageTitle="Forbinde Konfigurationsstyring til Log Analytics | Microsoft Azure"
    description="I denne artikel beskrives trinene til at forbinde Konfigurationsstyring til Log analyser og starte analyse af data."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="banders"/>

# <a name="connect-configuration-manager-to-log-analytics"></a>Oprette forbindelse Konfigurationsstyring til Log Analytics

Du kan oprette forbindelse System Center Configuration Manager til Log Analytics i OMS til synkronisering af enhed samling data. Det gør data fra din Konfigurationsstyring installation tilgængelig i OMS.

Der er en lang række trin, der er behov for at oprette forbindelse Konfigurationsstyring til OMS, så her er en hurtig oversigt over den overordnede proces:

1. Registrer Konfigurationsstyring som et webprogram og/eller Web API-app i portalen Azure administration, og sikre, at du har klient-ID og klienten hemmeligt nøgle registrering fra Azure Active Directory. Se [Brug portal for at oprette Active Directory-program og tjenesten ydelsen, der kan få adgang til ressourcer](../resource-group-create-service-principal-portal.md) for at få detaljerede oplysninger om, hvordan udføre dette trin.
2. I Azure Management-portalen, [giver tilladelse til at få adgang til OMS Konfigurationsstyring (registrerede web-app)](#provide-configuration-manager-with-permissions-to-oms).
3. I Konfigurationsstyring, [tilføje en forbindelse ved hjælp af guiden Tilføj OMS forbindelse](#add-an-oms-connection-to-configuration-manager).
4. I Konfigurationsstyring, du kan [opdatere forbindelsesegenskaberne](#update-oms-connection-properties) Hvis den adgangskode, eller klienten hemmeligt nøgle nogensinde udløber eller går tabt.
5. [Hent og Installer Microsoft overvågning Agent](#download-and-install-the-agent) på den computer, der kører Configuration Manager service forbindelsen Peg med oplysninger fra portalen OMS websted Systemrolle. Agenten sender Konfigurationsstyring data til OMS.
6. I OMS, [importere samlinger fra Konfigurationsstyring](#import-collections) som computergrupper.
7. OMS, få vist data fra Konfigurationsstyring som [computergrupper](log-analytics-computer-groups.md).

Du kan læse mere om at oprette forbindelse Konfigurationsstyring til OMS på [synkronisering af data fra Configuration Manager til Microsoft Operations Management pakke](https://technet.microsoft.com/library/mt757374.aspx).



## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Give konfigurationsstyring tilladelser til OMS

Den følgende fremgangsmåde indeholder portalen Azure Management med tilladelser til at få adgang til OMS. Specifikt, skal du give brugere i ressourcegruppen *rollen bidragyder* . Også, der tillader Azure Management portalen for at oprette forbindelse Konfigurationsstyring til OMS.

>[AZURE.NOTE] Du skal angive tilladelser til at OMS til Configuration Manager. Ellers skal du modtager en fejlmeddelelse, når du bruger konfigurationsguiden i Konfigurationsstyring.


1. Åbn [Azure-portalen](https://portal.azure.com/) , og klik på **Gennemse** > **Log Analytics (OMS)** til at åbne bladet Log Analytics (OMS).  
2. Klik på **Tilføj** for at åbne bladet **OMS arbejdsområde** bladet **Log Analytics (OMS)** .  
  ![OMS blade](./media/log-analytics-sccm/sccm-azure01.png)
3. Angiv følgende oplysninger på bladet **OMS arbejdsområde** , og klik derefter på **OK**.
  - **OMS arbejdsområde**
  - **Abonnement**
  - **Ressourcegruppe**
  - **Placering**
  - **Priser niveau**  
    ![OMS blade](./media/log-analytics-sccm/sccm-azure02.png)  

    >[AZURE.NOTE] Ovenstående eksempel opretter en ny ressourcegruppe. Ressourcegruppen bruges kun til at give tilladelser til arbejdsområdet OMS i dette eksempel Configuration Manager.

4. Klik på **Gennemse** > **ressourcegrupper** til at åbne bladet **ressourcegrupper** .
5. Klik på den ressourcegruppe, som du oprettede ovenfor til at åbne i bladet **ressourcegrupper** på &lt;gruppe ressourcenavn&gt; indstillinger blade.  
  ![ressource gruppe indstillinger blade](./media/log-analytics-sccm/sccm-azure03.png)
6. I den &lt;gruppe ressourcenavn&gt; indstillinger blade, skal du klikke på adgangskontrol (IAM) for at åbne den &lt;ressource gruppenavn&gt; brugere blade.  
  ![ressource gruppe brugere blade](./media/log-analytics-sccm/sccm-azure04.png)  
7. I den &lt;gruppe ressourcenavn&gt; brugere blade, skal du klikke på **Tilføj** for at åbne bladet **Tilføj adgang** .
8. Klik på **Vælg en rolle**i bladet **Tilføj access** , og vælg derefter rollen **bidragyder** .  
  ![Vælg en rolle](./media/log-analytics-sccm/sccm-azure05.png)  
9. Klik på **Tilføj brugere**, vælg brugeren, Konfigurationsstyring, klik på **Vælg**og klik derefter på **OK**.  
  ![tilføje brugere](./media/log-analytics-sccm/sccm-azure06.png)  


## <a name="add-an-oms-connection-to-configuration-manager"></a>Føje en OMS forbindelse til Konfigurationsstyring

For at tilføje en OMS forbindelse, skal Konfigurationsstyring miljøet have en [tjeneste forbindelsespunkt på det sted](https://technet.microsoft.com/library/mt627781.aspx) , der er konfigureret til onlinetilstand.

1. Markér **OMS forbindelse**i arbejdsområdet **Administration** af Configuration Manager. **Tilføje OMS forbindelse guiden**åbnes. Klik på **Næste**.

2. På skærmbilledet **Generelt** Bekræft, at du har installeret følgende handlinger og, at du har oplysninger om hvert element, og vælg derefter **Næste**.
  1. Du har registreret Konfigurationsstyring som et webprogram og/eller Web API-app, og at du har [klient-ID fra registreringen](../active-directory/active-directory-integrating-applications.md)på portalen Azure administration.
  2. I portalen Azure Management, har du oprettet en app hemmeligt nøgle for registrerede appen i Azure Active Directory.  
  3. Du har angivet de registrerede online med tilladelse til at få adgang til OMS i portalen Azure administration.  
  ![Forbindelsen til OMS guiden generelt side](./media/log-analytics-sccm/sccm-console-general01.png)

3. Konfigurere forbindelsesindstillingerne til OMS ved at indsende din **lejer** , **Klient-ID** og **Klienten hemmeligt nøgle** på skærmbilledet **Azure Active Directory** og derefter vælge **Næste**.  
  ![Forbindelsen til OMS guiden Azure Active Directory-side](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)

4. Hvis du har udført alle de andre procedurer, derefter vises oplysningerne på skærmbilledet **OMS Forbindelseskonfiguration** automatisk på denne side. Oplysninger om forbindelsesindstillingerne bør fremgå til **Azure-abonnement** , **Azure ressourcegruppe** og **Handlinger arbejdsområdet til administration pakke**.  
  ![Forbindelsen til OMS guiden OMS forbindelse side](./media/log-analytics-sccm/sccm-wizard-configure04.png)

5. Guiden opretter forbindelse til tjenesten OMS ved hjælp af de oplysninger, du har indtastet. Vælg de enhed af websteder, som du vil synkronisere med OMS, og klik derefter på **Tilføj**.  
  ![Vælg af websteder](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)

6. Bekræfte dine forbindelsesindstillinger på skærmbilledet **Oversigt** og derefter vælge **Næste**. Skærmbilledet **status** viser forbindelsesstatus, og derefter skal **færdigt**.

>[AZURE.NOTE] Du skal knytte OMS til webstedet for øverste niveau i hierarkiet. Hvis du forbinde OMS til et enkeltstående primære websted og derefter føje et central administration af websted til dit miljø, får du at slette og gendanne OMS forbindelsen i det nye hierarki.

Når du har sammenkædet Konfigurationsstyring OMS, kan du tilføje eller fjerne af websteder og få vist egenskaberne for OMS forbindelsen.

## <a name="update-oms-connection-properties"></a>Opdatere OMS forbindelsesegenskaber

Hvis en adgangskode eller klient hemmeligt nøgle nogensinde udløber eller går tabt, skal du manuelt opdatere forbindelsesegenskaber OMS.

1. I Konfigurationsstyring skal gå til **Skytjenester** og derefter vælge **OMS Connector** til at åbne siden **OMS forbindelsesegenskaber** .
2. Klik på fanen **Azure Active Directory** for at få vist din **lejer**, **Klient-ID**, **klienten hemmeligt vigtige udløb**på denne side. **Bekræft** din **klient hemmeligt nøgle** Hvis det er udløbet.


## <a name="download-and-install-the-agent"></a>Downloade og installere agenten

1. I OMS portalen, [hente installationsfilen agent fra OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Bruge en af følgende metoder til at installere og konfigurere agenten på den computer, der kører Konfigurationsstyring forbindelse punkt websted system rollen:
  - [Installere agent ved hjælp af konfiguration](log-analytics-windows-agents.md#install-the-agent-using-setup)
  - [Installere agent ved hjælp af kommandolinjen](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
  - [Installere agent ved hjælp af DTK i Azure Automation](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)


## <a name="import-collections"></a>Import af websteder

Når du har tilføjet en OMS forbindelse til Konfigurationsstyring og installeret på agent peg på den computer, der kører Configuration Manager service forbindelsen websted Systemrolle, skal det næste trin er at importere af websteder fra Konfigurationsstyring i OMS som computergrupper.

Når indførsel er aktiveret, hentes medlemsoplysninger samling hver 3 timer for at holde samling medlemskaberne aktuelle. Du kan vælge at deaktivere indførsel når som helst.

1. Klik på **Indstillinger**i portalen OMS.
2. Klik på fanen **Computergrupper** , og klik derefter på fanen **SCCM** .
3. Vælg **medlemskaber, Konfigurationsstyring til Import af websteder** , og klik derefter på **Gem**.  
  ![Computergrupper - SCCM fanen](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Få vist data fra Konfigurationsstyring

Når du har tilføjet en OMS forbindelse til Konfigurationsstyring og installeret på agent på den computer, der kører Configuration Manager service forbindelse punkt websted Systemrolle, sendes data fra en agent til OMS. I OMS vises dine Konfigurationsstyring samlinger som [computergrupper](log-analytics-computer-groups.md). Du kan få vist grupperne på siden **Konfigurationsstyring** under **Computergrupper** i **Indstillinger**.

Når samlingerne, der er importeret, kan du se, hvor mange computere med medlemskab af websteder, der er fundet. Du kan også få vist antallet af websteder, som er blevet importeret.

![Computergrupper - SCCM fanen](./media/log-analytics-sccm/sccm-computer-groups02.png)

Når du klikker på et af disse elementer, åbnes søgning og viser enten alle de importerede grupper eller alle computere, der hører til hver gruppe. Ved hjælp af [Log søgning](log-analytics-log-searches.md), kan du starte indgående analyse til Konfigurationsstyring data.

## <a name="next-steps"></a>Næste trin

- Brug [Log Søg](log-analytics-log-searches.md) til at få vist detaljerede oplysninger om dataene Configuration Manager.
