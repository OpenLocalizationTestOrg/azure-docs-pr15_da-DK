<properties 
   pageTitle="Oprette selvsignerede certifikater til punkt-til-Site virtuelt netværk tværs lokale forbindelser ved hjælp af makecert | Microsoft Azure"
   description="I denne artikel indeholder trin til at bruge makecert til at oprette selvsignerede certifikater i Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Arbejde med et selvsigneret certifikat til punkt-til-Site forbindelser

I denne artikel hjælper dig med at oprette et selvsigneret certifikat ved hjælp af **makecert**, og opret derefter klientcertifikater fra den. Trinnene, der skrives til makecert på Windows 10. MakeCert er blevet bekræftet for at oprette certifikater, der er kompatible med P2S forbindelser. 

I P2S forbindelser, den foretrukne metode til certifikater er at bruge din certifikat virksomhedsløsning, og Sørg for at udstede klienten certifikater med almindelige værdi navneformatet 'name@yourdomain.com', i stedet for at formatet 'NetBIOS domænenavn\brugernavn'.

Hvis du ikke har en virksomhedsløsning, er det et selvsigneret certifikat, der er nødvendigt at tillade P2S klienter mulighed for at oprette forbindelse til et virtuelt netværk. Vi har kendskab, makecert frarådes, men det er stadig en gyldig metode til at oprette selvsignerede certifikater, der er kompatible med P2S forbindelser. Vi arbejder på en anden løsning til oprettelse af selvsignerede certifikater, men på nuværende tidspunkt makecert er den foretrukne metode.

## <a name="create-a-self-signed-certificate"></a>Oprette et selvsigneret certifikat

MakeCert er en måde at oprette et selvsigneret certifikat. Følgende trin hjælper dig med at oprette et selvsigneret certifikat ved hjælp af makecert. Disse trin er ikke-implementeringsmodel specifikke. De er gyldige for både ressourcestyring og klassisk.

### <a name="to-create-a-self-signed-certificate"></a>Oprette et selvsigneret certifikat

