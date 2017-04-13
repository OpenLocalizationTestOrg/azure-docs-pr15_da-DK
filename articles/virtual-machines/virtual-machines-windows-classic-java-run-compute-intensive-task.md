<properties
    pageTitle="Beregn-intensivt Java-program på en VM | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter en Azure virtuelt, der kører et Beregn-intensivt Java-program, der kan overvåges af et andet Java-program."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Sådan køres en Beregn-intensivt opgave i Java på en virtuel maskine

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Med Azure, kan du bruge en virtuel maskine til at håndtere Beregn-intensivt opgaver. For eksempel kan en virtuel maskine håndtere opgaver og levere søgeresultater til klientcomputere eller mobilprogrammer. Når du har læst i denne artikel, har du en forståelse af, hvordan du opretter en virtuel maskine, der kører et Beregn-intensivt Java-program, der kan overvåges af et andet Java-program.

Dette selvstudium antages det, du ved, hvordan du opretter Java console programmer, kan importere biblioteker til Java-program og kan generere et Java arkiv (glas). Ingen kendskab til Microsoft Azure antages.

Du kan få mere at vide:

* Sådan oprettes en virtuel maskine med en Java Development Kit (JDK) allerede er installeret.
* Sådan fra en fjernplacering log på din virtuelle maskine.
* Sådan oprettes et service bus navneområde.
* Sådan oprettes en Java-program, som udfører en Beregn-intensivt opgave.
* Sådan oprettes et Java-program, der overvåger status for Beregn-intensivt opgaven.
* Sådan køres Java-programmer.
* Sådan forhindres Java-programmer.

Dette selvstudium bruger Traveling sælger problemet til opgaven Beregn-intensivt. Følgende er et eksempel på den Java-program, der kører Beregn-intensivt opgaven.

![Traveling sælger Problem Problemløser][solver_output]

Følgende er et eksempel af programmet Java overvågning Beregn-intensivt opgaven.

![Traveling sælger Problem klient][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Du opretter en virtuel maskine

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **Ny**, skal du klikke på **beregne**, skal du klikke på **virtuelt**og derefter klikke på **Fra galleriet**.
3. Vælg **JDK 7 Windows Server 2012**i dialogboksen **Vælg virtuelt billede** .
Bemærk, at **JDK 6 Windows Server 2012** er tilgængelig, hvis du har ældre programmer, som ikke endnu er klar til at køre i JDK 7.
4. Klik på **Næste**.
4. I dialogboksen **virtuelt konfiguration** :
    1. Angiv et navn til den virtuelle maskine.
    2. Angiv størrelsen, der skal bruges til den virtuelle maskine.
    3. Angiv et navn til administratoren i feltet **Brugernavn** . Husk, at dette navn og den adgangskode, du vil angive næste, du vil bruge dem, når du fra en fjernplacering logger ind på den virtuelle maskine.
    4. Angive en adgangskode i feltet **Ny adgangskode** , og skriv det igen i feltet **Bekræft** . Dette er adgangskoden til administratorkontoen.
    5. Klik på **Næste**.
5. Næste **virtuelt konfiguration** i dialogboksen:
    1. Brug standard **Opret en ny tjeneste i skyen**for **skybaseret tjeneste**.
    2. Værdien for **skyen tjenesten DNS-navn** skal være entydige i hele cloudapp.net. Hvis det er nødvendigt, kan du ændre denne værdi, så Azure angiver det er entydige.
    2. Angiv et område, forbindelse gruppe eller virtuelt netværk. For anvendelsen af dette selvstudium skal du angive et område som **Vest USA**.
    2. Vælg **Brug en automatisk oprettede lagerplads konto**for **Lagerplads konto**.
    3. For at **Angive tilgængelighed**, skal du vælge **(ingen)**.
    4. Klik på **Næste**.
5. I endeligt dialogboksen **virtuelt konfiguration** :
    1. Acceptér standard slutpunkt poster.
    2. Klik på **udført**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Til eksternt log på din virtuelle maskine

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).
2. Klik på **virtuelle maskiner**.
3. Klik på navnet på den virtuelle maskine, du vil logge på.
4. Klik på **Opret forbindelse**.
5. Besvare instruktionerne efter behov for at oprette forbindelse til den virtuelle maskine. Når du bliver bedt om det administratornavn og adgangskode, kan du bruge de værdier, du angav, da du oprettede den virtuelle maskine.

