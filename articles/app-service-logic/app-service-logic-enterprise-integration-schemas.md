<properties
    pageTitle="Oversigt over skemaer og Enterprise-Integration Pack | Microsoft Azure"
    description="Lær at bruge skemaer til Enterprise-Integration Pack og logik-apps"
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
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Få mere at vide om skemaer og Enterprise-Integration Pack  

## <a name="why-use-a-schema"></a>Hvorfor bruge et skema?
Bruge skemaer til at bekræfte, at XML-dokumenter, du modtager er gyldige, med de forventede data i et foruddefineret format. Ved hjælp af skemaer bruges til at validere meddelelser, der er udvekslet i et scenarie med B2B.

## <a name="add-a-schema"></a>Tilføje et skema
Fra Azure-portalen:  

1. Vælg **flere tjenester**.  
![Skærmbillede af Azure-portalen, hvor "Flere tjenester" fremhævet](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Angiv **integration**i feltet filter Søg, og vælg **Integration konti** fra på resultatlisten.     
![Skærmbillede af søgefeltet filter](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Vælg den **integration konto** , du tilføjer i skemaet.    
![Skærmbillede af listen over integration af konti](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Vælg feltet **skemaer** .  
![Skærmbillede af IEP Integration-konto, med "Skemaer" fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Tilføje en skemafil, der er mindre end 2 MB  

1. Vælg **Tilføj**bladet **ved hjælp af skemaer** , der åbnes (fra de foregående trin).  
![Skærmbillede af skemaer blade, med knappen "Tilføj" fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Angiv et navn til dit skema. Vælg derefter mappeikonet ud for tekstfeltet **skema** for at overføre skemafilen. Når overførslen er fuldført, skal du vælge **OK**.    
![Skærmbillede af "Tilføj skema", med "Lille fil" fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Tilføje en skemafil, der er større end 2 MB (op til maksimalt 8 MB)  

Processen for dette afhænger af blob objektbeholder adgangsniveauet: **offentlige** eller **ingen anonym adgang**. Vælg den blob beholder, du vil for at finde ud af dette adgangsniveau i **Azure Storage Explorer**under **Blob beholdere**. Vælg **sikkerhed**, og vælg fanen **Adgangsniveau** .

1. Hvis blob access sikkerhedsniveauet er **offentlig**, skal du følge disse trin.  
  ![Skærmbillede af Azure lagerplads Stifinder, med "Blob beholdere", "Sikkerhed" og "Offentlige" fremhævet](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    en. Overføre skemaet til lager, og Kopiér URI.  
    ![Skærmbillede af lagerplads konto med URI fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. Vælg **store filer**i **Tilføj skema**, og giv URI i tekstfeltet **Indhold URI** .  
    ![Skærmbillede af skemaer, med knappen "Tilføj" og "Stor fil" fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Hvis blob access sikkerhedsniveauet er **ingen anonym adgang**, skal du følge disse trin.  
  ![Skærmbillede af Azure lagerplads Stifinder, med "Blob beholdere", "Sikkerhed" og "Ingen anonym adgang" fremhævet](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    en. Overføre skemaet til lagerplads.  
    ![Skærmbillede af lagerplads konto](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Oprette en delt adgang signatur til skemaet.  
    ![Skærmbillede af lagerplads sccount, med delt adgang signaturer fanen fremhævet](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. Vælg **store filer**i **Tilføj skema**, og giv signaturen delt adgang URI i tekstfeltet **Indhold URI** .  
    ![Skærmbillede af skemaer, med knappen "Tilføj" og "Stor fil" fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. Du bør nu se de nyligt tilføjede skema i bladet **skemaer** EIP Integration konto.  
![Skærmbillede af EIP Integration-konto, med "Skemaer" og det nye skema fremhævet](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Redigere skemaer
1. Vælg feltet **skemaer** .  
2. Vælg det skema, du vil redigere fra bladet **ved hjælp af skemaer** , der åbnes.
3. Klik på **Rediger**på bladet **skemaer** .  
![Skærmbillede af skemaer blade](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Vælg den skemafil, du vil redigere ved hjælp af datovælger dialogboksen fil, der åbnes.
5. Vælg **Åbn** i vælgeren til fil.  
![Skærmbillede af fil datovælger](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Du modtager en meddelelse, der angiver, at overførslen blev gennemført.  

## <a name="delete-schemas"></a>Slette ved hjælp af skemaer
1. Vælg feltet **skemaer** .  
2. Vælg det skema, du vil slette fra bladet **ved hjælp af skemaer** , der åbnes.  
3. Vælg **Slet**bladet **skemaer** .
![Skærmbillede af skemaer blade](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Vælg **Ja**for at bekræfte dit valg.  
![Skærmbillede af "Slet skema" bekræftelsesmeddelelsen](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Til sidst skal Bemærk, at listen over skemaer i bladet **skemaer** opdaterer, og du har slettet skemaet ikke længere vises.  
![Skærmbillede af EIP Integration-konto, med "Skemaer" fremhævet](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Næste trin

- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om enterprise integration pakken").  
