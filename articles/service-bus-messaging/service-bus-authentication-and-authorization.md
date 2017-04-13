<properties 
    pageTitle="Service Bus godkendelse og autorisation | Microsoft Azure"
    description="Oversigt over delte Access signatur (SAS) godkendelse."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Service Bus godkendelse og autorisation

Programmer kan godkende til Azure Service Bus ved hjælp af godkendelse enten delt Access signatur (SAS) eller via Azure Active Directory adgangskontrol (også kendt som Access Control Service eller ACS). Delt Access signatur godkendelse giver programmer til at godkende Service Bus ved hjælp af en hurtigtast, der er konfigureret på navneområdet eller på det objekt, som er knyttet specifikke rettigheder. Derefter kan du bruge denne tast til at oprette en delt Access signatur token, kunder kan bruge til at godkende Service Bus.

>AZURE. VIGTIGE SAS anbefales over ACS, da det giver en enkel, fleksibel og nem at bruge godkendelse farveskema for Service Bus. Programmer kan bruge SAS i scenarier, hvor de ikke har brug for at administrere begrebet en autoriseret "bruger."

## <a name="shared-access-signature-authentication"></a>Delt Access signatur-godkendelse

[SAS godkendelse](service-bus-sas-overview.md) gør det muligt at tildele en brugeradgang til tjenesten Bus ressourcer med bestemte rettigheder. SAS godkendelse i Service Bus omfatter konfiguration af en krypteringsnøgle med tilknyttede rettigheder til en tjeneste Bus ressource. Kunder kan derefter få adgang til ressourcen ved at præsentere et SAS token, der består af ressource-URI, der åbnes, og en udløbet signeret med tasten konfigureret.

Du kan konfigurere taster for Sikkerhedstilknytninger på en Service Bus navneområde. Tasten gælder for alle SMS enheder i pågældende navneområde. Du kan også konfigurere taster på Service Bus køer og emner. SAS er også understøttet på Service Bus videresender.

Hvis du vil bruge SAS, kan du konfigurere et [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) objekt på et navneområde, kø eller emne, der består af følgende:

- *Nøglenavn* , der identificerer reglen.

- *Primær nøgle* er et cryptographic nøgle, der bruges til at logge/validere SAS tokens.

- *Sekundær nøgle* er et cryptographic nøgle, der bruges til at logge/validere SAS tokens.

- *Rettigheder* , der repræsenterer samlingen af lytte, Send eller administrere rettigheder.

Regler for godkendelse af konfigureret på niveauet for navneområde kan give adgang til alle objekter i et navneområde for klienter med tokens, der er signeret med den tilsvarende nøgle. Op til 12 denne tilladelse kan være konfigureret regler i en Service Bus navneområde, kø eller emne. En [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) har alle rettigheder er som standard konfigureret til hver navneområde, når den først er klargjort.

For at få adgang til et objekt, kræver klienten et SAS token, der er oprettet ved hjælp af en bestemt [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). SAS Tokenet er genereret ved hjælp af HMAC-SHA256 af en ressourcestreng, der består af den ressource-URI, hvor der anmodes om adgang, og en udløbet med en krypteringsnøgle, der er knyttet til godkendelsesreglen.

Understøttelse af SAS godkendelse for tjenesten Bus er inkluderet i Azure .NET SDK version 2.0 eller nyere. SAS indeholder understøttelse af en [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Alle API'er, der accepterer en forbindelsesstreng som en parameter indeholder support til SAS forbindelsesstrenge.

## <a name="acs-authentication"></a>ACS-godkendelse

Service Bus-godkendelse gennem ACS administreres via en companion "-sb" ACS navneområde. Hvis du vil et companion ACS navneområde skal oprettes til et Service Bus navneområde, kan ikke du oprette din Service Bus navneområde i portalen til Azure klassisk; Du skal oprette navneområdet ved hjælp af [Ny AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) PowerShell-cmdlet. Eksempel:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Hvis du vil undgå at oprette et ACS navneområde, udstede følgende kommando:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Eksempelvis hvis du opretter et Service Bus navneområde kaldet **contoso.servicebus.windows.net**, er et companion ACS navneområde, der hedder **contoso-sb.accesscontrol.windows.net** klargjort automatisk. For alle navneområder, der blev oprettet før August 2014, oprettes et ledsagende ACS navneområde blev også.

En standardidentitet service "ejer," har alle rettigheder, der klargøres som standard i dette companion ACS navneområde. Du kan få detaljerede kontrolelement til en hvilken som helst Service Bus enhed gennem ACS ved at konfigurere de relevante tillidsforhold. Du kan konfigurere yderligere service identiteter til administration af adgang til tjenesten Bus objekter.

For at få adgang til et objekt, klienten anmoder om et SWT token fra ACS med de relevante krav ved at præsentere dens legitimationsoplysninger. SWT tokenet skal derefter sendes som en del af anmodningen til Service Bus til at aktivere godkendelse af klienten til adgang til objektet.

Understøttelse af ACS godkendelse for tjenesten Bus er inkluderet i Azure .NET SDK version 2.0 eller nyere. Denne godkendelse indeholder understøttelse af en [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Alle API'er, der accepterer en forbindelsesstreng som en parameter indeholder support til ACS forbindelsesstrenge.

## <a name="next-steps"></a>Næste trin

Fortsætte med at læse [delt Access signatur godkendelse med Service Bus](service-bus-shared-access-signature-authentication.md) få mere at vide om SAS.

Du kan finde en overordnet oversigt over SAS i Service Bus, [Delt Access signaturer](service-bus-sas-overview.md).

Du kan finde flere oplysninger om ACS tokens i [Sådan: anmode om et Token fra ACS via OAuth OMBRYDE protokollen](https://msdn.microsoft.com/library/hh674475.aspx).