Bemærk, at funktionen Azure Service Bus kræver Baltimore CyberTrust Root certificate installeres som en del af din JRE **cacerts** store. Dette certifikat inkluderet automatisk i den Java Runtime miljø (JRE) bruges af dette selvstudium. Hvis du ikke har dette certifikat i din JRE **cacerts** store, kan du se [tilføje et certifikat til Java CA certifikatlager] [ add_ca_cert] for oplysninger om tilføjelse af det (samt oplysninger om visning af certifikater i din cacerts store).

## <a name="how-to-create-a-service-bus-namespace"></a>Sådan oprettes et service bus navneområde

Hvis du vil begynde at bruge Service Bus køer i Azure, skal du først oprette en tjeneste navneområde. Et service navneområde indeholder en angiver området beholder til at håndtere Service Bus ressourcer i dit program.

Sådan oprettes et service navneområde:

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com).
2.  Klik på **Service Bus, adgangskontrol og cachelagring**i nederste venstre navigationsrude i portalen Azure klassisk.
3.  Klik på noden **Service Bus** i øverste venstre rude i portalen Azure klassisk skal, og klik derefter på knappen **Ny** .  
    ![Tjenesten Bus Node skærmbillede][svc_bus_node]
4.  Angiv et **Namespace**, og klik derefter på knappen **Kontrollér tilgængelighed** for at sikre dig, at det er entydige, i dialogboksen **Opret en ny tjeneste Namespace** .  
    ![Oprette et nyt Namespace skærmbillede][create_namespace]
5.  Efter at sikre, at navneområdet navn er tilgængeligt, Vælg det land eller område, hvor din navneområde bør være vært for, og klik derefter på knappen **Opret Namespace** .  

    Det navneområde, du har oprettet derefter vises i portalen Azure klassisk og tager et øjeblik på at aktivere. Vent, indtil status er **aktiv** , før du fortsætter til næste trin.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Få standard Management legitimationsoplysninger for navneområdet

For at udføre handlinger i administration, som opretter en kø på det nye navneområde, skal du have management legitimationsoplysninger for navneområdet.

1.  Klik på noden **Service Bus** for at få vist listen over tilgængelige navneområder i den venstre navigationsrude.
    ![Tilgængelige navneområder skærmbillede][avail_namespaces]
2.  Vælg det navneområde, du lige har oprettet på listen, vises.
    ![Skærmbillede af Namespace liste][namespace_list]
3.  Ruden højre **Egenskaber** viser egenskaberne for det nye navneområde.
    ![Skærmbillede af ruden Egenskaber][properties_pane]
4.  **Standard nøgle** er skjult. Klik på knappen **Vis** for at få vist legitimationsoplysningerne.
    ![Skærmbillede af standard-tasten][default_key]
5.  Skal du notere **Standard udsteder** og **Standard nøgle** da du skal bruge oplysningerne nedenfor til at udføre handlinger med navneområdet.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Sådan oprettes en Java-program, som udfører en Beregn-intensivt opgave

