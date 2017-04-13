<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Ofte stillede spørgsmål – modsat DNS-Posterne for din Azure-tildelt IP-adresse

### <a name="how-much-do-reverse-dns-records-cost"></a>Hvor meget omvendt DNS-poster omkostninger?
De er gratis!  Der er gratis for modsat DNS-poster eller forespørgsler.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Vil mine modsat DNS-poster løse fra internettet?
Ja. Når du indstiller egenskaben modsat DNS for dit skybaseret tjeneste, administrerer Azure alle DNS-delegeringer og DNS zones kræves for at sikre, at løser modsat DNS-post for alle brugere af internettet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Der oprettes en standard-modsat DNS-post til min Skytjenester?
Nej. Modsat DNS er et tilføjelsesprogram funktion. Hvis du vælger ikke at konfigurere dem, oprettes der ingen standard modsat DNS-poster.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Hvad er formatet for det fulde domænenavn (fulde Domænenavn)?
FQDN'er er angivet i videresende rækkefølge og skal afsluttes med en prik (f.eks., "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Hvad sker der, hvis valideringskontroller til modsat DNS jeg har angivet mislykkes?
Hvor validering af modsat DNS kontrollerer mislykkes, handlingen service management mislykkes. Ret værdien modsat DNS efter behov, og prøv igen.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Kan jeg administrere modsat DNS for mine Azure-websted?
Modsat DNS understøttes ikke af Azure websteder. Modsat DNS understøttes til Azure PaaS roller og IaaS virtuelle computere.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>Kan jeg konfigurere flere modsat DNS-poster til min skytjeneste?
Nej. Azure understøtter en enkelt modsat DNS-post for hver Azure skybaseret tjeneste. Hver Azure skybaseret tjeneste kan dog have sin egen modsat DNS-post.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jeg sende mails til eksterne domæner fra Mine Azure beregne tjenester?
Nej. [Azure beregne services understøtter ikke sende mails til eksterne domæner](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
