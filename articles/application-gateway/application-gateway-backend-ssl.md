<properties
   pageTitle="Aktivere SSL politik og til slut SSL på Application Gateway | Microsoft Azure"
   description="Denne side indeholder en oversigt over gatewayen programmet start til slut SSL understøtter."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Aktivere SSL politik og til slut SSL på Application Gateway

## <a name="overview"></a>Oversigt

Programmet gateway understøtter SSL opsigelse før på din gateway, når hvilken trafik flyder typisk ikke-krypteret til back end-servere. Dette giver mulighed for webservere at være unburdened fra dyrt kryptering, der er relateret omkostninger. Men for nogle kunder ikke-krypteret kommunikation til back end-servere er ikke en acceptabel indstilling. Det kan være på grund af sikkerhed/overholde krav eller programmet kan kun acceptere en sikker forbindelse. For sådanne programmer understøtter programmet gateway nu start til slut SSL-kryptering.

Afslut til slutningen SSL giver dig mulighed for sikker overføre følsomme data til back end-krypteret stadig udnytte fordelene ved lag 7 justering af belastning funktioner gateway hvilket program understøtter, såsom cookie forbindelse, URL-baserede routing, til distribution baseret på websteder eller mulighed for at indsætte X-videresendte-* sidehoveder.

Når er konfigureret med start til slut SSL kommunikation tilstand, programmet gateway ophører bruger SSL-sessioner på din gateway og dekrypterer bruger trafik. Det gælder derefter konfigureret reglerne for at vælge en relevant back end-puljen forekomst for at dirigere trafik til den. Programmet gateway derefter starter en ny SSL-forbindelse til back end-serveren og igen krypterer data ved hjælp af back end-serverens certifikat offentlig nøgle før overførslen af anmodning på backend-version. Afslut for at afslutte SSL er aktiveret ved at angive protocol indstilling i BackendHTTPSetting til Https, som derefter anvendes på en back end-puljen. De enkelte back end-servere i back end-puljen med start til slut SSL aktiveret skal være konfigureret med et certifikat til at tillade sikker kommunikation.

![Start til slut ssl-scenario][1]

I dette eksempel distribueres anmodninger ved hjælp af TLS1.2 til back end-servere i Pool1 ved hjælp af start til slut SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Afslut for at afslutte SSL og whitelisting af certifikater

Programmet gateway kommunikerer kun med kendte back end-forekomster, der har whitelisted deres certifikat med application gateway. Hvis du vil aktivere whitelisting af certifikater, skal du overføre offentlig nøgle af back end-servercertifikater til gatewayen programmet (ikke rodcertifikat). Kun forbindelser til kendte og whitelisted en back-end der er tilladt. Resterende en back-end resultaterne i en gateway-fejl. Selvsignerede certifikater er til testformål kun og ikke anbefales til fremstilling arbejdsmængder. Certifikaterne skal også være whitelisted med application gateway som beskrevet i de forrige trin, før de kan bruges.

## <a name="application-gateway-ssl-policy"></a>Programmets Gateway SSL politik

Programmet gateway understøtter konfigurerbar SSL forhandling brugerpolitikker, så vores kunder mere kontrol over SSL-forbindelser på programmet gateway.

1. SSL 2.0 og 3.0 deaktiveret som standard for alle program-Gateways. De er ikke kan konfigureres overhovedet.
2. SSL politik definition giver du alternativ til at deaktivere en af følgende 3 protokoller - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Hvis ingen SSL-politik er defineret alle tre (TLSv1\_0, TLSv1\_1, TLSv1_2) er aktiveret.

## <a name="next-steps"></a>Næste trin

Når du lære om end for at slutningen SSL og SSL politikken, gå til at [aktivere start til slut SSL på programmet gateway](application-gateway-end-to-end-ssl-powershell.md) til at oprette et program-gateway med mulighed for at sende trafik til en back-end i krypteret form.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png