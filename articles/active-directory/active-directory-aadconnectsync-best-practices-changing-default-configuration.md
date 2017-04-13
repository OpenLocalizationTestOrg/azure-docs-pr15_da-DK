<properties
    pageTitle="Azure AD-forbindelse synkronisering: bedste fremgangsmåder til at ændre standardkonfigurationen | Microsoft Azure"
    description="Viser de bedste fremgangsmåder til at ændre standardkonfigurationen af synkronisering af Azure AD-forbindelse."
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
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD-forbindelse synkronisering: bedste fremgangsmåder til at ændre standardkonfigurationen
Formålet med dette emne er til at beskrive understøttes og ikke understøttes ændringer til synkronisering af Azure AD-forbindelse.

Den konfiguration, der er oprettet af Azure AD-forbindelse fungerer "som det er" for de fleste miljøer, der skal synkroniseres lokale Active Directory med Azure AD. Men i nogle tilfælde det er nødvendigt at anvende ændringer på en konfiguration at tilfredsstille en bestemt behov eller kravet om.

## <a name="changes-to-the-service-account"></a>Ændringer til tjenestekontoen
Azure AD-forbindelse Synkroniser kører under en tjenestekonto, der er oprettet af installationsguiden. Denne tjenestekonto indeholder tasterne kryptering til den database, der bruges af Synkroniser. Den er oprettet med en lang adgangskode 127 tegn og adgangskoden er indstillet til ikke at udløbe.

- Det er **ikke-understøttede** til at ændre eller nulstille adgangskoden for kontoen for tjenesten. Hvis du gør det kasserer tasterne kryptering og tjenesten er ikke få adgang til databasen og kan ikke starte.

## <a name="changes-to-the-scheduler"></a>Ændringer til scheduler
Starte med udgaver fra version 1.1 (februar 2016) du kan konfigurere [scheduler](active-directory-aadconnectsync-feature-scheduler.md) skal have en anden synkronisering cyklus end standardlyden 30 minutter.

## <a name="changes-to-synchronization-rules"></a>Ændringer til synkronisering af regler
Installationsguiden indeholder en konfiguration, som er beregnet til at arbejde for de mest almindelige scenarier. I tilfælde af, at du har brug for til at foretage ændringer af konfigurationen, skal du følge disse regler, stadig har en understøttet konfiguration.

- Du kan [ændre attributten flyder](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) , hvis standard direkte attribut flyder ikke er egnet til din organisation.
- Hvis du vil [ikke lade en attribut](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) og fjerne eventuelle eksisterende attributværdier i Azure AD, skal du oprette en regel for dette scenario.
- [Deaktivere en regel for uønskede synkronisering](#disable-an-unwanted-sync-rule) i stedet for at slette den. En slettede regel genskabes under en opgradering.
- [Ændre en regel for ud for feltet](#change-an-out-of-box-rule), skal du oprette en kopi af den oprindelige regel og deaktivere out of box reglen. Du bliver bedt om og hjælper dig med at synkronisere regel Editor.
- Eksportere dine brugerdefinerede synkronisering regler ved hjælp af synkronisering regler Editor. Editoren giver dig et PowerShell-script, som du kan bruge til nemt genskabe dem i et nedbrud.

>[AZURE.WARNING] Synkronisering af out of box reglerne har en miniature. Hvis du foretager en ændring i disse regler, matcher miniature ikke længere. Du muligvis problemer på et senere tidspunkt, når du forsøger at anvende en ny version af Azure AD-forbindelse. Kun ændre den måde, det er beskrevet i denne artikel.

### <a name="disable-an-unwanted-sync-rule"></a>Deaktivere en regel for uønskede synkronisering
Undlad at slette en regel for out of box synkronisering. Det genskabes næste opgraderingen.

I nogle tilfælde har installationsguiden produceret en konfiguration, som ikke fungerer for topologien. Eksempelvis hvis du har en konto er ressourcerelaterede skov topologi, men du har udvidet skema i området konto med Exchange-skema, er derefter regler for Exchange oprettet for konto skoven og området, ressource. I dette tilfælde skal du deaktivere synkronisering af reglen for Exchange.

![Deaktiveret Synkroniser regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Installationsguiden har fundet et gamle Exchange 2003-skema i området konto på billedet ovenfor. Dette skema lokalnummer blev tilføjet, før området, der ressource blev introduceret i Fabrikams miljø. For at sikre, at ingen attributter fra den gamle Exchange-implementering synkroniseres, bør Synkroniser reglen deaktiveres, som vist.

### <a name="change-an-out-of-box-rule"></a>Ændre en regel for out of box
Hvis du vil foretage ændringer i en out of box regel, skal du oprette en kopi af den out of box regel og deaktivere den oprindelige regel. Foretag ændringerne til klonede reglen. Synkroniser regel Editor hjælper dig med disse trin. Når du åbner en regel for out of box, får du vist denne dialogboks:  
![Advarsel om af feltet regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Vælg **Ja** til at oprette en kopi af reglen. Klonede reglen derefter åbnes.  
![Klonede regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Foretag de nødvendige ændringer for omfang, deltage i og transformationer under denne klonede regel.

## <a name="next-steps"></a>Næste trin

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
