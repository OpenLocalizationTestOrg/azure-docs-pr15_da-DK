<properties
   pageTitle="Red Hat opdatering infrastruktur (RHUI) | Microsoft Azure"
   description="Få mere at vide om rød Hat opdatering infrastruktur (RHUI) efter efter behov Red Hat Enterprise Linux forekomster i Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat opdatering infrastruktur (RHUI) til on demand Red Hat Enterprise Linux FOS i Azure

Virtuelle maskiner, der er oprettet ud fra de efter behov rød Hat Enterprise Linux (RHEL) tilgængelige billeder i Azure Marketplace er registreret til at få adgang til den røde Hat opdatering infrastruktur (RHUI) som implementeret i Azure.  Efter behov RHEL forekomster har adgang til en internationale yum lager og kunne modtage trinvise opdateringer.

Listen yum lager, som administreres af RHUI, er konfigureret i din RHEL forekomst under klargøring. Du ikke behøver at udføre yderligere konfiguration - køre `yum update` når din RHEL forekomst er klar til at få de seneste opdateringer.

> [AZURE.NOTE] Azure RHUI infrastruktur er opdateret for nylig (September 2016) og kræver ændringer i konfigurationen af din eksisterende RHEL forekomster til uafbrudt adgang til Azure RHUI. Se afsnittet RHUI Azure infrastruktur Update for detaljer.


## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure infrastruktur opdatering
Og September 2016 har Azure et nyt sæt rød Hat opdatering infrastruktur (RHUI)-servere. Disse servere er installeret med [Azure trafik Manager]( https://azure.microsoft.com/services/traffic-manager/) , så et enkelt slutpunkt (rhui-1.micrsoft.com) kan anvendes af enhver VM uanset område. De kan også bruge en SSL-certifikat, der er knyttet til en lang række velkendte nøglecenter (Baltimore rod). Gøre denne opdatering automatisk ville være farlig for nogle kunder, der har ACLs eller brugerdefinerede routing tabeller for RHUI Opdater serverne, så denne opdatering er "vælge-in." Manuelle trin til onboarding til disse nye servere er tilgængelige på denne side og et komplet script til onboarding på et automatiseret måde (af kontrol af de enkelte trin). Ny RHEL PAYG billederne i Azure Marketplace (versioner dateret September 2016 eller nyere) peger automatisk på de nye Azure RHUI servere og kræver ikke en yderligere handling.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>Ny Azure RHUI infrastruktur onboarding tidslinjen

| Dato | Bemærk! |
| --- | --- |
|September 22 2016 | RHUI servere og Installer kørselsvejledning tilgængelig til brug. FOS installeres ved hjælp af den nye (September 2016 dateret) RHEL PAYG marketplace billeder bruger automatisk de nye RHUI-servere, men eksisterende FOS er "vælge i"
|1 november 2016 | Ældre RHEL PAYG VM billeder (der bruger de gamle Azure RHUI servere) fjernes fra galleriet Azure Marketplace
|Januar 16 2017 | De gamle Azure RHUI servere vil ophøre. Opdatere alle dine berørte PAYG RHEL FOS ved denne gang for at bevare adgang til Azure RHUI

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>IP'er for de nye RHUI levering af indhold servere er

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Manuel opdatering fremgangsmåde for at bruge de nye Azure RHUI-servere

Hente offentlige nøgle signaturen (via krøllet)

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Bekræfte tasten hentede

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Kontrollere output, skal du kontrollere `keyid` og `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installere offentlig nøgle

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Hente, bekræfte og installere klient RPM

Download: For RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

For RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Kontrollere:

```
rpm -Kv azureclient.rpm
```

Markér i output signaturen på pakkens er OK

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installere ROTATIONSHASTIGHED

```
sudo rpm -U azureclient.rpm
```

Når er afsluttet, bekræfte, at du kan få adgang til Azure RHUI formular VM

### <a name="all-in-one-script-for-automating-the-above-task"></a>I én script til automatisering ovenfor opgaven
Brug følgende script efter behov til at automatisere opgaven for at opdatere berørte FOS til de nye Azure RHUI-servere.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Oversigt over RHUI
[Rød Hat opdatering infrastruktur](https://access.redhat.com/products/red-hat-update-infrastructure) tilbyder en meget SVG løsning for at administrere yum lager indhold efter Red Hat Enterprise Linux skyen forekomster, der er hostet af Red Hat-certificeret skyen udbydere. RHUI baseret på det foregående masse projekt, giver mulighed for skyen udbydere til lokalt spejling af Red Hat hostet lager indhold, oprette brugerdefinerede typer lagre med deres eget indhold og gøre disse typer lagre tilgængelig for en stor gruppe slutbrugere via et netværksbelastningen indhold levering system.

## <a name="regions-where-rhui-is-available"></a>Områder, hvor RHUI er tilgængelig
RHUI er tilgængelig i alle områder, hvor RHEL efter behov billeder er tilgængelig. Den indeholder aktuelt alle offentlige områder, der er angivet på siden [status for Azure dashboard](https://azure.microsoft.com/status/) og Azure os Government områder. RHUI adgang til FOS klargjort fra RHEL efter behov billeder er inkluderet i deres pris. Yderligere internationale/nationale skyen tilgængelighed opdateres, som vi udvide tilgængeligheden af RHEL efter behov i fremtiden.

> [AZURE.NOTE] Adgang til Azure-hostede RHUI er begrænset til FOS i [Microsoft Azure Datacenter IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Hente opdateringer fra en anden opdatering lager

Hvis du har brug at få opdateringer fra en anden opdatering lager (i stedet for Azure-hostede RHUI) skal du fjerne registreringen af din forekomster fra RHUI og registrere dem igen med den ønskede opdatering infrastruktur (såsom rød Hat satellit eller rød Hat kunde Portal CDN). Du skal rette Red Hat abonnementer for disse tjenester og registrering for [Rød Hat Skyadgang i Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Unregister RHUI og registrere til din opdatering infrastruktur opfølgning på under trin.

1.  Redigere /etc/yum.repos.d/rh-cloud.repo og ændre alle `enabled=1` til `enabled=0`. Eksempel:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Redigere /etc/yum/pluginconf.d/rhnplugin.conf og ændre `enabled=0` til `enabled=1`.
3.  Registrere derefter den ønskede infrastruktur, som rød Hat kunde Portal. Følge Red Hat løsning vejledning om, [hvordan du kan registrere og abonnere på et system til portalen rød Hat kunde](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Adgang til den Azure-hostede RHUI er inkluderet i RHEL Pay-As-You-Go (PAYG) billede prisen. Fjerne registreringen af en PAYG RHEL VM fra den Azure-hostet RHUI ikke konvertere den virtuelle maskine til Placer din-ejer-licenser (BYOL) type VM og dermed du kan skulle dobbelte betale Hvis du vil registrere den samme VM med en anden kilde opdateringer. 
> 
> Hvis du konsekvent skal bruge en opdatering infrastruktur andet end Azure-hostet RHUI du overveje at oprette og implementere dine egne (BYOL-type) billeder som beskrevet i [oprette og overføre Red Hat-baseret virtuel maskine til Azure](virtual-machines-linux-redhat-create-upload-vhd.md) artikel.

## <a name="next-steps"></a>Næste trin
Sådan oprettes en rød Hat Enterprise Linux VM fra Azure Marketplace Pay-As-You-Go billede og udnyttelse gå Azure-hostet RHUI til [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Du vil kunne bruge `yum update` i din RHEL forekomst uden eventuelle yderligere opsætning.