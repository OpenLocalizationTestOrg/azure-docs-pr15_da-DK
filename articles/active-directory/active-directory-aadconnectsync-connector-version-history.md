<properties
   pageTitle="Forbindelse versionshistorik Release | Microsoft Azure"
   description="I dette emne beskrives alle versioner af forbindelserne til Forefront identitet Manager (FIM) og Microsoft identitet Manager (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Forbindelse Release versionshistorik
Forbindelser til Forefront identitet Manager (FIM) og Microsoft identitet Manager (MIM) opdateres ofte.

>[AZURE.NOTE]
Dette emne er kun på FIM og MIM. Disse forbindelser understøttes ikke på Azure AD-forbindelse.

Dette emne viser alle versioner af forbindelser, der er blevet frigivet.

Relaterede links:

- [Hent seneste forbindelser](http://go.microsoft.com/fwlink/?LinkId=717495)
- [Generisk LDAP-forbindelse](active-directory-aadconnectsync-connector-genericldap.md) referencedokumentation
- [Generisk SQL Connector](active-directory-aadconnectsync-connector-genericsql.md) referencedokumentation
- [Web Services Connector](http://go.microsoft.com/fwlink/?LinkID=226245) referencedokumentation
- [PowerShell forbindelse](active-directory-aadconnectsync-connector-powershell.md) referencedokumentation
- [Lotus Domino Connector](active-directory-aadconnectsync-connector-domino.md) referencedokumentation

## <a name="111170"></a>1.1.117.0
Udgivet: 2016 marts

**Ny forbindelse**  
Første udgave af [Generisk SQL-forbindelse](active-directory-aadconnectsync-connector-genericsql.md).

**Nye funktioner:**

- Generisk LDAP-forbindelseskomponent:
    - Understøttelse af Deltaimport med Isode tilføjet.
- Web Services Connector:
    - Opdateret csEntryChangeResult aktivitet og setImportErrorCode aktivitet tillade objekt niveau fejl skal returneres tilbage til synkronisering af programmet.
    - Opdateret SAP6 og SAP6User skabeloner for at bruge den nye objekt fejl niveau funktionalitet.
- Lotus Domino-forbindelseskomponent:
    - Eksportér skal du én certifier per adressekartotek. Du kan nu bruge den samme adgangskode til alle certifiers for at gøre det nemmere for administration.

**Fast problemer:**

- Generisk LDAP-forbindelseskomponent:
    - Til IBM Tivoli DS blev nogle referenceattributter ikke registreret korrekt.
    - For åbne LDAP under Deltaimport af en blev mellemrum i begyndelsen og slutningen af strenge afkortet.
    - Novell og NetIQ ved omdøbt en eksport, som et objekt har flyttet mellem afdelinger/beholdere og på samme tid objektet mislykkedes.
- Web Services Connector:
    - Hvis webtjenesten havde flere endepunkter samme indbinding, derefter forbindelsen ikke korrekt opdage disse endepunkter.
- Lotus Domino-forbindelseskomponent:
    - Eksport af attributten fuldt navn til en mail i database fungerer ikke.
    - En eksport som både tilføjes eller fjernes medlem fra en gruppe eksporteres kun de tilføjede medlemmer.
    - Hvis et dokument i noter er ugyldig (attribut-isValid, der er angivet til FALSK), og klik derefter på forbindelse mislykkes.

## <a name="older-releases"></a>Ældre versioner
Forbindelserne er før marts 2016 udsendt som emner i Hjælp.

**Generisk LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, 2015 September
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marts 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, januar 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, September 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marts 2014

**Webtjenestenøgle**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, September 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, September 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, 2015 September
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marts 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, August 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, februar 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, oktober 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, 2013 August

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
