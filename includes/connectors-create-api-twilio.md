### <a name="prerequisites"></a>Forudsætninger
- En Twilio-konto
- Et bekræftet Twilio telefonnummer, der kan modtage SMS
- En bekræftet Twilio telefonnummer, du kan sende SMS

>[AZURE.NOTE] Hvis du bruger en prøveversion Twilio-konto, kan du kun sende SMS til **bekræftet** telefonnumre.  

Før du kan bruge kontoen Twilio i en logik app, skal du tillade logik app til at oprette forbindelse til kontoen Twilio. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen. 

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til kontoen Twilio:

1. For at oprette en forbindelse til Twilio, i logik app designer skal du markere **Vis Microsoft administrerede API'er** i på rullelisten derefter angive *Twilio* i søgefeltet. Vælg den udløser eller handling, du vil gerne bruge:  
  ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Hvis du ikke har oprettet en hvilken som helst forbindelser til Twilio før, får du bedt om at angive din Twilio legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen Twilio data:  
  ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Du skal bruge **Twilio konto-id** og **Twilio adgangstoken** fra dashboardet i Twilio, så log på kontoen Twilio nu at fange disse to dele af oplysninger:  
  ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio og logik apps bruge forskellige navne til at identificere disse to forskellige elementer. Her er, hvordan du skal knytte dem til dialogboksen logik apps:![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Vælg knappen **Opret forbindelse** :  
  ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Meddelelse om forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik:  
  ![](./media/connectors-create-api-twilio/twilio-5.png)