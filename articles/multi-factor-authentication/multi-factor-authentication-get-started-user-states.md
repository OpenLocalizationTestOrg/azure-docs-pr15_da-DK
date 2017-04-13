<properties 
    pageTitle="Microsoft Azure Multi-Factor Authentication bruger tilstande"
    description="Få mere at vide om bruger tilstande i Azure MFA."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>Bruger tilstande i Azure Multi-Factor Authentication

Brugerkonti i Azure Multi-Factor Authentication har følgende tre forskellige tilstande:

Stat | Beskrivelse |Ikke-browserapps påvirkes| Noter
:-------------: | :-------------: |:-------------: |:-------------: |
Deaktiveret | Standardtilstanden for en ny bruger, ikke tilmeldt Multi-Factor authentication.|Nej|Brugeren er ikke ved hjælp af Multi-Factor godkendelse.
Aktiveret |Brugeren er blevet registreret i Multi-Factor authentication.|Nej.  De fortsat fungerer, før registreringsprocessen er fuldført.|Brugeren er aktiveret, men ikke har fuldført registreringsprocessen. De bliver bedt om at fuldføre processen på næste logon.
Tvungen|Brugeren er blevet registreret og er afsluttet registreringsprocessen til brug af godkendelse i flere niveauer.|Ja.  Apps kræver appadgangskoder. | Brugeren kan eller ikke har fuldført registrering. Hvis de har fuldført registreringsprocessen, derefter bruger de godkendelse i flere niveauer. Ellers kan brugeren bliver bedt om at fuldføre processen på næste logon.

## <a name="changing-a-user-state"></a>Ændre en brugertilstand
Tilstanden brugere ændres afhængigt af hvorvidt der er gået konfiguration til MFA og om brugeren har fuldført processen.  Når du slår MFA for en bruger, deaktiveret tilstand ændres fra brugerne aktiverede.  Når den bruger, hvis tilstand er blevet ændret til aktiveret, logger på og fuldfører processen, ændres deres status til tvungen.  

### <a name="to-view-a-users-state"></a>Sådan får du vist en brugers tilstand
--------------------------------------------------------------------------------
1.  Log på **Azure klassisk portalen** som Administrator.
2.  Klik på **Active Directory**i venstre side.
3.  **Directory** Klik under, på mappen for den bruger, du vil aktivere.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klik på **brugere**er placeret øverst.
5.  Klik på **Administrer Multi-Factor Auth**nederst på siden.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Dette åbner en ny fane i browseren.  Du vil kunne få vist tilstanden brugere.
![Klik på mappe](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Hvis du vil ændre tilstanden fra deaktiveret aktiveret
1.  Log på **Azure klassisk portalen** som Administrator.
2.  Klik på **Active Directory**i venstre side.
3.  **Directory** Klik under, på mappen for den bruger, du vil aktivere.
![Klik på Telefonbog](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klik på **brugere**er placeret øverst.
5.  Klik på **Administrer Multi-Factor Auth**nederst på siden.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Dette åbner en ny fane i browseren.  Find den bruger, du vil aktivere til multi-Factor authentication. Du skal muligvis ændre visningen øverst. Sørg for, at status er **deaktiveret.** 
 ![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placer en **kontrollere** i feltet ud for deres navn.
7.  Klik på **Aktiver**i højre side.
![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Klik på **Aktiver Multi-Factor auth**.
![Aktivér bruger](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Der skal være brugerens status er ændret fra **deaktiveret** til **aktiveret**.
![Giver brugerne mulighed for](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Når du har aktiveret dine brugere, anbefales det, at du kontakte dem via mail.  Det bør også informere dem hvordan de kan bruge deres ikke-browserapps for at undgå bliver låst ude.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Ændre tilstanden fra aktiveret/gennemtvinges til deaktiveret
1.  Log på **Azure klassisk portalen** som Administrator.
2.  Klik på **Active Directory**i venstre side.
3.  **Directory** Klik under, på mappen for den bruger, du vil aktivere.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klik på **brugere**er placeret øverst.
5.  Klik på **Administrer Multi-Factor Auth**nederst på siden.
![Klik på mappe](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Dette åbner en ny fane i browseren.  Find den bruger, du vil deaktivere. Du skal muligvis ændre visningen øverst. Sikre, at status er enten **aktiveret** eller **hos.**
7.  Placer en **kontrollere** i feltet ud for deres navn.
7.  Klik på **Deaktiver**i højre side.
![Deaktivere bruger](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Du bliver bedt om at bekræfte dette.  Klik på **Ja**.
![Deaktivere bruger](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Derefter skal du se, at det lykkedes.  Klik på **lukke.** 
 ![Deaktiver bruger](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
