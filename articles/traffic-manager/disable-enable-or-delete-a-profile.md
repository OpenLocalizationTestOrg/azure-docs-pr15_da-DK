<properties
   pageTitle="Deaktivere, aktivere eller slette en trafik Manager profil | Microsoft Azure"
   description="Denne artikel kan hjælpe dig med at arbejde med dine trafik Manager profiler."
   services="traffic-manager"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Deaktivere, aktivere eller slette en profil


Du kan deaktivere en eksisterende trafik Manager-profil, så den ikke refererer anmodninger til slutpunkterne konfigureret. Når du deaktiverer en trafik Manager profil, selve profilen og oplysningerne i profilen, forbliver intakt og kan redigeres i grænsefladen trafik Manager. Når du vil genaktivere profilen, kan du nemt gøre dette i Azure portal og henvisninger fortsætter. Når du opretter en trafik Manager profil på portalen Azure, er det automatisk aktiveret.

## <a name="to-disable-a-profile"></a>Deaktivere en profil

1. Ændre ressource DNS-post på din Internet DNS-server at bruge den relevante post af typen og markøren til et andet navn eller IP-adressen på en bestemt placering på internettet. Ændre med andre ord ressource DNS-post på din Internet DNS-server, så den ikke længere bruger en CNAME-ressourcepost, der peger på domænenavnet for din trafik Manager profil.
1. Trafik stopper blive bedt om at slutpunkterne gennem profilindstillinger trafik Manager.
1. Vælg den profil, du vil deaktivere. For at vælge profilen, på siden trafik Manager skal du fremhæve profilen ved at klikke på kolonnen ud for profilnavnet. Skal ikke klikke på navnet på profilen eller på pilen ud for navnet, som dette kommer du til indstillingssiden for profilen.
1. Når du har valgt profilen, skal du klikke på Deaktiver nederst på siden.

## <a name="to-enable-a-profile"></a>Aktivere en profil

1. Markér den profil, du vil aktivere. For at vælge profilen, på siden trafik Manager skal du fremhæve profilen ved at klikke på kolonnen ud for profilnavnet. Skal ikke klikke på navnet på profilen eller på pilen ud for navnet, som dette kommer du til indstillingssiden for profilen.
1. Når du har valgt profilen, skal du klikke på Aktivér nederst på siden.
1. Ændre ressource DNS-post på din Internet DNS-server til at bruge CNAME record type, som er tilknyttet domænenavnet for din profil trafik Manager virksomhedens domænenavn. Du kan finde yderligere oplysninger finder [punkt et Firmadomæne Internet til et trafik Manager domæne](traffic-manager-point-internet-domain.md).
1. Trafik begynder at blive ført til slutpunkterne igen.

## <a name="delete-a-profile"></a>Slette en profil


1. Sikre, at ressource DNS-post på din Internet DNS-server ikke længere bruger en CNAME-ressourcepost, der peger på domænenavnet for din trafik Manager profil.
1. Markér den profil, du vil slette. For at vælge profilen, på siden trafik Manager skal du fremhæve profil ved at
1. Klik på kolonnen ud for profilen. Skal ikke klikke på navnet på profilen eller på pilen ud for navnet, som dette kommer du til indstillingssiden for profilen.
1. Når du har valgt profilen, skal du klikke på Slet nederst på siden.

## <a name="next-steps"></a>Næste trin

[Trafik Manager - deaktivere eller aktivere et slutpunkt](disable-or-enable-an-endpoint.md)

[Konfigurere failover routing metode](traffic-manager-configure-failover-routing-method.md)

[Konfigurere round robin routing metode](traffic-manager-configure-round-robin-routing-method.md)

[Konfigurere ydeevne routing metode](traffic-manager-configure-performance-routing-method.md)

[Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)