1. På computeren udvikling (som ikke behøver at være den virtuelle maskine, du har oprettet) Hent [Azure SDK til Java](https://azure.microsoft.com/develop/java/).
2. Oprette et Java console-program ved hjælp af eksempelkoden i slutningen af dette afsnit. I dette selvstudium bruger vi **TSPSolver.java** som filnavn Java. Ændre den **din\_service\_bus\_navneområde**, **din\_service\_bus\_ejer**, og **din\_service\_bus\_nøgle** pladsholdere til at bruge din service bus **navneområde**, **Standard udsteder** og **Standard nøgle** -værdier, henholdsvis.
3. Efter kodningssprog, eksportere programmet til en runnable Java arkiv (glas) og pakke nødvendige DLL-filer i den oprettede glas. I dette selvstudium bruger vi **TSPSolver.jar** som genererede glas navnet.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Sådan oprettes et Java-program, der overvåger status for opgaven Beregn-intensivt

1. Oprette en Java console-program ved hjælp af eksempelkoden i slutningen af dette afsnit på computeren udvikling. I dette selvstudium bruger vi **TSPClient.java** som filnavn Java. Som vist tidligere, kan du ændre den **din\_service\_bus\_navneområde**, **din\_service\_bus\_ejer**, og **din\_service\_bus\_nøgle** pladsholdere til at bruge din service bus **navneområde**, **Standard udsteder** og **Standard nøgle** -værdier, henholdsvis.
2. Eksportere programmet til en runnable glas, og pakke nødvendige DLL-filer i den oprettede glas. I dette selvstudium bruger vi **TSPClient.jar** som genererede glas navnet.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Sådan køres Java-programmer
Kør programmet Beregn-intensivt først at oprette køen, derefter for at løse rejse Saleseman problemet, som føjer den aktuelle bedste rute til service bus køen. Mens programmet Beregn-intensivt er løbende (eller senere) køre klienten for at få vist resultater fra service bus køen.

### <a name="to-run-the-compute-intensive-application"></a>Køre programmet Beregn-intensivt

1. Log på din virtuelle maskine.
2. Oprette en mappe, hvor du vil køre programmet. For eksempel, **c:\TSP**.
3. Kopiere **TSPSolver.jar** til **c:\TSP**
4. Oprette en fil med navnet **c:\TSP\cities.txt** med følgende indholdet.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. Ændre mapper til c:\TSP ved en kommandoprompt.
6. Kontrollér, at den JRE placering mappe er miljøvariablen PATH.
7. Du skal oprette service bus kø før du kører TSP Problemløser permutationer. Kør følgende kommando for at oprette service bus køen.

        java -jar TSPSolver.jar createqueue

8. Nu hvor køen er oprettet, kan du køre TSP Problemløser permutationer. For eksempel køre følgende kommando for at køre Problemløser for 8 byer.

        java -jar TSPSolver.jar 8

 Hvis du ikke angiver et tal, kører det for 10 byer. Som Problemløser finder aktuelle kortest omdirigerer, føjer det dem til køen.

> [AZURE.NOTE]
> Jo større tal, du angiver den længere Problemløser kører. For eksempel køre til 14 byer kan tage flere minutter, og køre til 15 byer kan tage flere timer. Øge til 16 eller flere byer, kan det medføre dage af runtime (sidste uger, måneder og år). Dette er på grund af den hurtige forøgelse antallet af permutationer vurderet af Problemløser som antallet af byer stiger.

### <a name="how-to-run-the-monitoring-client-application"></a>Sådan køres overvågning klientprogrammet
1. Log på din computer, hvor du skal køre klientprogrammet. Dette behøver ikke at være den samme computer, der kører programmet **TSPSolver** , men det kan være.
2. Oprette en mappe, hvor du vil køre programmet. For eksempel, **c:\TSP**.
3. Kopiere **TSPClient.jar** til **c:\TSP**
4. Kontrollér, at den JRE placering mappe er miljøvariablen PATH.
5. Ændre mapper til c:\TSP ved en kommandoprompt.
6. Kør følgende kommando.

        java -jar TSPClient.jar

    Angiv eventuelt antallet minutter til dvale mellem kontrollere køen, ved at overførsel af argumentet kommandolinjen. Standard sengetider periode for at kontrollere køen er 3 minutter, der bruges, hvis ingen kommandolinjen argument er overført til **TSPClient**. Hvis du vil bruge en anden værdi for sengetider intervallet, for eksempel et minut køre følgende kommando.

        java -jar TSPClient.jar 1

    Klienten kører, indtil den ser meddelelsen kø "Afsluttet". Bemærk, at hvis du kører flere forekomster af Problemløser uden at køre klienten, du kan køre klienten flere gange til helt at tømme køen. Du kan også slette køen og derefter oprette den igen. Hvis du vil slette køen, skal du køre følgende kommando for **TSPSolver** (ikke **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    Problemløser kører, indtil den er færdig med at undersøge alle omdirigerer.

## <a name="how-to-stop-the-java-applications"></a>Hvordan du stopper Java-programmer
Til både Problemløser og klientprogrammer, kan du trykke på **Ctrl + C** for at afslutte, hvis du vil afslutte før tid.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
