<properties
   pageTitle="Azure Active Directory rapportering latenstider | Microsoft Azure"
   description="Mængde tid, det tager for rapportering hændelser, der vises i din Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-latencies"></a>Azure Active Directory rapport latenstider

*Denne dokumentation er en del af [Azure Active Directory rapportering Guide](active-directory-reporting-guide.md).*

Rapport                                                  | Minimum  | Gennemsnit    | Maksimum
------------------------------------------------------- | -------- | ---------- | ----------
**Sikkerhedsrapporter**                                    |          |            |
Uregelmæssige logon aktivitet                              | to timer  | 4 timer    | otte timer
Logon fra ukendte kilder                           | to timer  | 4 timer    | otte timer
Logon efter flere fejl                        | to timer  | 4 timer    | otte timer
Logon fra forskellige lande                      | to timer  | 4 timer    | otte timer
Logon fra IP-adresser med mistænkelig aktivitet     | to timer  | 4 timer    | otte timer
Logon fra muligvis inficerede enheder                 | to timer  | 4 timer    | otte timer
Brugere med uoverensstemmende logon-aktiviteter                   | to timer  | 4 timer    | otte timer
Brugere med lækket legitimationsoplysninger                           | to timer  | 4 timer    | otte timer
**Programmet rapporter**                                 |          |            |
Klargøring af aktivitet-konto                           | to timer  | 4 timer    | otte timer
Klargøringsfejl-konto                             | to timer  | 4 timer    | otte timer
Brug af program                                       | to timer  | 4 timer    | otte timer
Adgangskode overgang status                                | to timer  | 4 timer    | otte timer
**Overvågning og aktivitetsrapporter**                            |          |            |
Overvåge rapport                                            | 1 minut | 15 minutter | 30 minutter
Aktivitet (Azure AD) til nulstilling af adgangskode                      | to timer  | 4 timer    | otte timer
Aktivitet (identitet Manager) til nulstilling af adgangskode              | to timer  | 4 timer    | otte timer
Registrering aktivitet (Azure AD) til nulstilling af adgangskode         | to timer  | 4 timer    | otte timer
Registrering aktivitet (identitet Manager) til nulstilling af adgangskode | to timer  | 4 timer    | otte timer
Selv skal kunne grupper aktivitet (Azure AD)                 | to timer  | 4 timer    | otte timer
Selv skal kunne grupper aktivitet (identitet Manager)         | to timer  | 4 timer    | otte timer
**RMS-rapporter**                                         |          |            |
Mest aktive RMS-brugere                                   | to timer  | 4 timer    | otte timer
RMS brugen                                               | to timer  | 4 timer    | otte timer
Brug af RMS enheden                                        | to timer  | 4 timer    | otte timer
Brug af RMS aktiveret program                           | to timer  | 4 timer    | otte timer
**Privat Preview rapporter**                             |          |            |
Alle brugerlogon aktivitet                               | to timer  | 4 timer    | otte timer
