<properties 
    pageTitle="Kendte netværk | Microsoft Azure" 
    description="Ved at konfigurere kendte netværk, kan du undgå, at IP-adresser, der er ejet af din organisation, der er inkluderet i log ins fra forskellige lande og log ins fra IP-adresser med mistænkelig aktivitetsrapporter." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Kendte netværk


Du kan bruge Azure Active Directorys adgang og anvendelsesrapporter for at få indsigt i integritet og sikkerheden for organisationens adressekartotek. Disse oplysninger, kan en administrator for directory bedre bestemme hvor mulige sikkerhedsrisici kan ligge, så de kan planlægge en dækkende måde at reducere disse risici.

Det er muligt, at rapporterne "*Log in-programmer fra forskellige lande*" og "*Log in-programmer fra IP-adresser med mistænkelige aktiviteter*" forkert flag IP-adresser, der faktisk ejes af din organisation. 

Dette kan ske, for eksempel, når: 

- En bruger på din office har logget på fjernadgang til dine datacenter i San Francisco forhånd udløser rapporten "Log in-programmer fra forskellige lande" 

- En bruger i organisationen forsøger at logge på flere gange med en forkert adgangskode udløsere rapporten "Log in-programmer fra IP-adresser med mistænkelig aktivitet" 

Hvis du vil forhindre, at disse tilfælde generere vildledende sikkerhedsrapporter, skal du tilføje kendte IP-adresseområder til listen over din organisations offentlige IP-adresse.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Hvis du vil føje din organisations offentlige IP-adresseområder, skal du udføre følgende trin: 

1.  Sign-on til [Azure management portal](https://manage.windowsazure.com).

2.  Klik på **Active Directory**i venstre rude. <br><br>![Hvordan fungerer skyen App registrering](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Vælg mappen i fanen **mappe** .

4.  Klik på **Konfigurer**i menuen øverst. <br><br>![Hvordan fungerer skyen App registrering](./media/active-directory-known-networks/known-netwoks-02.png)

5.  På fanen Konfigurer gå til **din organisationer offentlige IP-adresseområder** <br><br>![Hvordan fungerer skyen App registrering](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Klik på **tilføjer kendte IP-adresseområder**.

7.  Tilføje din adresseområder i den dialogboks, der vises, og klik derefter på knappen Kontrollér, når du er færdig. <br><br>![Hvordan fungerer skyen App registrering](./media/active-directory-known-networks/known-netwoks-04.png)


**Yderligere ressourcer**


* [Få vist din access- og brugsrapporter](active-directory-view-access-usage-reports.md)
* [Log in-programmer fra IP-adresser med mistænkelig aktivitet](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Log in-programmer fra forskellige lande](active-directory-reporting-sign-ins-from-multiple-geographies.md)


