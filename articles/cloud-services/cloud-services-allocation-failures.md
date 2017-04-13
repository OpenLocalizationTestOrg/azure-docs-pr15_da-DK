<properties
    pageTitle="Fejlfinding i forbindelse med allokering skyen tjenestefejl | Microsoft Azure"
    description="Fejlfinding i forbindelse med allokering fejl, når du installerer Cloud Services i Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Fejlfinding i forbindelse med allokering fejl, når du installerer Cloud Services i Azure

## <a name="summary"></a>Oversigt
Når du installerer forekomster til en skybaseret tjeneste eller tilføje nyt websted eller arbejder rolle forekomster, tildeler Microsoft Azure Beregn ressourcer. Vises der muligvis nogle gange fejl, når du udfører disse handlinger, lige før du når Azure abonnement grænseværdier. I denne artikel forklares det, årsagerne til nogle af de almindelige allokeringsfejl og foreslår mulige afhjælpning. Oplysningerne kan også være nyttig, når du planlægger installationen for dine tjenester.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Baggrund – Sådan fungerer allokering
Servere i Azure datacentre er opdelt i klynger. En ny skyen allokering serviceanmodning forsøges i flere klynger. Når den første forekomst installeres til en skybaseret tjeneste (i midlertidige eller fremstilling), skybaseret tjeneste bliver fastgjort til en klynge. Eventuelle yderligere installationer for skytjenesten, opstår der i den samme klynge. I denne artikel vil vi henviser til dette som "fastgjort til en klynge". Diagram 1 nedenfor illustrerer en normal tildeling som forsøges i flere clusters små eller store bogstaver Diagram 2 illustreres bogstav i en fordeling, der er fastgjort til klynge 2, fordi det er, hvor eksisterende skyen Service CS_1 er hostet.

![Allokering Diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Hvorfor sker fejl under allokering
Når en anmodning om hukommelsesallokering er fastgjort til en klynge, er der højere mulighed for ikke at finde gratis ressourcer, da den tilgængelige ressourcepulje er begrænset til en klynge. Desuden, hvis din anmodning om hukommelsesallokering er fastgjort til en klynge, men typen ressourcer, du har anmodet om understøttes ikke af klyngen, din anmodning mislykkes selvom klyngen har gratis ressource. Diagram 3 nedenfor vises de tilfælde, hvor en fastgjort allokering mislykkes, fordi den eneste kandidat klynge ikke har ledige ressourcer. Diagram 4 viser de tilfælde, hvor en fastgjort allokering mislykkes, fordi den eneste kandidat klynge ikke understøtter den anmodede VM størrelse, selvom klyngen har gratis ressourcer.

![Fejl under fastgjort allokering](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Fejlfinding i forbindelse med fejl under allokering til skytjenester
### <a name="error-message"></a>Fejlmeddelelse
Du kan få vist følgende fejlmeddelelse:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Almindelige problemer
Her er de almindelige allokering scenarier, der kan medføre en anmodning om en tildeling skal være fastgjort til en enkelt klynge.

- Anvender for midlertidige Slot -, hvis en skybaseret tjeneste har en installation i enten slot, derefter fastgøres hele skytjenesten til en bestemt klynge.  Det betyder, at hvis en installation allerede findes i fremstilling slotten, derefter en ny midlertidige installation kan kun fordeles på den samme klynge som fremstilling slot. Hvis klyngen er ved at nå kapacitet, mislykkes anmodningen.

- Skalering – tilføjelse af nye forekomster til en eksisterende skybaseret tjeneste skal tildele i den samme klynge.  Lille skalering anmodninger kan normalt være tildelt, men ikke altid. Hvis klyngen er ved at nå kapacitet, mislykkes anmodningen.

- Forbindelse gruppe - en ny installation til en tom skybaseret tjeneste kan allokeres af strukturen i en klynge i det pågældende område, medmindre skytjenesten er fastgjort til en forbindelse gruppe. Installationer til gruppen samme forbindelse forsøges på den samme klynge. Hvis klyngen er ved at nå kapacitet, mislykkes anmodningen.

- Forbindelse gruppe vNet - ældre virtuelle netværk er bundet til forbindelsen mellem processorer grupper i stedet for områder, og skytjenester i disse virtuelle netværk ville være fastgjort til forbindelsen mellem processorer gruppe klynge. Installationer til denne type virtuelle netværk vil blive forsøgt på fastgjorte klynge. Hvis klyngen er ved at nå kapacitet, mislykkes anmodningen.

## <a name="solutions"></a>Løsninger

1. Geninstaller til en ny tjeneste i skyen - denne løsning der sandsynligvis er mest vellykket, da der kan platform at vælge mellem alle klynger i det pågældende område.

    - Installere arbejdsbelastningen til en ny skybaseret tjeneste  

    - Opdatere CNAME eller en post for at henvise trafik til den nye tjeneste i skyen

    - Når nul trafik skal det gamle websted, kan du slette det gamle skybaseret tjeneste. Denne løsning skal betale nul nedetid.

2. Slette fremstilling og arrangere pladser – denne løsning bevarer dit eksisterende DNS-navn, men der forårsager nedetid i dit program.

    - Slette fremstilling og arrangere pladser af en eksisterende skybaseret tjeneste, så skytjenesten er tom, og derefter

    - Oprette en ny installation i det eksisterende skybaseret tjeneste. Dette vil igen forsøge at allokering på alle klynger i området. Kontrollér, at skytjenesten ikke er bundet til en forbindelse gruppe.

3. Reserverede IP - denne løsning bevarer din eksisterende IP-adresse adresse, men der forårsager nedetid i dit program.  

    - Oprette en reserveret IP til din eksisterende installation ved hjælp af Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Følg #2 fra et sted over, og Sørg for at angive nye reserveret IP i tjenestens CSCFG.

4. Fjerne forbindelsen mellem processorer gruppe for nye installationer - forbindelse grupper er ikke længere anbefalet. Følg trinnene for #1 ovenfor for at installere en ny tjeneste i skyen. Sørg for skybaseret tjeneste ikke er i en forbindelse gruppe.

5. Konvertere til en internationale Virtual Network - se, [hvordan du overfører fra forbindelse grupper til et internationale virtuelt netværk (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
