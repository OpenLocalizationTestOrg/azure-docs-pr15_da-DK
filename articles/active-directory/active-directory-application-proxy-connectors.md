<properties
    pageTitle="Arbejde med Azure AD-proxyen forbindelser | Microsoft Azure"
    description="Omhandler, hvordan du kan oprette og administrere grupper af forbindelser i Azure AD-proxyen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Udgive programmer på separate netværk og placeringer, der bruger forbindelse grupper

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure klassisk portal](active-directory-application-proxy-connectors.md)


Forbindelse grupper er nyttige til forskellige scenarier, herunder:

- Websteder med flere forbundne datacentre. I dette tilfælde, vil du beholde så meget trafik inden for datacenteret som muligt, fordi kryds-datacenter links er som regel dyr og langsom. Du kan installere forbindelser i hver datacenter at kunne levere kun de programmer, der er placeret i datacenteret. Denne fremgangsmåde minimerer kryds-datacenter links og giver en helt gennemsigtig oplevelse til brugerne.
- Administration af programmer på isolerede netværk, der ikke er en del af den primære virksomhedens netværk. Du kan bruge connector grupper til at installere dedikeret forbindelser på isolerede netværk til at isolere også programmer til netværket.
- For programmer på IaaS til skyadgang, giver forbindelse grupper en fælles tjeneste for at sikre adgangen til alle apps. Forbindelse grupper ikke oprette flere afhængighed på virksomhedens netværk, eller fragmentere app oplevelse. Forbindelser kan være installeret på hver skyen datacenter og fungerer kun programmer, der er placeret på dette netværk. Du kan installere flere forbindelser for at opnå høj tilgængelighed.
- Understøttelse af flere områder miljøer bestemte forbindelser kan installeres per skov og angive at kunne levere bestemte programmer.
- Forbindelse grupper kan bruges som sikkerhedskopi eller i nedbrud websteder til at registrere enten failover for det overordnede websted.
- Forbindelse grupper kan også bruges til at betjene flere firmaer fra en enkelt lejer.

## <a name="prerequisite-create-your-connectors"></a>Forudsætninger: Oprette dine forbindelser
Hvis du vil gruppere dine forbindelser, du har Sørg for, at du har [installeret flere forbindelser](active-directory-application-proxy-enable.md), og, du navngiver dem og derefter gruppere dem. Du skal tildele dem til bestemte apps.

## <a name="step-1-create-connector-groups"></a>Trin 1: Oprette forbindelse grupper
Du kan oprette forbindelse så mange grupper, som du vil have. Oprettelse af forbindelse gruppe opnås i portalen Azure klassisk.

1. Vælg mappen, og klik på **Konfigurer**.  
    ![Proxyen, konfigurere skærmbillede – Klik på Administrer grupper af forbindelse](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Klik på **Administrer forbindelse grupper** under proxyen, og Opret en ny forbindelse gruppe ved at give gruppen et navn.  
    ![Programmet proxy forbindelse grupper skærmbillede - navnet ny gruppe](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Trin 2: Tildele forbindelser til dine grupper
Når grupperne forbindelsen er oprettet, kan du flytte forbindelserne til den relevante gruppe.

1. Klik på **Administrer forbindelser**under **Proxyen**.
2. Vælg den gruppe, du vil for hver forbindelse under **gruppen**. Det kan tage forbindelserne op til 10 minutter er aktiv i gruppen ny.  
    ![Programmet proxy forbindelser skærmbillede - gruppe af udvalgte rullemenuen](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Trin 3: Tildele programmer til din forbindelse grupper
Det sidste trin er at angive hver enkelt program til gruppen forbindelse, der skal fungere den.

1. Vælg det program, du vil knytte til gruppen, og klik på **Konfigurer**på Azure-klassisk portalen i dit katalog skal.
2. Vælg den gruppe, du vil bruge programmet under **forbindelse gruppe**. Denne ændring anvendt øjeblikkeligt bliver.  
    ![Programmet proxy forbindelse gruppe skærmbillede - gruppe af udvalgte rullemenuen](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Se også

- [Aktivere proxyen](active-directory-application-proxy-enable.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)
- [Foretage fejlfinding af problemer, du har kørende med proxyen](active-directory-application-proxy-troubleshoot.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
