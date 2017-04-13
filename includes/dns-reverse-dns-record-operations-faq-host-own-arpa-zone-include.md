<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Ofte stillede spørgsmål – vært for din ARPA zone i Azure DNS

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan jeg hoste ARPA zoner for min Internetudbyder tildelt IP-blokke på Azure DNS?
Ja. Vært zonerne ARPA til din egen IP-intervaller i Azure DNS understøttes fuldt ud.

Du skal blot [oprette zonen i Azure DNS](dns-getstarted-create-dnszone.md)og derefter arbejde med din Internetudbyder skal [uddelegeres zonen](dns-domain-delegation.md).  Derefter kan du administrere PTR-poster for hver omvendt opslag på samme måde som andre posttyper.

Du kan også [importere en eksisterende omvendt opslagszone ved hjælp af Azure CLI](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Hvor meget koster vært for mit ARPA zone omkostninger?
Vært zonen ARPA til din Internetudbyder tildelt IP-adresse som blokken i Azure DNS registreres til [standard Azure DNS satser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan jeg hoste ARPA zoner for både IPv4 og IPv6-adresser i Azure DNS?
Ja.