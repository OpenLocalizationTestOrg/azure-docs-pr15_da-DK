<properties
    pageTitle="Azure Active Directory ofte stillede spørgsmål om | Microsoft Azure"
    description="Azure Active Directory ofte stillede spørgsmål, som indeholder et svar på spørgsmålene i sammen med adgang til Azure og Azure Active Directory, administration og programmet adgang i adgangskode."
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
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Azure Active Directory ofte stillede spørgsmål

Azure Active Directory er en omfattende identitet som en tjeneste (IDaaS)-løsning, der strækker sig over alle aspekter af identitet, administration af adgang og sikkerhed.


Yderligere oplysninger finder du [Hvad er Azure Active Directory?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Få adgang til Azure og Azure Active Directory


**Sp: Hvorfor får jeg "ikke fundet nogen abonnementer" Når jeg forsøger at få adgang til Azure AD i Azure klassisk portal (https://manage.windowsazure.com)?**

**A:** Få adgang til portalen Azure klassisk kræver, at hver enkelt bruger skal have tilladelser på en Azure-abonnement. Hvis du har et betalt Office 365 eller Azure AD gå til [http://aka.ms/accessAAD](http://aka.ms/accessAAD) for et enkeltstående aktivering trin, ellers skal du aktivere en fuld [Azure prøveversion](https://azure.microsoft.com/pricing/free-trial/) eller et betalt abonnement. 

Du kan finde flere oplysninger i:

- [Hvordan Azure abonnementer er knyttet til Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Administrere mappen for dit Office 365-abonnement i Azure](active-directory-manage-o365-subscription.md)

---

**Sp: Hvad er forholdet mellem Azure AD Office 365 og Azure?**

**A:** Azure Active Directory giver dig almindelige identitets- og -funktioner til alle Microsoft online services. Om du bruger Office 365, Microsoft Azure, Intune eller andre, bruger du allerede en Azure AD til at aktivere enkeltlogon og få adgang til administration for alle disse tjenester. 

Faktisk er alle de brugere, du har aktiveret for Microsoft Online services defineret som brugerkonti i en eller flere Azure AD-forekomster. Du kan aktivere disse konti gratis Azure AD-funktioner som programmet skyadgang.
 
Desuden Azure AD betalt tjenester (f.eks.: Azure AD basic Premium, EMS, osv.) supplement andre tjenester som Office 365 og Microsoft Azure med omfattende skala administration og sikkerhed virksomhedsløsninger Online.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Introduktion til Hybrid Azure AD


**Sp: Hvordan kan jeg oprette forbindelse min lokal adresseliste til Azure AD?**

**A:** Du kan oprette forbindelse til din lokale mappe til Azure AD ved hjælp af **Azure AD-forbindelse**. 

Få mere at vide ved at se [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).


---

**Sp: Hvordan konfigurerer jeg SSO mellem min lokale mappe og mine cloud-programmer?**

**A:** Du skal kun konfigurere SSO mellem dit lokale katalog og Azure AD. Så længe du få adgang til dine skyen programmer via Azure AD-tjenesten automatisk drev dine brugere at godkende korrekt med deres lokale legitimationsoplysninger.

Implementere SSO fra det lokale kan nemt opnås med sammenslutning løsninger som ADFS eller ved at konfigurere synkronisering af adgangskoder hash. Du kan nemt installere begge muligheder, ved hjælp af konfigurationsguiden af Azure AD-forbindelse.
  

Få mere at vide ved at se [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
  

---

**Sp: Azure Active Directory giver en selvbetjening portal for brugere i min organisation?**

**A:** Ja, Azure Active Directory giver dig [Azure AD Access Panel](http://myapps.microsoft.com) for bruger selvbetjening og adgang til programmer. Hvis du er Office 365-kunde, kan du finde mange af de samme funktioner i Office 365-portalen. 

Yderligere oplysninger finder du se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md). 



---

**Sp: Azure AD hjælper mig med at administrere mine lokale infrastruktur?**

**A:** Ja, gør den. Azure AD Premium edition giver dig **Forbinde tilstand**. Azure AD forbinde sundhed hjælper dig med at overvåge og få indsigt i din lokale identitet infrastruktur og synkronisering af tjenesterne.  

Få mere at vide i [overvåge dine lokale identitet infrastruktur og synkronisering services i skyen](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Administration af adgangskoder

**Sp: kan jeg bruge Azure AD adgangskode tilbageskrivning uden synkronisering af adgangskoder? (Også kendt, jeg vil gerne bruge Azure AD SSPR med adgangskode tilbageskrivning men jeg vil ikke have dine adgangskoder, der er gemt i cloud?)**

**A:** Du behøver ikke at synkronisere dine AD adgangskoder til Azure AD for at aktivere tilbageskrivning. I et medlem af organisationsnetværket miljø er Azure AD SSO afhængig af den lokale mappe til at godkende brugeren. Dette scenarie kræver ikke lokalt adgangskoden til spores i Azure AD.

---

**Sp: hvor lang tid tager det for en adgangskode skal skrives tilbage til AD lokalt?**

**A:** Adgangskode tilbageskrivning fungerer i realtid. 

Få mere at vide i [Introduktion til administration af adgangskoder](active-directory-passwords-getting-started.md) 


---

**Sp: kan jeg bruge adgangskode tilbageskrivning med adgangskoder, der administreres af en administrator?**

**A:** Ja, hvis du har adgangskode tilbageskrivning aktiveret, handlingerne adgangskode er udført af en administrator skrives tilbage til dit lokale miljø.  

Relaterede spørgsmål, se [Adgangskode Management ofte stillede spørgsmål](active-directory-passwords-faq.md)til flere svar til adgangskode.

---

## <a name="application-access"></a>Adgang til programmer


**Sp: hvor kan jeg finde en liste over programmer, der allerede er integreret med Azure AD og deres egenskaber?**

**A:** Azure AD har over 2600 allerede integrerede programmer fra Microsoft, programmet tjenesteudbydere eller partnere. Alle foruddefinerede integreret programmer understøtter SSO. SSO gør det muligt at bruge din organisatoriske legitimationsoplysninger til at få adgang til dine apps. Nogle af programmerne understøtter også automatiseret klargøring og deaktivere klargøring

Du kan finde en komplet liste over de allerede integreret programmer, [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**Sp: Hvad nu, hvis jeg har brug for programmet er ikke på Azure AD-markedet?**

**A:** Du kan tilføje og konfigurere et program, du vil med Azure AD Premium. Du kan konfigurere SSO og automatiseret klargøring afhængigt af din programmets funktionalitet og dine præferencer.  

Du kan finde flere oplysninger i:

- [Konfiguration af single sign-on til programmer, der ikke er i galleriet Azure Active Directory-program](active-directory-saas-custom-apps.md)
- [Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer](active-directory-scim-provisioning.md) 


---

**Sp: hvordan brugere logger programmer ved hjælp af Azure Active Directory?**
 
**A:** Azure Active directory indeholder flere måder for brugerne at få vist og få adgang til deres programmer f.eks.:

- Panelet Azure AD adgang

- Starteren Office 365-program

- Direkte sign-on til medlem af organisationsnetværket apps

- Deep links til medlem af organisationsnetværket, baseret på adgangskoder, eller eksisterende apps

Yderligere oplysninger finder du se [installere Azure AD integreret programmer til brugere](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**Sp: Hvad er de forskellige måder Azure Active Directory giver mulighed for godkendelse og single sign-on til programmer?**
 
**A:** Azure Active Directory understøtter mange standardiseret protokoller for godkendelse og autorisation som SAML 2.0, OpenID forbinde, OAuth 2.0 og WS-sammenslutning. Azure AD understøtter også adgangskode større og automatiseret logon-funktioner til apps, der understøtter kun formularbaseret godkendelse.  

Du kan finde flere oplysninger i:

- [Godkendelse scenarier, hvor Azure AD](active-directory-authentication-scenarios.md)

- [Active Directory Authentication protokoller](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Hvordan single sign-on – med Azure Active Directory arbejde?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Sp: kan jeg tilføje programmer jeg kører i det lokale miljø?**

**A:** Azure AD-proxyen giver dig nem og sikker adgang til lokale webprogrammer, som du vælger. Du kan få adgang til disse programmer på samme måde, du får adgang til dine SaaS apps i Azure Active Directory. Der er ingen grund til en VPN-forbindelse eller ændre din netværksinfrastruktur.  

Se, [hvordan du giver sikker fjernadgang til lokale programmer](active-directory-application-proxy-get-started.md)kan finde flere oplysninger.


--- 

**Sp: hvordan kræver MFA for brugere at få adgang til et bestemt program?**

**A:** Bruger betinget Azure AD-adgang, kan du tildele et entydigt access-politik for hvert program. Du kan kræve MFA i din politik til enhver tid, eller når brugerne ikke har forbindelse til det lokale netværk.  

Få mere at vide under [sikring af adgang til Office 365 og andre apps, der er knyttet til Azure Active Directory](active-directory-conditional-access.md).


---

**Sp: Hvad er automatiserede klargøring af brugere til SaaS Apps?**

**A:** Azure Active Directory gør det muligt at automatisere oprettelse, vedligeholdelse og fjernelse af bruger-id'er i mange populære skyen (SaaS)-programmer. 

Få mere at vide under [automatisere brugerklargøring og Deprovisioning til SaaS programmer med Azure Active Directory](active-directory-saas-app-provisioning.md)

---



