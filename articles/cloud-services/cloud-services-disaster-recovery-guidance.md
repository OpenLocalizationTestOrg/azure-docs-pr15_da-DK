<properties
    pageTitle="Hvad du skal gøre i tilfælde af en Azure service afbrydelser, der påvirker Azure Cloud Services | Microsoft Azure"
    description="Få mere at vide, hvad du skal gøre i tilfælde af en Azure tjenesteforstyrrelse, der påvirker Azure Cloud Services."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Hvad du skal gøre i tilfælde af en Azure service afbrydelser, der påvirker Azure Cloud Services

Hos Microsoft store vi anstrengelser for at sikre, at vores tjenester altid er tilgængelige for dig, når du har brug for dem. Styrker uden for vores kontrol påvirke os nogle gange på måder, der kan medføre ikke-planlagt tjeneste afbrydelser.

Microsoft yder en serviceaftale (SLA) for sine tjenester som en forpligtelse til oppetid og forbindelse til. SLA for enkelte Azure tjenester kan findes på [Azure serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

Azure indeholder allerede mange indbyggede platformfunktioner, der understøtter meget tilgængelige programmer. Få mere at vide om disse tjenester, du kan finde [nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Denne artikel omhandler et SAND nedbrud, når et hele område oplever en afbrydelse på grund af overordnede neutralt nedbrud eller udbredte tjenesten blev afbrudt. Dette er sjældne forekomster, men du skal forberede muligheden for, at der er en afbrydelse af en hel område. Hvis et hele område har en tjenesteforstyrrelse, vil de lokalt overflødige kopier af dine data midlertidigt ikke tilgængelig. Hvis du har aktiveret geografisk gentagelse, gemmes tre flere kopier af dine Azure-lager BLOB og tabeller i et andet område. I tilfælde af en komplet internationale afbrydelse eller nedbrud, ikke er det primære område gendannes, knytter Azure alle DNS-poster til de replikerede geografisk område.

>[AZURE.NOTE]Vær opmærksom på, at du ikke har en hvilken som helst kontrol over denne proces, og det forekommer kun til datacenter hele service afbrydelser. På grund af dette, skal du også stole på andre programmer-specifikke sikkerhedskopiering strategier til at opnå det højeste niveau af tilgængelighed. Yderligere oplysninger finder du i afsnittet om [Data strategier for nedbrud](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Hvis du gerne vil kunne påvirker dine egne failover, kan du overveje at bruge [læseadgang geografisk overflødige lagerplads (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), der opretter en skrivebeskyttet kopi af dine data i et andet område.

For at hjælpe dig med at håndtere disse sjældne forekomster, giver vi de følgende retningslinjer for Azure virtuelle maskiner (VM'er) tale om en tjenesteforstyrrelse af det hele område, hvor din Azure VM programmet installeres.

##<a name="option-1-wait-for-recovery"></a>Mulighed 1: Vent gendannelse
I dette tilfælde er ingen handling fra din side påkrævet. Ved, at Azure teams arbejder fremføre for at gendanne tjenesten kører. Du kan se den aktuelle Tjenestestatus på vores [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Dette er den bedste løsning, hvis en kunde ikke har konfigureret Azure gendannelse af websteder eller har en sekundær installation i et andet område.

For kunder, der vil have øjeblikkelig adgang til deres udløst skytjenester, er følgende indstillinger tilgængelige.

>[AZURE.NOTE]Vær opmærksom på, at disse indstillinger har mulighed for at forekomme tab af data.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Mulighed 2: Installere igen konfigurationen af tjenesten skyen til et nyt område

Hvis du har din oprindelige kode, kan du blot lige Geninstaller det program, tilknyttede konfiguration og de tilknyttede ressourcer til en ny skybaseret tjeneste i et nyt område.  

Se, [hvordan du opretter og installerer en skybaseret tjeneste](./cloud-services-how-to-create-deploy-portal.md)for at få flere detaljer om, hvordan du opretter og installerer et tjenesteprogram i skyen.

Afhængigt af dit program-datakilder skal du kontrollere gendannelse procedurerne for datakilden programmet på computeren.
  * Azure-lager-datakilder, du [Azure-lager gentagelse](../storage/storage-redundancy.md#read-access-geo-redundant-storage) til at se, om de indstillinger, der er tilgængelige baseret på den valgte gentagelse model for dit program.
  * SQL-Database kilder, læse [Oversigt: business løbende og database nedbrud med SQL-Database i skyen](../sql-database/sql-database-business-continuity.md) til at se, om de indstillinger, der er tilgængelige baseret på den valgte gentagelse model for dit program.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Mulighed 3: Brug en sikkerhedskopi installation via Azure trafik Manager
Denne indstilling antages det, at du allerede har designet dit program-løsning med internationale nedbrud huske. Du kan bruge denne indstilling, hvis du allerede har en sekundær cloud services programmet installation, der kører i et andet område og forbindelse via en trafik manager kanal. I dette tilfælde kontrollerer du tilstanden for den sekundære installation. Hvis det er i orden, kan du omdirigere trafik til den via Azure trafik Manager. Med denne strategi, kan du drage fordel af de trafik routing metode og failover rækkefølge konfigurationer i Azure trafik Manager. Se, [hvordan du konfigurerer indstillinger for trafik Manager](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings)kan finde flere oplysninger.

![Justering af Azure Cloud Services på tværs af områder med Azure trafik Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Næste trin

Få mere at vide om at implementere en nedbrud og høj tilgængelighed strategi, under [nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Se [Azure fleksibilitet tekniske retningslinjer](../resiliency/resiliency-technical-guidance.md)for at udvikle en detaljerede tekniske forståelse af en sky platform funktioner.

Kontakt [Kundesupport](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), hvis vejledningen er tvivl om, eller hvis du vil have Microsoft for at udføre handlinger på dine vegne.
