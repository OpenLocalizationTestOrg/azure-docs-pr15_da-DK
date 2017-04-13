<properties 
    pageTitle="Data Factory - navngive regler | Microsoft Azure" 
    description="Beskriver naming regler for Data Factory objekter." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - navngivning af regler 
Den følgende tabel indeholder naming regler til Data Factory elementer.



Navn | At entydige navn | Validering på trykkeri
:--- | :-------------- | :----------------
Data Factory | Entydige på tværs af Microsoft Azure. Navnene er case-insensitive, det vil sige, MyDF og mydf refererer til den samme data fabrik. |<ul><li>Hver data factory er knyttet til ét Azure abonnement.</li><li>Objektnavne, der skal starte med et bogstav eller et tal, og kan indeholde kun bogstaver, tal og bindestreg (-).</li><li>Hver bindestreg (-) skal være umiddelbart foran og efterfulgt af et bogstav eller et tal. Fortløbende stiplet er ikke tilladt i beholderen navne.</li><li>Navnet kan bestå af 3-63 tegn.</li></ul>
Sammenkædede tjenester/tabeller/rørledninger | Entydige med i en data fabrik. Navne er case-insensitive. | <ul><li>Maksimale antal tegn i et tabelnavn: 260.</li><li>Objektnavne, der skal starte med et bogstav tal eller en understregningstegnet (_).</li><li>Følgende tegn er ikke tilladt: ".", "+", "?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul>
Ressourcegruppe | Entydige på tværs af Microsoft Azure. Navne er case-insensitive. | <ul><li>Maksimale antal tegn: 1000.</li><li>Navnet kan indeholde bogstaver, tal og følgende tegn: "-", "_",","og".".</li></ul>