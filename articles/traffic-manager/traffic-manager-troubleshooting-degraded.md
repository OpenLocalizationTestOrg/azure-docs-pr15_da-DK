<properties
    pageTitle="Fejlfinding i forbindelse med nedsat status på Azure trafik Manager"
    description="Sådan foretages fejlfinding af trafik Manager profiler, når der vises som nedsat status."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Fejlfinding i forbindelse med nedsat tilstand på Azure trafik Manager

I denne artikel beskrives, hvordan du udfører fejlfinding af en Azure trafik Manager-profil, der vises en nedgraderede status. Dette scenario, kan du overveje at du har konfigureret en trafik Manager profil, der peger på nogle af dine cloudapp.net hostet tjenester. Når du markerer tilstanden for chefen trafik, kan du se, at Status er nedsat.

![forringet tilstand](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Hvis du skifter til fanen slutpunkter for profilen, vises en eller flere af slutpunkterne med en offlinestatus:

![offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Om trafik Manager sonder

- Trafik Manager finder et slutpunkt skal være ONLINE kun, når efterprøvning af af modtager en HTTP 200 svar tilbage fra efterprøvning af af sti. Alle andre ikke-200 svaret, som er en fejl.
- En 30 x omdirigering mislykkes, selvom omdirigeret URL-adressen returnerer en 200.
- Til HTTPs sonder ignoreres certifikatfejl.
- Indholdet af efterprøvning af af stien er ligegyldig, så længe en 200 returneres. Teste en URL-adresse til nogle statisk indhold som "/ favicon.ico" er et almindelige metode. Dynamisk indhold, som ASP-siderne, returnerer muligvis ikke 200, altid, selvom programmet er i orden.
- Den bedste fremgangsmåde er at indstille efterprøvning af af stien til noget, der har tilstrækkelig logik at finde ud af, at webstedet er op eller ned. I det forrige eksempel, ved at angive stien til "/ favicon.ico", du er kun test, der w3wp.exe svarer. Denne efterprøvning af af kan ikke angive, at dit program er i orden. Et bedre alternativ er at oprette en sti til en noget såsom "/ Probe.aspx", der har logik til at bestemme tilstanden for webstedet. Du kan for eksempel bruge ydeevne tællere til CPU-forbrug eller måle antallet af mislykkede anmodninger. Eller du kan forsøge at få adgang til databaseressourcer eller sessionstilstand for at sikre dig, at webprogrammet fungerer.
- Hvis alle slutpunkter i en profil er nedsat, derefter behandler trafik Manager alle slutpunkter som sund og omdirigerer trafik til alle slutpunkter. Denne funktionalitet sikrer, at problemer med test ordning ikke medfører en komplet afbrydelse af din tjeneste.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du vil foretage fejlfinding af en efterprøvning af af fejl, skal du et værktøj, der viser HTTP-statuskode returnerede fra efterprøvning af af URL-adressen. Der findes mange tilgængelige funktioner, der viser rå HTTP-svaret.

* [Fiddler](http://www.telerik.com/fiddler)
* [Krøllet](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Du kan også bruge fanen netværk i F12-fejlfindingsværktøjer i Internet Explorer til at få vist HTTP-svarene.

I dette eksempel, vi vil have vist svaret fra vores efterprøvning af af URL-adresse: http://watestsdp2008r2.cloudapp.net:80/efterprøvning af af. I følgende eksempel PowerShell illustreres problemet.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Eksempel på output:

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Bemærk, at vi har modtaget et svar til omdirigering. Som tidligere nævnt, en hvilken som helst statuskode andet end betragtes 200 som en fejl. Trafik Manager ændres slutpunkt status til Offline. Du kan løse problemet ved at kontrollere websted konfigurationen for at sikre, at det stort statuskode kan returneres fra sti, efterprøvning af af. Omkonfigurere efterprøvning af af den trafik Manager til at pege på en sti, der returnerer en 200.

Hvis din efterprøvning af af bruger HTTPS-protokollen, kan du vil deaktivere certifikat kontrol for at undgå SSL/TLS fejl under din test. Følgende PowerShell-sætninger deaktiverer certifikat validering for den aktuelle session PowerShell:

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Næste trin

[Om trafik Manager trafik routing metoder](traffic-manager-routing-methods.md)

[Hvad er trafik Manager](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Webapps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Handlinger på trafik Manager (REST-API Reference)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure-cmdletter trafik Manager][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
