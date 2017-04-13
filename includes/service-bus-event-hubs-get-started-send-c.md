## <a name="send-messages-to-event-hubs"></a>Sende meddelelser til begivenhed hubber

I dette afsnit skriver vi en C-app for at sende begivenheder til begivenhed centrum. Vi bruger biblioteket Proton AMQP fra [Apache Qpid projekt](http://qpid.apache.org/). Dette er svarer til at bruge Service Bus køer og emner med AMQP fra C som vist [her](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Få mere at vide dokumentationen [Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Klik på linket **Installerer Qpid Proton** [Qpid AMQP Messenger side](http://qpid.apache.org/components/messenger/index.html), og følg vejledningen afhængigt af dit miljø. Vi antager et Linux-miljø for eksempel en [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) med Ubuntu 14.04.

2. Hvis du vil samle biblioteket Proton, ved at installere følgende pakker:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Hente biblioteket [Qpid Proton bibliotek](http://qpid.apache.org/proton/index.html) og udtrække, f.eks.:

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Oprette en build directory, kompilering og installation:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. Oprette en ny fil kaldet **sender.c** med følgende indhold i mappen arbejde. Husk at erstatte værdien for din begivenhed Hub navnet og navneområde (disse er som regel `{event hub name}-ns`). Du skal også erstatte en URL-kodet version af tasten **SendRule** oprettede tidligere. Du kan URL-kodning det [her](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. Samle filen, hvis du allerede har **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] I denne kode, skal bruge vi en udgående vinduet for 1 tvinge meddelelser ud af, så tidligt som muligt. Generelt skal dit program prøve at batchen meddelelser for at øge kapaciteten. Du kan finde flere oplysninger om, hvordan du bruger Qpid Proton biblioteket i dette og andre miljøer og fra platforme, der leveres bindinger [Qpid AMQP Messenger side](http://qpid.apache.org/components/messenger/index.html) (aktuelt Perl, PHP, Python og fonetisk).
