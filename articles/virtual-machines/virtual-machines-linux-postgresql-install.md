<properties
    pageTitle="Konfigurere PostgreSQL på en Linux VM | Microsoft Azure"
    description="Lær, hvordan du installerer og konfigurerer PostgreSQL på en Linux virtuel maskine i Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Installere og konfigurere PostgreSQL på Azure

PostgreSQL er en avanceret open source--database, der svarer til Oracle og DB2. Den indeholder enterprise klar til funktioner som fulde SURT overholdelse, pålidelig transaktions behandling og med flere versioner på dokumentsammenfald kontrol. Det understøtter også standarder som ANSI SQL og SQL/KLOKKESL (herunder fremmed data indpakningsmaterialet til Oracle, MySQL, MongoDB og mange andre). Det er meget extensible med understøttelse af over 12 Beskrivelse af sprog, GIN og GiST indeks, geografiske dataunderstøttelse og flere funktioner, der synes godt om NoSQL for JSON eller nøgle-værdi-baserede programmer.

I denne artikel kan lære du, hvordan du installerer og konfigurerer PostgreSQL på en Azure virtuelt kører Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Installere PostgreSQL

> [AZURE.NOTE] Du skal allerede har en Azure virtuelt køre Linux for at kunne udføre dette selvstudium. For at oprette og konfigurere en Linux VM før du fortsætter, skal du se [Azure Linux VM selvstudium](virtual-machines-linux-quick-create-cli.md).

I dette tilfælde Brug port 1999 som PostgreSQL-port.  

Oprette forbindelse til Linux VM, du har oprettet via trykfarver. Hvis dette er første gang du bruger en Azure Linux VM, se, [hvordan du kan bruge SSH med Linux på Azure](virtual-machines-linux-mac-create-ssh-keys.md) at lære, hvordan du bruger trykfarver til at oprette forbindelse til en Linux VM.

1. Kør følgende kommando for at skifte til i roden (admin):

        # sudo su -

2. Nogle salgsdistributioner har afhængigheder, du skal installere før du installerer PostgreSQL. Kontrollere, om din distro på denne liste, og kør den relevante kommando:

    - Red Hat base Linux:

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian base Linux:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Hente PostgreSQL til rodmappen, og derefter udpakke pakken:

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    Ovenstående er et eksempel. Du kan finde mere detaljerede download adressen i [indekset for /pub/kilde /](https://ftp.postgresql.org/pub/source/).

4. Kør følgende kommandoer for at starte Opret skal:

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Hvis du vil oprette alt, som kan konfigureres, herunder dokumentation (HTML og mand sider) og yderligere moduler (contrib), kan du køre følgende kommando i stedet:

        # gmake install-world

    Du modtager følgende meddelelse:

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurere PostgreSQL

1. (Valgfrit) Oprette et symbolsk link for at forkorte PostgreSQL referencen for ikke at inkludere versionsnummeret:

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Opret en mappe til databasen:

        # mkdir -p /opt/pgsql_data

3. Oprette en ikke-rod-bruger, og rediger den brugerprofil. Derefter skal du skifte til den nye bruger (kaldet *postgres* i vores eksempel):

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Af sikkerhedsmæssige årsager bruger PostgreSQL en ikke-rod bruger til at initialiseret, starte eller lukke databasen.


4. Redigere filen *bash_profile* ved at angive kommandoerne nedenfor. Disse linjer, føjes til slutningen af filen *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Udføre *bash_profile* filen:

        $ source .bash_profile

6. Validere din installation ved hjælp af følgende kommando:

        $ which psql

    Hvis din installation er gået igennem, kan du se følgende svaret:

        /opt/pgsql/bin/psql

7. Du kan også se PostgreSQL-versionen:

        $ psql -V

8. Initialiseret databasen:

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Du skal modtage følgende output:

![Billede af](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurere PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Kør følgende kommandoer:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Ændre to variabler i filen /etc/init.d/postgresql. Præfikset er indstillet til PostgreSQL installationssti: **/opt/pgsql**. PGDATA er indstillet til data lagerplads stien til PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Billede af](./media/virtual-machines-linux-postgresql-install/no2.png)

Ændre filen for at gøre det eksekverbare:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Kontrollér, om slutpunktet på PostgreSQL vises på:

    # netstat -tunlp|grep 1999

Du burde se følgende output:

![Billede af](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Oprette forbindelse til databasens Postgres

Skifte til postgres brugeren igen:

    # su - postgres

Oprette en Postgres database:

    $ createdb events

Oprette forbindelse til databasens hændelser, du lige har oprettet:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Oprette og slette en Postgres tabel

Nu hvor du har forbindelse til databasen, kan du oprette tabeller i den.

For eksempel oprette en ny eksempel Postgres tabel ved hjælp af følgende kommando:

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Du har nu oprettet en tabel med fire kolonner med følgende kolonnenavne og begrænsninger:

1. Kolonnen "navn" er blevet begrænset af kommandoen VARCHAR skal være under 20 tegn.
2. Kolonnen "mad" angiver over fødevareelementer, hver person, der bringer. VARCHAR begrænser denne tekst for at være på under 30 tegn.
3. Kolonnen "bekræftet" poster om personen, der har RSVP'd til sammenkomst. De gyldige værdier er "J" og "N".
4. "Dato" kolonne vises, når de har tilmeldt sig begivenheden. Postgres kræver, at der skrives datoer som åååå-mm-dd.

Du burde se følgende, hvis tabellen er blevet oprettet:

![Billede af](./media/virtual-machines-linux-postgresql-install/no4.png)

Du kan også kontrollere tabelstrukturen ved hjælp af følgende kommando:

![Billede af](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Føje data til en tabel

Først skal indsætte oplysninger i en række:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Du burde se dette output:

![Billede af](./media/virtual-machines-linux-postgresql-install/no6.png)

Du kan tilføje nogle flere personer til tabellen samt. Her er nogle indstillinger, eller du kan oprette dine egne:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Få vist tabeller

Brug følgende kommando for at få vist en tabel:

    select * from potluck;

Output er:

![Billede af](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Slette data i en tabel

Brug følgende kommando til at slette data i en tabel:

    delete from potluck where name=’John’;

Dette sletter alle oplysningerne i rækken "John". Output er:

![Billede af](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Opdatere data i en tabel

Brug følgende kommando for at opdatere data i en tabel. Denne et har Sandy bekræftet, at der hun deltager, så vi vil ændre hendes RSVP fra "N" til "J":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Få mere at vide om PostgreSQL
Nu hvor du har fuldført installationen af PostgreSQL i en Azure Linux VM, kan du udnytte ved hjælp af det i Azure. Hvis du vil vide mere om PostgreSQL, gå til [PostgreSQL websted](http://www.postgresql.org/).
