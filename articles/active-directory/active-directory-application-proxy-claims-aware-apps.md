<properties
    pageTitle="Arbejde med krav opmærksom på Apps i proxyen"
    description="Beskriver, hvordan du kommer i gang med Azure AD-proxyen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbejde med krav opmærksom på apps i proxyen

Krav opmærksom på apps udføre en omdirigering til sikkerhed Token STS (Service), som så anmoder om legitimationsoplysninger fra bruger erstatning for et token før der stilles brugeren til programmet. Hvis du vil aktivere proxyen til at arbejde med disse omdirigeringer, skal følgende trin til at blive ført.

## <a name="prerequisites"></a>Forudsætninger
Før du udfører denne procedure, Sørg for, at STS appen krav opmærksom på omdirigerer til er tilgængelige uden for dit lokale netværk.

## <a name="azure-classic-portal-configuration"></a>Azure konfiguration af klassisk portal

1. Publicere dit program henhold til de instruktioner, der er beskrevet i [Udgiv programmer med proxyen](active-directory-application-proxy-publish.md).
2. Vælg krav opmærksom på appen på listen over programmer, og klik på **Konfigurer**.
3. Hvis du vælger **Passthrough** som din **Preauthentication metode**, Sørg for at vælge **HTTPS** som dit skema for **Ekstern URL-adresse** .
4. Hvis du vælger **Azure Active Directory** , som din **Preauthentication metode**, kan du vælge **ingen** som din **Interne godkendelsesmetode**.


## <a name="adfs-configuration"></a>ADFS-konfiguration

1. Åbn ADFS administration.
2. Gå til **Stole part har tillid til**, skal du højreklikke på den app, du udgiver med proxyen, og vælg **Egenskaber**.  
  ![Afhængige part tillidsforhold Højreklik på app-navn - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. På fanen **slutpunkter** under **slutpunkt type**skal du vælge **WS-sammenslutning**.
4. Angiv den URL-adresse, du har angivet i den markerede Proxy under **Ekstern URL-adresse** under **Der er tillid til URL-adresse** , og klik på **OK**.  
  ![Tilføje et slutpunkt – Angiv værdi der er tillid til URL-adresse - skærmbillede](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Se også

- [Udgive programmer med proxyen](active-directory-application-proxy-publish.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)
- [Foretage fejlfinding af problemer, du har kørende med proxyen](active-directory-application-proxy-troubleshoot.md)
- [Aktivere oprindelig klient apps til at interagere med proxy-programmer](active-directory-application-proxy-native-client.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
