<properties 
    pageTitle="Skybaseret App registrering Registreringsindstillingerne for proxytjenester | Microsoft Azure" 
    description="Formålet med dette emne er til at give dig de trin, du skal udføre for at angive den krævede port på computere, der kører skyen App registrering agent." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Skyen App registrering Registreringsindstillingerne for proxytjenester

Agent for skyen App registrering er som standard konfigureret til at bruge kun portene 80 eller 443. Hvis du planlægger at installere skyen App registrering i et miljø med en proxy-server, der bruger en brugerdefineret port (hverken 80 eller 443), skal du konfigurere din supportmedarbejdere for at bruge denne port. Konfigurationen er baseret på en registreringsdatabasenøgle.


Formålet med dette emne er til at give dig de trin, du skal udføre for at angive den krævede port på computere, der kører skyen App registrering agent.



**For at ændre den port, som den computer, der kører skyen App registrering agent skal du udføre følgende trin:**


1. Start Registreringseditor. <br> ![Kør](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Gå til eller oprette følgende registreringsdatabasenøgle: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 

3. Oprette en ny **med flere** strengværdi kaldet **porte**. ![Ny](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Dobbeltklik på værdien porte for at åbne dialogboksen **Rediger flere streng** .


5. I tekstfeltet værdi data skal du skrive følgende værdier og tilføje alle brugerdefinerede porte, der bruges af din organisation: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Redigere streng med flere værdier](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Klik på **OK** for at lukke dialogboksen **Rediger flere streng** .



**Yderligere ressourcer**


* [Hvordan kan jeg finde unsanctioned skyen apps, der bruges inden for min organisation](active-directory-cloudappdiscovery-whatis.md) 


