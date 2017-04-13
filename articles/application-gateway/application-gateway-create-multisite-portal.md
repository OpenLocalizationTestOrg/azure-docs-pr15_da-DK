<properties
   pageTitle="Konfigurere en eksisterende gateway program som vært for flere websteder i portalen Azure | Microsoft Azure"
   description="Denne side indeholder en vejledning til at konfigurere en eksisterende gateway Azure-program som vært for flere webprogrammer på den samme gateway ved hjælp af Azure portal."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Konfigurere en eksisterende gateway program som vært for flere webprogrammer

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-multisite-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Flere vært for webstedet, kan du installere mere end én webprogram på samme programmet gatewayen. Det er baseret på tilstedeværelsen af host sidehoved i den indgående HTTP-anmodning, til at bestemme, hvilke lytteren vil modtage trafik. Lytteren omdirigerer derefter trafik til den relevante back end-puljen, som er konfigureret i definitionen af regler for gateway. I SSL aktiveret webprogrammer programmet gateway, der er afhængig af filtypenavnet Server navn angivelse (SNI) til at vælge den korrekte lytteren for web-trafikken. En almindelig anvendelse af vært for flere websted er at indlæse balance anmodninger om forskellige webdomæner til anden back end-server grupper. På samme måde flere underdomæner for samme roddomænet, kan også bruges på samme programmet gatewayen.

## <a name="scenario"></a>Scenarie

I eksemplet nedenfor programmet gateway fungerer trafik til contoso.com og fabrikam.com med to grupper af back end-server: contoso server programgruppen og fabrikam server puljen. Konfiguration af lignende kan bruges til at host underdomæner som app.contoso.com og blog.contoso.com.

![flere lokationer scenarie][multisite]

## <a name="before-you-begin"></a>Inden du går i gang

Dette scenarie føjer understøttelse af flere websted til et eksisterende program-gateway. For at udføre dette scenario skal scenarie et eksisterende program-gateway være tilgængelige for at konfigurere. Gå til [Opret et program-gateway ved hjælp af portalen](./application-gateway-create-gateway-portal.md) for at lære at oprette en grundlæggende programmet gateway på portalen.

## <a name="requirements"></a>Krav

- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten tilhører det virtuelle netværksundernet eller skal være en offentlige IP-adresse/VIP. Fulde Domænenavn kan også bruges.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https disse værdier er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload). For flere websted aktiveret programmet gateways tilføjes værtsnavn og indikatorer for SNI også.
- **Regel:** Reglen binder lytteren, puljen back end-server og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter.
- **Certifikater:** Hver lytteren kræver et entydigt certifikat, i dette eksempel 2 lyttere er oprettet for flere websted. To .pfx certifikater og adgangskoder for at de skal oprettes.

## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Følgende er de trin, der er behov for at opdatere gatewayen program:

1. Oprette back end-grupper skal bruges til hvert websted.
2. Oprette en ny lytter for hver gruppe programmet gateway understøtter.
3. Oprette regler for at tilknytte hver lytteren med den relevante back-end.

## <a name="create-back-end-pools-for-each-site"></a>Oprette back end-grupper for hvert websted

En back end-puljen for hvert websted, der programmet gateway vil support skal bruges, i dette tilfælde 2 oprettes, én for contoso11.com og én til fabrikam11.com.

### <a name="step-1"></a>Trin 1

Gå til et eksisterende program-gateway i portalen Azure (https://portal.azure.com). Vælg **back end-grupper** , og klik på **Tilføj**

![tilføje back end-grupper][7]

### <a name="step-2"></a>Trin 2

Udfyld oplysningerne for den back end-puljen **pool1**, tilføje IP-adresser eller FQDN'er til back-end-servere, og klik på **OK**

![back end-puljen pool1 indstillinger][8]

### <a name="step-3"></a>Trin 3

Klik på **Tilføj** for at tilføje en ekstra back end-puljen **pool2**, tilføje IP-adresser eller FQDN'ER til back-end-servere på bladet back end-grupper, og klik på **OK**

![back end-puljen pool2 indstillinger][9]

### <a name="create-listeners-for-each-back-end"></a>Oprette lyttere til hver back end-

Application Gateway er afhængig af HTTP 1.1 host sidehoveder hoste mere end ét websted på den samme offentlige IP-adresse og port. Grundlæggende lytteren oprettet i portalen indeholder ikke denne egenskab.

### <a name="step-1"></a>Trin 1

Klik på **lyttere** på gatewayen eksisterende program og **med flere websted** for at tilføje den første lytteren.

![lyttere oversigt blade][1]

### <a name="step-2"></a>Trin 2

Udfyld oplysningerne for lytteren, i dette eksempel SSL opsigelse før er konfigureret, oprette en ny front end-port. Upload .pfx certifikat skal bruges til opsigelse før SSL. Den eneste forskel på denne blade sammenlignet med bladet standard grundlæggende lytteren er værtsnavnet.

![lytteren egenskaber blade][2]

### <a name="step-3"></a>Trin 3

Klik på **flere websted** , og Opret en anden lytteren, som beskrevet i det foregående trin for det andet websted. Sørg for at bruge et andet certifikat for anden lytteren. Den eneste forskel på denne blade sammenlignet med bladet standard grundlæggende lytteren er værtsnavnet. Udfyld oplysningerne for lytteren, og klik på **OK**.

![lytteren egenskaber blade][3]

> [AZURE.NOTE] Oprettelse af lyttere i portalen Azure programmet gatewayen er en længerevarende opgave, kan det tage lidt tid på at oprette de to lyttere i dette scenarie. Når fuldført lyttere Vis i portalen som set på følgende billede.

![Oversigt over lytteren][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Oprette regler for at tilknytte lyttere til back end-grupper

### <a name="step-1"></a>Trin 1

Gå til et eksisterende program-gateway i portalen Azure (https://portal.azure.com). Marker de **regler,** og Vælg eksisterende standard regel **rule1** , og klik på **Rediger**.

### <a name="step-2"></a>Trin 2

Udfyld bladet regler, som det fremgår i det følgende billede. Vælge den første lytteren og første puljen, og klikke på **Gem** , når du er færdig.

![redigere eksisterende regel][6]

### <a name="step-3"></a>Trin 3

Klik på **grundlæggende regel** for at oprette 2. reglen. Udfyld formularen med den anden lytteren og anden back end-puljen, og klik på **OK** for at gemme.

![tilføje grundlæggende regel blade][10]

Dette er fuldført konfiguration af et eksisterende program-gateway med support af flere websteder via Azure-portalen.

## <a name="next-steps"></a>Næste trin

Lær, hvordan du beskytter dine websteder med [Application Gateway - Firewall til webprogrammer](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png