<properties
   pageTitle="Azure Active Directory rapportering – preview | Microsoft Azure"
   description="Viser en liste over de forskellige tilgængelige rapporter til Azure Active Directory preview"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory rapportering – eksempel

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-reporting-azure-portal.md)
- [Azure klassisk portal](active-directory-reporting-guide.md)

*Denne dokumentation er en del af [Azure Active Directory rapportering Guide](active-directory-reporting-guide.md).*

Med rapportering i Azure Active Directory preview, får du alle de oplysninger, du har brug for at finde ud af, hvordan dit miljø er at gøre. [Hvad er i preview?](active-directory-preview-explainer.md)

Der er to hovedområderne i rapportering:

- **Log på aktiviteter** – oplysninger om brug af administrerede programmer og brugeraktiviteter-logon

- **Overvågningslogge** - System aktivitetsoplysninger om brugere og gruppeadministration, din administrerede programmer og directory aktiviteter

Afhængigt af omfanget af de data, du leder efter, du kan få adgang til disse rapporter enten ved at klikke på **brugere og grupper** eller **Enterprise-programmer** i listen over tjenester på [Azure-portalen](https://portal.azure.com).

## <a name="sign-in-activities"></a>Log på aktiviteter

### <a name="user-sign-in-activities"></a>Log på brugeraktiviteter

Med de oplysninger, som rapporten bruger-logon, kan du finde svar på spørgsmål som f.eks.:

- Hvad er det logon-mønster for en bruger?
- Hvor mange brugere har brugere logget på en uge?
- Hvad er status for disse logon?

Den hurtigste vej til disse data er bruger-logon graf i sektionen **Oversigt over** under **brugere og grupper**.

 ![Rapportering] (./media/active-directory-reporting-azure-portal/05.png "Rapportering")

Bruger-logon grafen viser ugentlig sammenlægninger i logge ins for alle brugere i en given tidsperiode. Standard for den angivne periode er 30 dage.

![Rapportering] (./media/active-directory-reporting-azure-portal/02.png "Rapportering")

Når du klikker på en dag i graph-logon, får du en detaljeret liste over logon-aktiviteter.

![Rapportering] (./media/active-directory-reporting-azure-portal/03.png "Rapportering")

Hver række i logon aktiviteter liste giver dig de detaljerede oplysninger om den valgte logon f.eks.:

- Der er logget på?

- Hvad var relaterede UPN?

- Hvilket program var destination for logonnavn?

- Hvad er IP-adressen på logonnavn?

- Hvad er status for logonnavn?

### <a name="usage-of-managed-applications"></a>Brugen af administrerede programmer

Med et program-central visning af dataene logon, kan du besvare spørgsmål som f.eks.:

- Der bruger mine-programmer?

- Hvad er de øverste 3 programmer i din organisation?

- Jeg har senest gennemført et program. Hvordan det sig?


Den hurtigste vej til disse data er de øverste 3 programmer i din organisation i de seneste 30 dage rapport i sektionen **Oversigt over** under **Enterprise-programmer**.

 ![Rapportering] (./media/active-directory-reporting-azure-portal/06.png "Rapportering")


De app brugen graph ugentlig sammenlægninger af Tilmeld ins programmerne top 3 i en given tidsperiode. Standard for den angivne periode er 30 dage.

![Rapportering] (./media/active-directory-reporting-azure-portal/78.png "Rapportering")

Hvis du vil, kan du angive fokus på et bestemt program.

![Rapportering] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Rapportering")


Når du klikker på en dag i app brugen graph, får du en detaljeret liste over logon-aktiviteter.


![Rapportering] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Rapportering")



Indstillingen **logon** får du en komplet oversigt over alle logon begivenheder til dine programmer.

![Rapportering] (./media/active-directory-reporting-azure-portal/85.png "Rapportering")

Ved hjælp af i kolonnevælger, kan du vælge de datafelter, du vil have vist.

![Rapportering] (./media/active-directory-reporting-azure-portal/column_chooser.png "Rapportering")



### <a name="filtering-sign-ins"></a>Filtrering logon

Du kan filtrere logon efter et tidsinterval til at begrænse mængden viste data.

![Rapportering] (./media/active-directory-reporting-azure-portal/927.png "Rapportering")


En anden metode til at filtrere posterne for de aktiviteter, der logon er at søge efter specifikke poster.
Metoden søgning gør det muligt at begrænse din logon rundt om bestemte **brugere**, **grupper** eller **programmer**.


![Rapportering] (./media/active-directory-reporting-azure-portal/84.png "Rapportering")

## <a name="audit-logs"></a>Overvågningslogge

Overvågning loggene i Azure Active Directory indeholder posterne for systemaktiviteter for overholdelse af angivne standarder.

Der findes tre primære kategorier for overvågning relaterede aktiviteter i portalen Azure:

- Brugere og grupper   

- Programmer

- Directory   


Du kan finde en komplet liste over overvåge rapport aktiviteter, på [listen over overvåge rapport hændelser](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Den hurtigste vej til alle overvå data er **overvågningslogge** i sektionen **aktivitet** af **Azure Active Directory**.


![Overvågning] (./media/active-directory-reporting-azure-portal/61.png "Overvågning")


En overvågningslog har en listevisning, der viser aktører (,), hvilke aktiviteter (hvad) og målene.


![Overvågning] (./media/active-directory-reporting-azure-portal/345.png "Overvågning")


Du kan få flere oplysninger om det ved at klikke på et element i listevisningen.

![Overvågning] (./media/active-directory-reporting-azure-portal/873.png "Overvågning")




### <a name="users-and-groups-audit-logs"></a>Brugere og grupper overvågningslogge


Med bruger- og gruppe-baserede overvågningslog rapporter, kan du få svar på spørgsmål som f.eks.:

- Hvilke typer af opdateringer er blevet anvendt brugerne?

- Hvor mange brugere er blevet ændret?

- Hvor mange adgangskoder er blevet ændret?

- Hvad er en administrator udført i en mappe?

- Hvad er de grupper, der er blevet tilføjet?

- Er der grupper med medlemskab ændringer?

- Er blevet ændret ejerne af gruppen?

- Hvilke licenser der er tildelt til en gruppe eller en bruger?


Hvis du blot ønsker at gennemse overvå data, der er relateret til brugere og grupper, kan du finde en filtreret visning under **overvågningslogge** i afsnittet **aktivitet** på **brugere og grupper**.


![Overvågning] (./media/active-directory-reporting-azure-portal/93.png "Overvågning")


### <a name="application-audit-logs"></a>Programmet overvågningslogge

Med programmet-baserede overvåge, rapporter, kan du få svar på spørgsmål som f.eks.:

- Hvad er de programmer, der er blevet tilføjet eller opdateret?

- Hvad er de programmer, der er blevet fjernet?

- Er en tjeneste princippet for et program ændret?

- Er blevet ændret navnene på de programmer?

- Hvem har givet samtykke til programmet?


Hvis du blot ønsker at gennemse overvå data, der er relateret til programmer, kan du finde en filtreret visning under **overvågningslogge** i afsnittet **aktivitet** i **Enterprise-programmer**.


![Overvågning] (./media/active-directory-reporting-azure-portal/134.png "Overvågning")


### <a name="filtering-audit-logs"></a>Filtrering overvågningslogge

Du kan filtrere en overvågningslog rapport efter et tidsinterval til at begrænse mængden viste data.

![Overvågning] (./media/active-directory-reporting-azure-portal/324.png "Overvågning")

En anden metode til at filtrere posterne i en overvågningsloggen er at søge efter specifikke poster.

![Overvågning] (./media/active-directory-reporting-azure-portal/237.png "Overvågning")

## <a name="next-steps"></a>Næste trin

Se [Azure Active Directory rapportering vejledning](active-directory-reporting-guide.md).
