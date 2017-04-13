<properties
    pageTitle="Tildele licenser til Azure MFA | Microsoft Azure"
    description="Lær at tildele brugerlicenser til Microsoft Azure Multi-Factor Authentication."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Tildele en Azure MFA, Azure AD Premium eller mobilitet Enterprise-licens til brugere

Hvis du har købt Azure MFA, Azure AD Premium eller Enterprise mobilitet pakke licenser, behøver du ikke at oprette en multi-Factor Auth-udbyder. Når du tildeler licenser til brugerne, kan du begynde at aktivere dem til MFA.

## <a name="to-assign-a-license"></a>Tildele en licens

1. Log på [Azure klassisk portalen](https://manage.windowsazure.com) som administrator.
2. Vælg **Active Directory**i venstre side.
3. Dobbeltklik på den mappe, der indeholder de brugere, du vil aktivere på siden Active Directory.
4. Vælg **licenser**øverst på siden med mappen.
![Tildele licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Vælg **Azure Multi-Factor Authentication**, **Active Directory-Premium**eller **Enterprise mobilitet Suite**på siden licenser.  Hvis du kun har en, skal derefter det være markeret automatisk.
6. Klik på **Tildel**nederst på siden.
![Tildele licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
6. I den dialogboks, der dukker op, skal du klikke på ud for de brugere eller grupper, du vil tildele licenser til.  Du bør se et grønt flueben vises.
7. Klik på ikonet markering for at gemme ændringerne.
![Tildele licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
8. Du bør se en meddelelse om, hvor mange licenser er blevet tildelt og hvor mange kan have mislykkedes.  Klik på **Ok**.
![Tildele licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)
