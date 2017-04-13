<properties
    pageTitle="Azure Multi-Factor Authentication rapporter"
    description="Dette beskriver, hvordan du bruger funktionen Azure Multi-Factor Authentication - rapporter."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication indeholder flere rapporter, der kan anvendes af du og din organisation. Disse rapporter kan åbnes via Multi-Factor Authentication Management Portal, som kræver, at du har en Azure MFA-udbyder, eller en Azure MFA, Azure AD Premium eller Enterprise mobilitet Suite licens. Følgende er en liste over de tilgængelige rapporter.

Du kan få adgang til rapporter via Azure Management-portalen.

Navn| Beskrivelse
:------------- | :------------- |
Brugen | Brug rapporter vises oplysninger om overordnede brugen, bruger oversigt og oplysninger om bruger.
Serverens Status|Denne rapport viser status for multi-Factor Authentication servere, der er knyttet til din konto.
Oversigt over blokerede bruger|Disse rapporter vise oversigten over anmodninger om at blokere eller fjerne blokeringen af brugere.
Oversigt over filsystemscachen bruger|Viser oversigten over anmodninger om at tilsidesætte Multi-Factor Authentication for en brugers telefonnummer.
Svindel besked|Viser en oversigt over advarsler om svindel sendt det datointerval, du har angivet.
I kø|Lister rapporter i kø til behandling og deres status. Et link til, eller få vist rapporten er angivet, når rapporten er fuldført.

## <a name="to-view-reports"></a>Vise rapporter

1.  Logge på http://azure.microsoft.com
2.  Vælg Active Directory i venstre side.
3.  Vælg en af følgende indstillinger:
    - **Mulighed 1**: Klik på fanen Multi-Factor Auth udbydere. Markér udbyderen af MFA, og klik på knappen Administrer nederst.
    - **Mulighed 2**: vælge mappen og klikke på fanen Konfigurer. Vælg Administrer Tjenesteindstillinger under afsnittet Multi-Factor authentication. Klik på Gå til linket portalen nederst på siden MFA-Tjenesteindstillinger.
4.  I portalen Azure Multi-Factor Authentication Management får du vist visningen sektion i rapporten i venstre navigationsrude. Her kan du vælge de rapporter, der er beskrevet ovenfor.

<center>![Skyen](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Yderligere ressourcer**

* [For brugere](./end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication på MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
