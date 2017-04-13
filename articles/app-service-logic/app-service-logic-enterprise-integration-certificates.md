
<properties
    pageTitle="Ved hjælp af certifikater med Enterprise Integration Pack | Microsoft Azure"
    description="Lær, hvordan du bruger certifikater med Enterprise Integration Pack og logik Apps"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="deonhe"/>

# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Få mere at vide om certifikater og Enterprise-Integration Pack

## <a name="overview"></a>Oversigt
Virksomhedsintegration bruger certifikater til at sikre B2B kommunikation. Du kan bruge to typer certifikater i din virksomhed integration apps:

- Offentlige certifikater, som skal købes fra et nøglecenter (CA).
- Privat certifikater, som du kan udstede dig selv. Certifikaterne kaldes nogle gange selvsignerede certifikater.


## <a name="what-are-certificates"></a>Hvad er certifikater?
Certifikater er digitale dokumenter, der bekræfte identiteten af de andre deltagere i elektronisk kommunikation og, der også sikre elektronisk kommunikation.

## <a name="why-use-certificates"></a>Hvorfor bruge certifikater?
Nogle gange skal B2B kommunikation behandles fortroligt. Virksomhedsintegration bruger certifikater til at sikre disse kommunikation på to måder:

- Ved at kryptere indholdet af meddelelser
- Ved at signere meddelelser  

## <a name="how-do-you-upload-certificates"></a>Hvordan du overføre certifikater?

### <a name="public-certificates"></a>Offentlige certifikater
Hvis du vil bruge et *offentligt certifikat* i dine logik apps med B2B-funktioner, skal du først overføre certifikatet til kontoen integration. Hvis du vil bruge et *selvsigneret certifikat*, på den anden side skal du først overføre den til [Azure nøgle samling](../key-vault/key-vault-get-started.md "Få mere at vide om nøgle samling").

Når du overfører et certifikat, der kan hjælpe dig med at sikre dine B2B meddelelser, når du definerer deres egenskaber i de [aftaler](./app-service-logic-enterprise-integration-agreements.md) , som du opretter.  

Her er den detaljerede vejledning til at overføre dine offentlige certifikater til kontoen integration, når du logger på portalen Azure:

1. Vælg **Gennemse**.  
    ![Vælg Gennemse](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Angiv **integration** i feltet filter Søg, og vælg derefter **Integration konti** fra på resultatlisten.     
    ![Vælg Integration konti](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Vælg kontoen integration, du vil tilføje certifikatet.  
    ![Vælg kontoen integration, du vil tilføje certifikatet](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Vælg feltet **certifikater** .  
    ![Vælg feltet certifikater](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. Vælg knappen **Tilføj** i bladet **certifikater** , der åbnes.
    ![Vælg knappen Tilføj](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Angiv et **navn** til certifikatet, og vælg derefter certifikattypen. (I dette eksempel brugte vi den offentlige certifikattype.) Vælg derefter mappeikonet i højre side af tekstboksen **certifikat** .

7. Når datovælgeren fil åbnes, skal du finde og vælge den certifikatfil, du vil overføre til kontoen integration.

8. Vælg certifikatet, og klik derefter på **OK** i vælgeren til fil. Dette valideret og overfører certifikatet til kontoen integration.

8. Markér knappen **OK** på bladet **Tilføj certifikat** igen.  
    ![Vælg knappen OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. I om et minut, får du vist en meddelelse, der angiver, at certifikat overførslen er færdig.

10. Vælg feltet **certifikater** . Du bør se det certifikat, der er tilføjet for nylig.  
    ![Se det nye certifikat](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Privat certifikater
Du kan overføre private certifikater til kontoen integration ved at gøre følgende:  

1. [Overføre din private nøgle til nøgle samling] (../key-vault/key-vault-get-started.md "Få mere at vide om vigtige samling").  

    > [AZURE.TIP] Du skal Godkend funktionen logik Apps Azure App-tjenesten til at udføre handlinger på tasten samling. Du kan give adgang til tjenesten hovedstolen logik Apps ved hjælp af følgende PowerShell-kommando:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Oprette en privat certifikat.  

3. Overføre privat certifikatet til kontoen integration.

Når du har truffet de forrige trin, kan du bruge det private certifikat til at oprette aftaler.

Følgende er den detaljerede vejledning til at overføre dine private certifikater til kontoen integration, når du logger på portalen Azure:  

1. Vælg **Gennemse**.  
    ![Overføre dine private certifikater til kontoen integration](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Angiv **integration** i feltet filter Søg, og vælg derefter **Integration konti** fra på resultatlisten.     
    ![Vælg Integration konti](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Vælg kontoen integration, du vil tilføje certifikatet.  
    ![Vælg kontoen integration, du vil tilføje certifikatet](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Vælg feltet **certifikater** .  
    ![Vælg feltet certifikater](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. Vælg knappen **Tilføj** i bladet **certifikater** , der åbnes.
    ![Vælg knappen Tilføj](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Angiv et **navn** til certifikatet, og vælg derefter certifikattypen. (I dette eksempel brugte vi den offentlige certifikattype.) Vælg derefter mappeikonet i højre side af tekstboksen **certifikat** .

7. Når datovælgeren fil åbnes, skal du finde og vælge den certifikatfil, du vil overføre til kontoen integration.

8. Når du har valgt certifikatet, klikke på **OK** i vælgeren til fil. Denne handling valideret certifikatet og overfører den til kontoen integration.

9. Markér knappen **OK** på bladet **Tilføj certifikat** igen.  
    ![Tilføje certifikat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. I om et minut, får du vist en meddelelse, der angiver, at certifikat overførslen er færdig.

11. Vælg feltet **certifikater** . Du bør se det certifikat, der er tilføjet for nylig.
    ![Se det nye certifikat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Når du overfører et certifikat, der kan hjælpe dig med at sikre dine B2B meddelelser, når du definerer deres egenskaber i [aftaler](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Næste trin
- [Oprette en logik app, der bruger B2B funktioner](./app-service-logic-enterprise-integration-b2b.md)  
- [Oprette en B2B aftale](./app-service-logic-enterprise-integration-agreements.md)  
- [Lær mere om nøgle samling] (../key-vault/key-vault-get-started.md "Få mere at vide om vigtige samling")  
