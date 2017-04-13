<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Ofte stillede spørgsmål – modsat DNS-Posterne for din Azure-tildelt IP-adresse

### <a name="how-much-do-reverse-dns-records-cost"></a>Hvor meget omvendt DNS-poster omkostninger?
De er gratis!  Der er gratis for modsat DNS-poster eller forespørgsler.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Vil modsat DNS-posterne for min Azure-tildelt offentlige IP-adresse løse fra internettet?
Ja. Når du indstiller egenskaben modsat DNS for dit offentlige IP-adresse, administrerer Azure alle DNS-delegeringer og DNS zones kræves for at sikre, at løser modsat DNS-post for alle brugere af internettet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Der oprettes en standard-modsat DNS-post til mit offentlige IP-adresser?
Nej. Modsat DNS er et tilføjelsesprogram funktion. Hvis du vælger ikke at konfigurere dem, oprettes der ingen standard modsat DNS-poster.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Hvad er formatet for det fulde domænenavn (fulde Domænenavn)?
FQDN'er er angivet i videresende rækkefølge og skal afsluttes med en prik (f.eks., "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Hvad sker der, hvis valideringskontroller til modsat DNS jeg har angivet mislykkes?
Hvor validering af modsat DNS kontrollerer mislykkes, handlingen service management mislykkes. Ret værdien modsat DNS efter behov, og prøv igen.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Kan jeg administrere modsat DNS for mine Azure-websted?
Modsat DNS understøttes ikke af Azure websteder. Modsat DNS understøttes for virtuelle Azure-computere.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Kan jeg konfigurere flere modsat DNS-poster til min offentlige IP-adresse?
Nej. Azure understøtter en enkelt modsat DNS-post for hver offentlige IP-adresse. Hver offentlige IP-adresse kan dog have sin egen modsat DNS-post.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Kan jeg konfigurere modsat DNS-poster til en IPv6 offentlige IP-adresse?
Nej.  På nuværende tidspunkt understøttes modsat DNS-poster for IPv4 offentlige IP-adresser kun.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Kan jeg konfigurere en modsat DNS-post til mit offentlige IP-adresse uden en angivet DomainNameLabel?
Nej. For at udnytte modsat DNS-poster til din offentlige IP-adresser, skal du angive egenskaben DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jeg sende mails til eksterne domæner fra Mine Azure beregne tjenester?
Nej. [Azure beregne services understøtter ikke sende mails til eksterne domæner](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
