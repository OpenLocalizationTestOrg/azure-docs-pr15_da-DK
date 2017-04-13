<properties
    pageTitle="Arbejde med brugerdefinerede domæner i Azure AD-proxyen | Microsoft Azure"
    description="Sådan fungerer omslag med brugerdefinerede domæner i Azure AD-proxyen."
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

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Arbejde med brugerdefinerede domæner i Azure AD-proxyen

Ved hjælp af en standarddomænet gør det muligt at angive de samme URL-adresse som interne og eksterne URL-adressen for at få adgang til programmet, så brugerne kun har én URL-adresse skal huske at få adgang til appen, uanset hvor de får adgang til fra. Dette kan du også oprette en genvej til et enkelt i panelet adgang til programmet. Hvis du bruger standarddomænet fra Azure AD-proxyen, er der ingen yderligere konfiguration, skal du aktivere dit domæne. I tilfælde af, at du bruger et brugerdefineret domæne, er der et par ting, du skal gøre for at sikre, at proxyen genkender dit domæne og dens certifikater har valideret.

## <a name="selecting-your-custom-domain"></a>Valg af dit eget domæne

1. Publicere dit program ifølge instruktionerne i [Udgiv programmer med proxyen](active-directory-application-proxy-publish.md).
2. Når programmet vises på listen over programmer, skal du markere det og klikke på **Konfigurer**.
3. Angiv dit eget domæne under **Ekstern URL-adresse**.
4. Hvis din eksterne URL-adresse er https, bliver du bedt om at overføre et certifikat, så Azure kan validere URL-adressen af programmet. Du kan også overføre et certifikat med jokertegn, der svarer til den eksterne Webadresse til programmet. Dette domæne skal være i listen over dine [Azure bekræftede domæner](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure skal have et certifikat til domæne URL-adressen på programmet eller et certifikat med jokertegn, der svarer til den eksterne URL-adresse til programmet.
5. Sørg for at tilføje en DNS-post, som omdirigerer interne URL-adressen til det program, der gør det muligt at have samme URL-adressen til intern og ekstern adgang og en enkelt genvej til programmet i brugerens liste over programmer.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Ofte stillede spørgsmål om at arbejde med brugerdefinerede domæner

Sp: kan jeg vælge et certifikat, der allerede har overført uden at overføre den igen?  
SV: tidligere overførte certifikater automatisk er bundet til et program, og der er ét certifikater, der svarer programmets værtsnavn.  

Sp: Hvordan kan jeg føje et certifikat og hvilket format skal det eksporterede certifikat overføres i?  
SV: certifikatet, der skal overføres fra konfigurationssiden af programmet. Certifikatet, der skal være en PFX-fil.  

Sp: kan bruges ECC certifikater  
SV: der er ingen eksplicitte begrænsninger på signatur metoder.  

Sp: kan bruges SAN certifikater  
SV: Ja.  

Sp: kan bruges jokertegn certifikater  
SV: Ja.  

Sp: kan bruges et andet certifikat på hvert program?  
SV: Ja, medmindre de to programmer dele den samme eksterne vært.  

Sp: Hvis jeg har registreret et nyt domæne, kan jeg bruge dette domæne?  
SV: Ja, på listen over domæner indføres fra lejerens bekræftet domænelisten.  

Sp: Hvad sker der, når en udløber?  
SV: du får en advarsel i sektionen certifikat i konfigurationssiden af programmet. Når en bruger forsøger at få adgang til programmet, der en sikkerhedsadvarsel pop.  

Sp: Hvad skal jeg gøre, hvis jeg gerne vil erstatte et certifikat for en given app?  
SV: Overfør et nyt certifikat fra konfigurationssiden af programmet.  

Sp: kan jeg slette en cert og erstatte den?  
SV: Når du har overført et nyt certifikat, hvis det gamle certifikat ikke er i brug af et andet program, det, slettes automatisk.  

Sp: Hvad sker der, når en cert tilbagekaldes?  
SV: tilbagekaldelse kontrol udføres ikke for certifikater. Når en bruger forsøger at få adgang til programmet, afhængigt af browseren, kan en sikkerhedsadvarsel vises.  

Sp: kan jeg bruge et selvsigneret certifikat?  
SV: Ja, selvsignerede certifikater er tilladt. Bemærk, at hvis du bruger en privat nøglecenter, CDP (certifikat tilbagekaldte certifikater punkt fordeling punkt) for certifikatet, der skal være offentlig.  

Sp: er der et sted at få vist alle certifikater til min lejer?  
SV: Dette understøttes ikke i den aktuelle version.  


## <a name="see-also"></a>Se også

- [Udgive programmer med proxyen](active-directory-application-proxy-publish.md)
- [Aktivere enkeltlogon](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)
- [Føje dit eget domænenavn til Azure AD](active-directory-add-domain.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
