
<properties
    pageTitle="Brug af Azure AD forbinde tilstand med AD DS | Microsoft Azure"
    description="Dette er siden Azure AD forbinde tilstand, der beskriver, hvordan til at overvåge AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Brug af Azure AD forbinde tilstand med AD DS
Følgende dokumentation er specifikke for overvågning Active Directory Domain Services med Azure AD forbinde tilstand. Understøttede versioner af Active Directory-Domænetjenester er: Windows Server 2008 R2, Windows Server 2012 og Windows Server 2012 R2.

Du kan finde flere oplysninger om overvågning AD FS med Azure AD forbinde sundhed, [Ved hjælp af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md). Desuden kan finde oplysninger om overvågning Azure AD-forbindelse (Sync) med Azure AD forbinde tilstand i [Ved hjælp af Azure AD forbinde tilstand for synkronisering](active-directory-aadconnect-health-sync.md).

![Azure AD Connect tilstand til AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Beskeder for Azure AD forbinde tilstand til AD DS
Afsnittet beskeder i Azure AD forbinde tilstand til AD DS, giver dig en liste over aktive og afsluttede beskeder, der er relateret til dit domæne enheder. Valg af en aktiv eller løst besked åbnes en ny blade med yderligere oplysninger, sammen med opløsning trin og links til yderligere dokumentation. Hver beskedtype kan have en eller flere forekomster, der svarer til hvert af de domænecontrollere, der påvirkes af den pågældende bestemt påmindelse. Du kan dobbeltklikke på en pågældende domænenavn fra domænecontrolleren for at åbne en ekstra blade med flere oplysninger om forekomsten besked i bunden af bladet besked.

I denne blade, kan du aktivere mailbeskeder for beskeder og ændre tidsintervallet i visningen. Udvide tidsrum, kan du se forudgående løst beskeder.

![Azure AD-forbindelse synkroniseringsfejl](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Domæne enheder Dashboard
Dette dashboard giver en topologisk visning af dit miljø, sammen med vigtige funktionsdygtige mål og sundhedsstatus for hver af dine overvåget domænecontrollere. Præsenteret målepunkter hjælp til hurtigt for at identificere, en hvilken som helst domæne enheder, som kan kræve yderligere undersøgelse. Som standard vises kun et undersæt af kolonnerne. Du kan dog finde på hele sættet af tilgængelige kolonner, ved at dobbeltklikke på kommandoen kolonner. Vælg de kolonner, der mest interesserer dig, omdannes dette dashboard til en enkelt og nemt placere for at få vist tilstanden for dit miljø til Active Directory-Domænetjenester.

![Domæne enheder](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Domænecontrollere kan grupperes efter deres respektive domæne eller webstedet, hvilket er nyttigt for at forstå miljø topologien. Endelig, hvis du dobbeltklikker på overskriften blade, maksimerer dashboard for at udnytte tilgængelige skærmen ejendom. Denne større visning er nyttigt, når flere kolonner vises.

## <a name="replication-status-dashboard"></a>Replikering Status Dashboard
Dette dashboard indeholder en visning af replikering status og topologi for replikering over dine enheder, der overvåget domæne. Status for det seneste forsøg på gentagelse er angivet, sammen med praktiske dokumentation for eventuelle fejl, der er fundet. Du kan dobbeltklikke på et domænenavn fra domænecontrolleren med en fejl, at åbne en ny blade med oplysninger som f.eks.: oplysninger om fejl, anbefalede opløsning trin og links til fejlfinding i forbindelse med dokumentation.

![Status for gentagelse](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Overvågning
Denne funktion giver grafiske tendenser af forskellige ydeevne tællere, der er løbende der indsamles fra hver af overvåget domænecontrollerne. Ydeevnen for et domænenavn fra domænecontrolleren kan nemt sammenlignes på tværs af alle andre overvåget domæne enheder i dit område. Desuden kan du se forskellige tællere i ydeevne side om side, som er nyttigt, når fejlfinding af problemer i dit miljø.

![Overvågning](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Som standard har vi markeret fire tællere i ydeevne; Du kan dog medtage andre ved at klikke på kommandoen Filtrer og valg og fravalg af enhver tællere i ønskede ydeevne. Desuden kan du dobbeltklikke på en ydeevne tæller graf for at åbne en ny blade, som omfatter datapunkter for hver af overvåget domænecontrollerne.

## <a name="related-links"></a>Relaterede links

* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed Agent-Installation](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect sundhed handlinger](active-directory-aadconnect-health-operations.md)
* [Brug af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md)
* [Ved hjælp af Azure AD forbinde tilstand til synkronisering](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect sundhed ofte stillede spørgsmål](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
