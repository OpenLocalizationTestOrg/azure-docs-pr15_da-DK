#<a name="how-to-create-a-custom-virtual-machine"></a>Sådan oprettes en brugerdefineret virtuel maskine

En *brugerdefineret* virtuel maskine refererer til en virtuel maskine, du opretter ved hjælp af metoden **Fra galleriet** , da det kan du konfigurere flere indstillinger end metoden **Hurtig oprettelse** . Disse indstillinger, omfatter:

- Flere valgmuligheder til billedet for at bruge til at oprette den virtuelle maskine (VM)
- Oprette forbindelse VM til et virtuelt netværk
- Tilføje VM til en eksisterende skybaseret tjeneste
- Tilføje VM til et sæt tilgængelighed

> [AZURE.IMPORTANT] Hvis du vil din virtuelle maskine at bruge et virtuelt netværk, så du kan oprette forbindelse til den direkte ved hostname eller konfigurere tværs lokale forbindelser, Sørg for, at du angiver det virtuelle netværk, når du opretter den virtuelle maskine. En virtuel maskine kan konfigureres til at deltage i et virtuelt netværk kun, når du opretter den virtuelle maskine. Se [Azure virtuelle netværk oversigt over](http://go.microsoft.com/fwlink/p/?LinkID=294063)kan finde flere oplysninger om virtuelle netværk.

1. Log på [Azure-portalen](http://manage.windowsazure.com).

2. Klik på **Ny**på værktøjslinjen i.

3. Klik **beregne**på **virtuel maskine**, og klik derefter på **Fra galleriet**.

4. Vælg det billede, du vil bruge, og klik derefter på pilen for at fortsætte.

5. Hvis flere versioner af billedet er tilgængelige i **Version Udgivelsesdato**, skal du vælge den version, du vil bruge.

6. Skriv det navn, du vil bruge til den virtuelle maskine i **Virtual Machine navn**.

7. Brug **lag** og **størrelse** til at vælge den relevante størrelse for den virtuelle maskine. Den størrelse, du vælger påvirker maksimale konfigurationen af den virtuelle maskine, samt de priser. Oplysninger om konfiguration, skal du se [virtuelt og skyen Service størrelser til Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. Skriv et navn for den administrative konto, du vil bruge til at administrere serveren i **Nye brugernavn**.

9. Skriv en stærk adgangskode for den administrative konto i **Ny adgangskode**. Skriv den samme adgangskode i feltet **Bekræft adgangskode**.

10. Klik på pilen for at fortsætte.

11. I **Skybaseret tjeneste**, kan du gøre et af følgende:

    - Hvis dette er den første eller kun virtuelle maskine i skytjenesten, skal du vælge **Opret en ny tjeneste i skyen**. Skriv et navn, der bruger mellem 3 og 24 små bogstaver og tal i **Skyen tjenesten DNS-navn**. Dette navn bliver en del af den URI, der bruges til at kontakte den virtuelle maskine i skyen-tjenesten.
    - Hvis denne virtuelt føjes til en skybaseret tjeneste, kan du vælge den på listen.

    > [AZURE.NOTE] Se, [hvordan du opretter forbindelse virtuelle maskiner i en skybaseret tjeneste](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/)kan finde flere oplysninger om placere virtuelle maskiner i den samme skybaseret tjeneste.

12. Vælg område, forbindelse gruppe eller virtuelt netværk, du vil bruge til den virtuelle maskine i **Område/forbindelse gruppe/virtuelle netværk**. Se [om forbindelse grupper for virtuelt netværk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)kan finde flere oplysninger om forbindelsen mellem processorer grupper.

13. Vælg en eksisterende konto lager til den Virtuelle fil i **Lagerplads konto**, eller brug en automatisk oprettede lagerplads konto. Kun én lagerplads konto per område oprettes automatisk. Alle andre virtuelle maskiner, som du opretter med denne indstilling kan findes i denne lagerplads konto. Du er begrænset til 20 lagerplads konti.

14. Hvis du vil virtuelt skal tilhøre et tilgængelighed sæt i **Tilgængelighed angive**skal du vælge **Opret tilgængelighed** eller føje den til et eksisterende tilgængelighed.

    **Bemærk**: virtuelle maskiner i et sæt tilgængelighed er installeret på forskellige fejl domæner. Placere flere virtuelle maskiner i en tilgængelighed sæt med til at sikre, at dit program er tilgængelig i mislykkede forsøg på netværket, lokal disk hardware mislykkede forsøg og en hvilken som helst planlagt nedetid.

15.  Gennemse de nye slutpunkter, der oprettes for at tillade forbindelser til den virtuelle maskine, sådanne via Fjernskrivebord eller en Secure Shell (SSH) klient under **slutpunkter**. Du kan også tilføje slutpunkter nu, eller oprette dem senere. Finde instruktioner til oprettelse af dem senere, se, [hvordan du angive slutpunkter til en virtuel maskine](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

16.  Beslut, om at installere VM Agent under **VM Agent**. Denne agent indeholder miljø til at installere filtypenavne, der kan hjælpe dig med at interagere med den virtuelle maskine. Yderligere oplysninger finder du [Administrere filtypenavne](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Klik på pilen for at oprette den virtuelle maskine.

    ![Oprettelse af brugerdefinerede virtuelt vellykket](./media/howto-custom-create-vm/VMSuccessWindows.png)

##<a name="next-steps"></a>Næste trin##
Når den virtuelle maskine er oprettet, er det startes automatisk. Når portalen viser status som kører, kan du logge på den virtuelle maskine. Yderligere oplysninger finder du se en af følgende artikler:

- [Hvordan du kan logge på en virtuel maskine kører Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Hvordan du kan logge på en virtuel maskine, der kører Windows Server](../articles/virtual-machines/virtual-machines-windows-classic-connect-logon.md)

