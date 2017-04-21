<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Installere opdatering 1.2 fra portalen Azure klassisk

1. På siden StorSimple, Vælg din enhed. Gå til **enheder** > **vedligeholdelse**.

2. Nederst på siden skal du klikke på **Scanne opdateringer**. Der oprettes et job for at scanne efter tilgængelige opdateringer. Du får besked, når jobbet er fuldført.

3. Du ser, at nye softwareopdateringer er tilgængelige i sektionen **Softwareopdateringer** på samme side. Vi anbefaler, at du gennemser produktbemærkninger, før du anvender opdatering 1.2 på din enhed.

    ![Installere softwareopdateringer](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. Nederst på siden skal du klikke på **Installer opdateringer**.

5. Du bliver bedt om at bekræfte. Klik på **OK**.

6. Der præsenteres en dialogboks med **Installer opdateringer** . Din enhed skal opfylde de Kontroller, der er angivet i denne dialogboks. Disse trin er fuldført, før du opdateringen. Vælg **jeg forstår det ovennævnte krav og er klar til at opdatere min enhed**. Klik på ikonet Kontrollér.

    ![Bekræftelsesmeddelelsen](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Et sæt af automatisk foreløbig Kontroller nu starter. Dette omfatter:

    - **Controller sundhed kontrollerer** , at bekræfte, at begge enhed enheder er sund og online.
    
    - **Hardware-komponent sundhed kontrollerer** , at bekræfte, at alle hardwarekomponenter på enheden StorSimple er sund.
    
    - **DATA 0 kontrollerer** , at bekræfte, at DATAENE 0 er aktiveret på din enhed. Hvis denne grænseflade ikke er aktiveret, skal du aktivere den, og prøv derefter igen.
    
    - **DATA 2 og 3 DATA kontrollerer** , at bekræfte, at DATA 2 og 3 DATA netværksgrænseflader ikke er aktiveret. Hvis disse grænseflader er aktiveret, der skal du deaktivere dem, og prøv derefter at opdatere din enhed. Denne kontrol udføres kun, hvis du opdaterer fra en enhed, der kører GA software. Enheder, der kører versioner 0,1, 0,2 eller 0,3 behøver ikke denne kontrol.
    
    - **Kontrollere gateway** på en hvilken som helst enhed med en version før opdatering 1. Denne kontrol udføres på alle den enhed, der kører før opdatering 1 software, men mislykkes i de enheder, der har en gateway, der er konfigureret til en netværksgrænseflade end DATA 0.
 
    Opdater 1.2 anvendes kun, hvis alle de ovenstående før opdatering Kontroller er gennemført. Du vil have besked, før opdatering Kontroller er i gang.
  
    ![Foreløbig Kontrollér meddelelse](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    Følgende er et eksempel, hvor opgraderet kontrollen mislykkedes. Du skal bekræfte, at begge enhed enheder er sund og online. Du skal også Sådan kontrollerer du tilstanden af hardwarekomponenter. I dette eksempel skal Controller 0 og 1 Controller komponenter opmærksomhed. Du skal muligvis kontakte Microsoft Support, hvis du ikke kan løse disse problemer ved dig selv.

     ![Foreløbig Kontrollér mislykkedes](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

    > [AZURE.NOTE] Når du har anvendt opdatering 1.2 på enheden StorSimple DATA 2 og 3 DATA kontrollerer og kontrollen gateway ikke længere være nødvendigt til fremtidige opdateringer. De andre foreløbig Kontroller vil stadig være nødvendige.


8. Når den gamle kontrol er gennemført, oprettes en update-job. Du får besked, når kørslen opdatering er blevet oprettet.
 
    ![Opdatere oprettelse af job](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    Opdater bliver herefter anvendt på din enhed.
 
9. Hvis du vil overvåge forløbet af kørslen opdatering, skal du klikke på **Periode**. Du kan se status for opdatering på siden **job** . 

    ![Status for opdatering af job](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. Opdateringen tager et par timer at gennemføre. Du kan få vist oplysninger om jobbet når som helst.

    ![Opdatere oplysninger om job](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Når jobbet er fuldført, skal du gå til siden **vedligeholdelse** og Rul ned til **Softwareopdateringer**.

12. Kontrollér, at enheden kører **StorSimple 8000 serie opdatering 1.2 (6.3.9600.17584)**. **Senest opdateret dato** bør også ændres.

    ![Vedligeholdelsessiden](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Du kan nu se, at vedligeholdelse tilstand opdateringer er tilgængelige. Disse opdateringer er forstyrrende opdateringer, der medfører enhed nedetid og kan kun anvendes via Windows PowerShell brugergrænsefladen på din enhed. Følg vejledningen i [installere vedligeholdelse tilstand opdateringer](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) for at installere disse opdateringer via Windows PowerShell til StorSimple.

> [AZURE.NOTE] I visse tilfælde den meddelelse, der angiver vedligeholdelse tilstand opdateringer er tilgængelige vises muligvis op til 24 timer efter vedligeholdelse tilstand opdateringer er blevet anvendt på enheden.  


