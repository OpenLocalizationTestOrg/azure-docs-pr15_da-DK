<properties 
   pageTitle="Konfigurere CHAP til enheden StorSimple | Microsoft Azure"
   description="Beskriver, hvordan du konfigurerer udfordring CHAP Handshake Authentication Protocol () på en enhed med StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurere CHAP til enheden StorSimple

Dette selvstudiet beskrives, hvordan du konfigurerer CHAP til enheden StorSimple. Fremgangsmåden gennemgås i denne artikel gælder for StorSimple 8000 serie samt StorSimple 1200 enheder.

CHAP står for udfordring Password Authentication Protocol. Det er en godkendelse farveskema, der bruges af servere til at validere identiteten af fjerne klienter. Bekræftelsen er baseret på en delt adgangskode eller hemmeligt. CHAP kan være envejs (envejs) eller gensidig (tovejs). Envejs CHAP er, når destinationen godkender en afsender. Fælles eller omvendt CHAP kræver på den anden side, destinationen godkende afsenderen og derefter godkende afsenderen destinationen. Afsenderen godkendelse kan implementeres uden target godkendelse. Target godkendelse kan dog implementeres kun, hvis afsenderen godkendelse implementeres også. 

Som en bedste fremgangsmåde anbefaler vi, at du bruger CHAP til at forbedre iSCSI-sikkerhed.

>[AZURE.NOTE] Husk på, at IPSEC ikke understøttes i øjeblikket på StorSimple enheder.

Indstillingerne CHAP på StorSimple enheden kan konfigureres på følgende måder:

- Envejs eller envejs godkendelse

- Tovejs eller fælles eller omvendt godkendelse

Portal til enheden og serversoftware iSCSI afsenderen skal konfigureres i hver af disse tilfælde. Detaljeret vejledning til denne konfiguration er beskrevet i følgende selvstudiet.

## <a name="unidirectional-or-one-way-authentication"></a>Envejs eller envejs godkendelse

I envejs godkendelse godkender destinationen afsenderen. Denne godkendelse kræver, at du konfigurerer indstillingerne for CHAP afsenderen på StorSimple enheden og iSCSI afsenderen softwaren på værten. De detaljerede procedurer for StorSimple enheden og Windows host er beskrevet nedenfor.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Konfigurere enheden til envejs godkendelse

1. Klik på fanen **Konfigurer** i Azure klassisk portalen, på siden **enheder** .

    ![CHAP afsenderen](./media/storsimple-configure-chap/IC740943.png)

2. Rul ned på denne side og i afsnittet **CHAP afsender** :
                                                    
    1. Angive et brugernavn for din CHAP afsenderen.

    2. Angive en adgangskode til din CHAP afsenderen.

         > [AZURE.IMPORTANT] Brugernavnet CHAP skal indeholde færre end 233 tegn. CHAP adgangskoden skal være mellem 12 og 16 tegn. En længere brugernavn eller adgangskode medfører en fejl ved godkendelse på Windows-vært.
    
    3. Bekræft adgangskoden.

4. Klik på **Gem**. Der vises en bekræftelsesmeddelelse. Klik på **OK** for at gemme ændringerne.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Konfigurere envejs godkendelse på Windows host serveren

1. Start iSCSI afsenderen på Windows host server.

2. I vinduet **iSCSI afsenderen egenskaber** skal du udføre følgende trin:
                                                    
    1. Klik på fanen **registrering** .

        ![iSCSI afsenderen egenskaber](./media/storsimple-configure-chap/IC740944.png)

    2. Klik på **Oplev Portal**.

3. I dialogboksen **Opdage Target portalen** :
                                                    
    1. Angiv IP-adressen på din enhed.

    3. Klik på **Avanceret**.

        ![Opdag target portal](./media/storsimple-configure-chap/IC740945.png)

4. I dialogboksen **Avancerede indstillinger** :
                                                    
    1. Markér afkrydsningsfeltet **Aktivér CHAP logge på** .

    2. Angiv det brugernavn, du har angivet for afsenderen CHAP i portalen klassisk i feltet **navn** .

    3. Angiv den adgangskode, du har angivet for afsenderen CHAP i portalen klassisk i feltet **Target secret** .

    4. Klik på **OK**.

        ![Avancerede indstillinger Generelt](./media/storsimple-configure-chap/IC740946.png)

