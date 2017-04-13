<properties
   pageTitle="Deaktivere eller aktivere et trafik Manager slutpunkt | Microsoft Azure"
   description="I denne artikel kan deaktivere eller aktivere trafik Manager profil slutpunkterne."
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Deaktivere eller aktivere et trafik Manager slutpunkt

Du kan også deaktivere individuelle slutpunkter, som er en del af en trafik Manager profil. Slutpunkter omfatter både skytjenester og websteder. Deaktivere et slutpunkt forlader det som en del af profilen, men profilen, der fungerer som om slutpunktet ikke medtages i den. Denne handling er meget nyttigt til midlertidigt at fjerne et slutpunkt, der er i vedligeholdelsestilstand, eller der genaktiveres. Når slutpunktet er oppe at køre igen, kan det være aktiveret

>[AZURE.NOTE] **Deaktivere et slutpunkt har ingen betydning for med dens installation tilstand i Azure. En sund slutpunkt, forbliver op og kunne modtage trafik selvom deaktiveret i trafik Manager. Desuden kan påvirker deaktivere ark i en profil ikke dens status i en anden profil.**

## <a name="to-disable-an-endpoint"></a>Sådan deaktiverer du et slutpunkt

1. Find den trafik Manager-profil, der indeholder indstillingerne slutpunkt, som du vil ændre i ruden trafik Manager i portalen Azure klassisk, og klik derefter på pilen til højre for profilnavnet. Derved åbnes indstillingssiden for profilen.
1. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der er inkluderet i din konfiguration.
1. Klik på det slutpunkt, som du vil deaktivere, og klik derefter på **Deaktiver** nederst på siden.
1. Trafik stopper der flyder til slutpunktet baseret på DNS-Time-to-Live (TTL) konfigureret for domænenavnet trafik Manager. Du kan ændre TTL fra siden konfiguration af trafik Manager profilen.

## <a name="to-enable-an-endpoint"></a>Aktivere et slutpunkt


1. Find den trafik Manager-profil, der indeholder indstillingerne slutpunkt, som du vil ændre i ruden trafik Manager i portalen Azure klassisk, og klik derefter på pilen til højre for profilnavnet. Derved åbnes indstillingssiden for profilen.
1. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der er inkluderet i din konfiguration.
1. Klik på det slutpunkt, som du vil aktivere, og klik derefter på **Aktivér** nederst på siden.
1. Trafik starter der flyder til tjenesten igen som dikteret af profilen.

## <a name="next-steps"></a>Næste trin

[Trafik Manager - Deaktiver, aktiver eller slette en profil](disable-enable-or-delete-a-profile.md)

[Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)

[Overvejelser i forbindelse med trafik Manager ydeevne](traffic-manager-performance-considerations.md)