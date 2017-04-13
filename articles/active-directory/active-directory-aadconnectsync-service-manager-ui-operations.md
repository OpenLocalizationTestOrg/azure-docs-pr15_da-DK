<properties
    pageTitle="Azure AD-forbindelse synkronisering: synkronisering Service Manager UI | Microsoft Azure"
    description="Forstå fanen Handlinger i synkronisering Service Manager for Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD-forbindelse synkronisering: synkronisering Service Manager

[Handlinger](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Forbindelser](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse-søgning](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Fanen Handlinger viser resultaterne fra de seneste handlinger. Denne fane er tasten for at forstå og foretage fejlfinding af problemer.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Forstå de oplysninger, der er synlige i fanen Handlinger
Den øverste halvdel viser alle kører i kronisk rækkefølge. Handlinger loggen bevarer oplysninger om de seneste syv dage som standard, men denne indstilling kan ændres med [scheduler](active-directory-aadconnectsync-feature-scheduler.md). Du vil søge efter en hvilken som helst Kør, der ikke viser statussen succes. Du kan ændre sorteringen ved at klikke på overskrifterne.

Kolonnen **Status** er de vigtigste oplysninger og viser det mest alvorlige problem til et Kør. Her er en hurtig oversigt over de mest almindelige statusser i rækkefølgen af prioritet for at undersøge (hvor * angiver flere mulige fejlstrenge).

Status | Kommentar
--- | ---
holdt op med at-* | Kør kunne ikke fuldføres. For eksempel, hvis det eksterne system er nede og kan ikke kontaktes.
stoppet – fejl-grænse | Der er mere end 5.000 fejl. Kør stoppede automatisk på grund af det store antal fejl.
færdige -\*-fejl | Kør fuldført, men der er fejl (færre end 5.000), der skal undersøges.
færdige -\*-advarsler | Kør fuldført, men nogle data er ikke i den forventede tilstand. Hvis du har fejl, derefter denne meddelelse er som regel kun et symptom. Indtil du har behandlet fejl, bør du undersøge advarsler ikke.
succes | Ingen problemer.

Når du vælger en række, opdaterer bunden for at få vist oplysninger om, der kører. Til yderst til venstre i bunden, kan du have en liste, siger **trin #**. Denne liste vises kun, hvis du har flere domæner i dit område, hvor alle domæner, der er repræsenteret af et trin. Navnet på det domæne, kan findes under overskriften **Partition**. Du kan finde flere oplysninger om antallet af ændringer, der blev behandlet under **Synkronisering af statistik**. Du kan klikke på linkene for at få en liste over de ændrede objekter. Hvis du har objekter med fejl, vises disse fejl under **Synkroniseringsfejl**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Foretage fejlfinding under fanen Handlinger
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Når du har en fejl, er objektet i fejl og selve fejlen links, der indeholder flere oplysninger.

Start ved at klikke på strengen fejl (**Synkroniser-regel – fejl-funktionen-udløste** på billedet). Du først vises en oversigt over objektet. Klik på knappen **Staksporing**for at få vist den faktiske fejl. Denne sporing indeholder niveau fejlfindingsoplysninger for fejl.

**TIP:** Du kan højreklikke i feltet **stak opkaldsoplysninger** , Vælg **Vælg alle**, og **Kopiér**. Derefter kan du kopiere stablen og ser på fejlen i din foretrukne teksteditor, f.eks.

- Hvis fejlen er fra **SyncRulesEngine**, derefter har stak opkaldsoplysninger først en liste over alle attributter på objektet. Rul ned, indtil du ser overskriften **InnerException = >**.  
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
Linje efter viser fejlen. På billedet ovenfor er fejlen fra en brugerdefineret Synkroniser regel Fabrikam, der er oprettet.

Hvis fejlen selve ikke giver nok oplysninger, er det tid at kigge på selve dataene. Du kan klikke på linket med objekt-id og [følge et objekt og dens data hele systemet](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
