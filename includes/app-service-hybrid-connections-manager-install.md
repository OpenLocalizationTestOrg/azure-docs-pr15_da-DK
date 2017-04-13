
1. Klik på den hybrid forbindelse, du lige har oprettet i bladet **Hybrid forbindelser** og derefter klikke på **Lytteren installationen**.
    
    ![Klik på lytteren konfiguration](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. Bladet **Hybrid forbindelsesegenskaber** åbnes. Vælg **hente og konfigurere manuelt**under **Lokale Hybrid Forbindelsesstyring**, Gem hentede HybridConnectionManager.msi pakken, og Kopiér forbindelsesstrengen gateway.
    
    ![Klik her for at installere](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. Skriv følgende kommando for at starte installationsprogrammet fra en administratorkommandoprompt:

        start HybridConnectionManager.msi
 
7. Når du kører installationsprogrammet, skal du klikke på **ikke nu**, og derefter gå til mappen %ProgramFiles%\Microsoft\HybridConnectionManager, køre HCMConfigWizard.exe og klikke på **Ja** i dialogboksen **Kontrol af brugerkonti** .
        
7. Indsæt hybrid forbindelsesstrengen, som du kopierede tidligere, og klik på **OK**. 
    
    ![Installation af](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Når installationen er fuldført, skal du klikke på **Luk**.
    
    ![Klik på Luk](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    På bladet **Hybrid forbindelser** viser kolonnen **Status** nu **tilsluttet**. 
    
    ![Forbundne Status](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)