5. I vinduet **iSCSI afsenderen egenskaber** under fanen **destinationer** skal enhedens status vises som **tilsluttet**. Hvis du bruger en enhed med StorSimple 1200, derefter hver enhed, bliver tilsluttet som et iSCSI-mål som vist nedenfor. Trin 3-4 skal derfor skal gentages for hver lydstyrken.

    ![Enheder tilsluttet som separat destinationer](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Hvis du ændrer iSCSI-navn, bruges det nye navn til nye iSCSI-sessioner. Nye indstillinger er ikke bruges til eksisterende sessioner, indtil du logger af og log på igen.

Gå til [Yderligere overvejelser](#additional-considerations)kan finde flere oplysninger om konfiguration af CHAP på Windows host serveren.


## <a name="bidirectional-or-mutual-authentication"></a>Tovejs eller gensidig godkendelse

Destinationen godkender afsenderen og derefter afsenderen godkender destinationen i tovejs-godkendelse. Dette kræver, at brugeren til at konfigurere indstillingerne for CHAP afsenderen samt modsat CHAP indstillingerne på afsenderen enhed og iSCSI-softwaren på værten. Følgende procedurer beskriver trinnene for at konfigurere fælles godkendelse på enheden og på Windows-vært.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Konfigurere enheden til fælles godkendelse

1. Klik på fanen **Konfigurer** i Azure klassisk portalen, på siden **enheder** .

    ![CHAP target](./media/storsimple-configure-chap/IC740948.png)

2. Rul ned på denne side og i afsnittet **CHAP Target** :
                                                    
    1. Angiv et **omvendt CHAP brugernavn** til din enhed.

    2. Angive en **omvendt CHAP-adgangskode** til din enhed.

    3. Bekræft adgangskoden.

3. I sektionen **CHAP afsender** :
                                                
    1. Angive et **brugernavn** for din enhed.

    1. Angive en **adgangskode** til din enhed.

    3. Bekræft adgangskoden.

4. Klik på **Gem**. Der vises en bekræftelsesmeddelelse. Klik på **OK** for at gemme ændringerne.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurere tovejs godkendelse på Windows host serveren

1. Start iSCSI afsenderen på Windows host server.

2. Klik på fanen **konfiguration** i vinduet **iSCSI afsenderen egenskaber** .

3. Klik på **CHAP**.

4. I dialogboksen **iSCSI afsenderen fælles CHAP Secret** :
                                                    
    1. Skriv **Omvendt CHAP-adgangskode** , du konfigurerede på portalen Azure klassisk.

    2. Klik på **OK**.

        ![iSCSI afsenderen fælles CHAP hemmeligt](./media/storsimple-configure-chap/IC740949.png)

5. Klik på fanen **destinationer** .

6. Klik på knappen **Opret forbindelse** . 

7. Klik på **Avanceret**i dialogboksen **Opret forbindelse til destinationen** .

8. I dialogboksen **Avancerede egenskaber** :
                                                    
    1. Markér afkrydsningsfeltet **Aktivér CHAP logge på** .

    2. Angiv det brugernavn, du har angivet for afsenderen CHAP i portalen klassisk i feltet **navn** .

    3. Angiv den adgangskode, du har angivet for afsenderen CHAP i portalen klassisk i feltet **Target secret** .

    4. Markér afkrydsningsfeltet **Udfør fælles godkendelse** .

        ![Avancerede indstillinger fælles godkendelse](./media/storsimple-configure-chap/IC740950.png)

    5. Klik på **OK** for at fuldføre konfigurationen CHAP
     
Gå til [Yderligere overvejelser](#additional-considerations)kan finde flere oplysninger om konfiguration af CHAP på Windows host serveren.

## <a name="additional-considerations"></a>Yderligere overvejelser

Funktionen **Opret hurtig forbindelse** understøtter ikke forbindelser, der har aktiveret CHAP. Når CHAP er aktiveret, skal du kontrollere, at du bruger knappen **Opret forbindelse** , der er tilgængelige under fanen **destinationer** til at oprette forbindelse til en destination.

![Oprette forbindelse til destination](./media/storsimple-configure-chap/IC740947.png)

Markér afkrydsningsfeltet **Føj denne forbindelse til listen over foretrukne destinationer** i dialogboksen **Opret forbindelse til destinationen** , som præsenteres. Dette sikrer, at hver gang computeren genstartes, gøres et forsøg på at gendanne forbindelsen til iSCSI-foretrukne destinationer.

## <a name="errors-during-configuration"></a>Fejl under konfiguration

Hvis din CHAP konfiguration er forkert, er du sandsynligvis vises en fejlmeddelelse om **godkendelse mislykkedes** .

## <a name="verification-of-chap-configuration"></a>Kontrol af CHAP konfiguration

Du kan kontrollere, at CHAP bruges ved at udføre følgende trin.

#### <a name="to-verify-your-chap-configuration"></a>At bekræfte din CHAP konfiguration

1. Klik på **Foretrukne destinationer**.

2. Vælg den destinationsadresse, du har aktiveret godkendelse.

3. Klik på **Detaljer**.

    ![iSCSI-afsenderen egenskaber foretrukne destinationer](./media/storsimple-configure-chap/IC740951.png)

4. Bemærk angivelsen i feltet **godkendelse** i dialogboksen **Oplysninger om foretrukne mål** . Hvis konfigurationen er fuldført, skal der stå **CHAP**.

    ![Oplysninger om foretrukne mål](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Næste trin

- Lær mere om [StorSimple sikkerhed](storsimple-security.md).

- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
