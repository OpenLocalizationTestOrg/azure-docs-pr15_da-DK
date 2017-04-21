
1. Log på [Azure Management Portal](http://manager.windowsazure.com) (dette er den gamle portal) på den lokale computer.

2. Nederst i navigationsruden skal du vælge **+ Ny** > **App Services** > **BizTalk Service** > **Brugerdefineret oprette**.

3. Angiv et **Navn på BizTalk-tjeneste** , og vælg en **Edition**. 

    Dette selvstudium bruger **mobile1**. Du skal angive et entydigt navn til din nye BizTalk Service.

4. Når BizTalk Service er blevet oprettet, Vælg fanen **Hybrid forbindelser** og derefter klikke på **Tilføj**.

    ![Føje Hybrid forbindelse](./media/hybrid-connections-create-new/3.png)

    Dette opretter en ny hybrid-forbindelse.

5. Angiv et **navn** og **Værtsnavn** for hybride forbindelsen, og angiv **Port** `1433`. 
  
    ![Konfigurere Hybrid forbindelse](./media/hybrid-connections-create-new/4.png)

    Værtsnavnet er navnet på serveren, lokalt. Dette konfigurerer hybrid forbindelse for at få adgang til SQL Server, der kører på port 1433. Hvis du bruger en navngivet SQL Server-forekomst, i stedet bruge statisk port du definerede tidligere.

6. Når den nye forbindelse er oprettet, status for det på den nye forbindelse viser **lokale installationen ufuldstændig**.

7. Gå tilbage til din mobile service, klik på **Konfigurer**, Rul ned til **Hybrid forbindelser** og klik på **Tilføj hybrid forbindelse**, derefter vælge den hybrid forbindelse, du lige har oprettet og klik på **OK**.

    Dette gør det muligt for dine mobile tjeneste til at bruge din nye hybrid-forbindelse.

Derefter skal du installere Forbindelsesstyring Hybrid på den lokale computer.