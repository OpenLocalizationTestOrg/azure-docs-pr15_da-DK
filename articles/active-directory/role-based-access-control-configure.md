<properties
    pageTitle="Brug rollebaseret adgangskontrol i portalen Azure | Microsoft Azure"
    description="Komme i gang med administration af adgang med rollebaseret adgangskontrol i portalen Azure. Brug rolletildelinger til at tildele tilladelser til dine ressourcer."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Bruge rolletildelinger til at administrere adgang til dine Azure abonnementsressourcer

> [AZURE.SELECTOR]
- [Administrere adgangen ved bruger eller gruppe](role-based-access-control-manage-assignments.md)
- [Administrere adgangen af ressource](role-based-access-control-configure.md)

Azure rollebaseret Access kontrolelement (RBAC) gør det muligt for administration af detaljerede adgang til Azure. Ved hjælp af RBAC, kan du give kun mængden af access, at brugerne skal udføre deres arbejde. I denne artikel kan du kommer i gang med RBAC i portalen Azure. Hvis du vil have flere oplysninger om, hvordan RBAC hjælper dig med at administrere adgang, kan du se [Hvad er rollebaseret adgangskontrol](role-based-access-control-what-is.md).

## <a name="view-access"></a>Vis access
Du kan se, hvem der har adgang til en ressource, ressourcegruppe eller abonnement fra den primære blade i [Azure-portalen](https://portal.azure.com). For eksempel vil vi se, hvem der har adgang til en af vores ressourcegrupper:

1. Vælg **grupper** på navigationslinjen til venstre.  
    ![Ressourcegrupper - ikon](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Vælg navnet på ressourcegruppen bladet **ressourcegrupper** .
3. Vælg **adgangskontrol (IAM)** fra menuen til venstre.  
4. Kontrolelementet bladet Access viser en liste over alle brugere, grupper og programmer, der har fået adgang til ressourcegruppen.  

    ![Brugere blade - nedarvede vs tildelt adgang skærmbillede](./media/role-based-access-control-configure/view-access.png)

Meddelelse om, at nogle brugere er blevet **tildelt** adgang til under andre **Inherited** den. Access er tildelt specifikt ressourcegruppen eller nedarvet fra en opgave til det overordnede abonnement.

> [AZURE.NOTE] Klassisk abonnement administratorer og co betragtes som ejere af abonnementet i den nye RBAC model.


## <a name="add-access"></a>Tilføj adgang
Du give adgang til fra på den ressource, ressourcegruppe eller abonnement, der er omfanget af rolletildelingen.

1. Vælg **Tilføj** på bladet Access kontrolelement.  
2. Vælg den rolle, du vil tildele fra bladet **Vælg en rolle** .
3. Vælg den bruger, gruppe eller -programmer i den mappe, du vil give adgang til. Du kan søge i mappen med viste navne, mailadresser og objekt-id'er.  

    ![Tilføje brugere blade – søge skærmbillede](./media/role-based-access-control-configure/grant-access2.png)

4. Vælg **OK** for at oprette opgaven. **Tilføje bruger** pop-up-vinduet sporer status.  
    ![Tilføje bruger statuslinjen - skærmbillede](./media/role-based-access-control-configure/addinguser_popup.png)

Når du har tilføjet en rolletildeling korrekt, vises den på bladet **brugere** .

## <a name="remove-access"></a>Fjerne adgang

1. Vælg den rolletildeling på bladet Access kontrolelementet.
2. Vælg **Fjern** i bladet tildeling detaljer.  
3. Vælg **Ja** for at bekræfte fjernelse.  
    ![Brugere blade - Fjern fra rolle skærmbillede](./media/role-based-access-control-configure/remove-access1.png)

Nedarvede tildelinger kan ikke fjernes. Bemærk i billedet nedenfor, knappen Fjern er nedtonet. Se i stedet på **Tildelt på** detaljer. Gå til den ressource, vises der for at fjerne rolletildelingen.

![Brugere blade - nedarvede access deaktiverer fjerne knappen skærmbillede](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Andre værktøjer til at administrere adgang
Du kan tildele roller og administrere adgang med Azure RBAC kommandoer i værktøjer end Azure portalen.  Følg disse links for at få flere oplysninger om forudsætningerne og komme i gang med Azure RBAC kommandoer.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure kommandolinjen](role-based-access-control-manage-access-azure-cli.md)
- [REST-API](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Næste trin
- [Oprette en access-rapport Skift historik](role-based-access-control-access-change-history-report.md)
- Se [RBAC indbyggede roller](role-based-access-built-in-roles.md)
- Definere dine egne [brugerdefinerede roller i Azure RBAC](role-based-access-control-custom-roles.md)