1. Fra en computer med Windows 10 kan du hente og installere [Windows Software Development Kit (SDK) til Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Efter installationen, kan du finde værktøjet makecert.exe under denne sti: C:\Program Files (x86) \Windows Kits\10\bin\<bue >. 
        
    Eksempel:`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Derefter skal du oprette og installere et certifikat i personligt certifikat store på din computer. I følgende eksempel oprettes en tilsvarende *.cer* -fil, som du har overført til Azure, når du konfigurerer P2S. Kør følgende kommando, som administrator. Erstat *ARMP2SRootCert* og *ARMP2SRootCert.cer* med det navn, du vil bruge til certifikatet.<br><br>Certifikatet, der vil være placeret i din certifikater - aktuelle User\Personal\Certificates.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Du får fat på offentlig nøgle

Som en del af konfigurationen af VPN-Gateway for punkt-til-Site forbindelser, er den offentlige nøgle for certifikatet overført til Azure.

1. Hvis du vil hente en .cer-fil fra certifikatet, Åbn **certmgr.msc**. Højreklik på det selvsignerede certifikat, skal du klikke på **alle opgaver**, og klik derefter på **Eksporter**. Dette åbner **Certifikat guiden Eksporter**.

2. Klik på **Næste**i guiden, Vælg **Nej, Eksporter ikke den private nøgle**, og klik derefter på **Næste**.

3. På siden **Filformat til eksport** Vælg **Base-64-kodet X.509 (. Virksomheden).** Klik derefter på **Næste**. 

4. På den **fil skal eksporteres**, **Gå** til den placering, hvor du vil eksportere certifikat. Navngiv certifikatfilen for **filnavn**. Klik derefter på **Næste**.

5. Klik på **Udfør** for at eksportere certifikat.

 
### <a name="export-the-self-signed-certificate-optional"></a>Eksportere det selvsignerede certifikat (valgfrit)

Du vil eksportere det selvsignerede certifikat og gemme den på en sikker måde. Hvis skal være, du senere kan installere den på en anden computer og oprette flere klientcertifikater eller eksportere en anden .cer-fil. En hvilken som helst computer med et klientcertifikat, der er installeret, og at der også er konfigureret med stort VPN-klienten indstillinger kan oprette forbindelse til netværket virtuel via P2S. Derfor vil du sikre dig, klientcertifikater er oprettet, og kun installeres, når det er nødvendigt og et selvsigneret certifikat er gemt på en sikker måde.

Vælg certifikatet for at eksportere det selvsignerede certifikat som en .pfx, og bruge de samme trin som beskrevet i [eksportere et klientcertifikat](#clientkey) til at eksportere.

## <a name="create-and-install-client-certificates"></a>Oprette og installere klientcertifikater

Du ikke kan installere et selvsigneret certifikat direkte på klientcomputeren. Du vil oprette et klientcertifikat fra et selvsigneret certifikat. Og derefter eksportere og installere klientcertifikat på klientcomputeren. Følgende trin er ikke-implementeringsmodel specifikke. De er gyldige for både ressourcestyring og klassisk.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Del 1 – oprette et klientcertifikat fra et selvsigneret certifikat

Følgende trin fører dig igennem en metode til at oprette et klientcertifikat fra et selvsigneret certifikat. Du kan oprette flere klientcertifikater fra det samme certifikat. Hvert enkelt klientcertifikat kan derefter eksporteres og installeret på klientcomputeren. 

1. Åbn en kommandoprompt på den samme computer, du brugte til at oprette et selvsigneret certifikat, som administrator.

2. I dette eksempel refererer "ARMP2SRootCert" til et selvsigneret certifikat, du har oprettet. 
    - Ændre *"ARMP2SRootCert"* til navnet på selvsigneret roden, du opretter klientcertifikat fra. 
    - Ændre *ClientCertificateName* til det navn, du vil generere et klientcertifikat for at være. 


    Redigere og køre eksemplet for at generere et klientcertifikat. Hvis du kører i følgende eksempel uden at ændre den, er resultatet et klientcertifikat med navnet ClientCertificateName i din personlige lager, der er oprettet ud fra certifikat ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Alle certifikater gemmes i din 'certifikater - aktuelle User\Personal\Certificates' gemme på din computer. Du kan oprette mange klientcertifikater efter behov baseret på denne procedure.

### <a name="clientkey"></a>Del 2 – Eksportér et klientcertifikat

1. Hvis du vil eksportere et klientcertifikat, Åbn **certmgr.msc**. Højreklik på det klientcertifikat, du vil eksportere, klik på **alle opgaver**, og klik derefter på **Eksporter**. Dette åbner **Certifikat guiden Eksporter**.

2. I guiden, skal du klikke på **Næste**, og derefter vælge **Ja, Eksporter privat nøgle**og klik derefter på **Næste**.

3. Du kan lade standardindstillingerne valgt på siden **Filformat til eksport** . Klik derefter på **Næste**. 
 
4. På siden **sikkerhed** skal du beskytte privat nøgle. Hvis du vælger at bruge en adgangskode skal du sørge for at optage eller huske adgangskoden, som du angiver for dette certifikat. Klik derefter på **Næste**.

5. På den **fil skal eksporteres**, **Gå** til den placering, hvor du vil eksportere certifikat. Navngiv certifikatfilen for **filnavn**. Klik derefter på **Næste**.

6. Klik på **Udfør** for at eksportere certifikat.  

### <a name="part-3---install-a-client-certificate"></a>Del 3 – Installer et klientcertifikat

Hver klient, som du vil oprette forbindelse til netværket virtuel ved hjælp af en forbindelse til punkt-til-websted skal have et klientcertifikat, der er installeret. Dette certifikat er ud over den relevante VPN konfigurationspakke. Følgende trin fører dig igennem installerer klientcertifikat manuelt.

1. Find og Kopier *.pfx* -fil på klientcomputeren. Dobbeltklik på filen *.pfx* at installere på klientcomputeren. Lad den **Store placering** som **Aktuelle bruger**, og klik derefter på **Næste**.

2. På **fil** til at importere side, foretag eventuelle ændringer. Klik på **Næste**.

3. Skriv adgangskoden til certifikatet, hvis du bruges af dem, eller Bekræft, at den sikkerhedsadministrator, der installeret certifikatet er korrekte, og klik derefter på **Næste**på siden **beskyttelse af Private nøgler** .

4. Forlade standardplaceringen på siden **Lager** , og klik derefter på **Næste**.

5. Klik på **Udfør**. **Sikkerhedsadvarsel** for installationen af certifikatet, klik på **Ja**. Certifikatet, der er nu importeret.

## <a name="next-steps"></a>Næste trin

Fortsæt med konfigurationen af punkt-til-webstedet. 

- **Ressourcestyring** installation model vejledning, skal du se [konfigurere en punkt-til-Site forbindelse til en VNet ved hjælp af PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Se [konfigurere et punkt-websted VPN-forbindelse til en VNet ved hjælp af portalen klassisk](vpn-gateway-point-to-site-create.md) **Klassisk** installation model trin til at.