<properties 
    pageTitle="Konfigurere Python med Azure App Service Webapps" 
    description="Dette selvstudium beskrives indstillingerne for redigering og konfiguration af en grundlæggende webserver gatewayen (WSGI) kompatibelt Python program på Azure App Service Web Apps." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurere Python med Azure App Service Webapps

Dette selvstudium beskrives indstillingerne for redigering og konfiguration af en grundlæggende Web Server Gateway Interface (WSGI) kompatibelt Python program på [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Den beskriver yderligere funktioner i ciffer installation, som virtuelt miljø og pakkeinstallation ved hjælp af requirements.txt.


## <a name="bottle-django-or-flask"></a>Flasker, Django eller anbringes?

Azure Marketplace indeholder skabeloner til flasker, Django og anbringes strukturer. Hvis du udvikler din første online i Azure App Service, eller du ikke er fortrolig med ciffer, anbefales det at følge en af disse selvstudier, som indeholder trinvise instruktioner til oprettelse af en arbejde ansøgning fra galleriet med ciffer installation fra Windows eller Mac:

- [Oprettelse af online med flasker](web-sites-python-create-deploy-bottle-app.md)
- [Oprettelse af online med Django](web-sites-python-create-deploy-django-app.md)
- [Oprettelse af online med anbringes](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Oprettelse af Web app på Azure-portalen

Dette selvstudium antages det, et eksisterende Azure abonnement og adgang til portalen Azure.

Hvis du ikke har en eksisterende WebApp, kan du oprette én fra [Azure-portalen](https://portal.azure.com).  Klik på knappen Nyt i øverste venstre hjørne, og klik derefter på **Web + Mobile** > **WebApp**.

## <a name="git-publishing"></a>Ciffer udgivelse

Konfigurere ciffer udgivelse for din nyoprettede online ved at følge vejledningen på [Lokale ciffer installation til Azure App-tjenesten](app-service-deploy-local-git.md). Dette selvstudium bruger ciffer til at oprette, administrere og udgive vores Python WebApp til Azure App-tjenesten.

Når ciffer publicering er konfigureret, oprettes et ciffer lager og der er knyttet til din online. URL-adressen på lagringsstedet vises og fremover kan bruges til at overføre data fra det lokale udviklingsmiljø til skyen. Sørg for, at en ciffer klient også er installeret for at udgive programmer via ciffer, og bruge instruktionerne til at overføre dine web app-indhold til Azure App Service.


## <a name="application-overview"></a>Oversigt over anvendelse

I næste afsnit oprettes følgende filer. De skal være placeret i roden af ciffer lager.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>WSGI Handler

WSGI er en Python standard beskrevet ved [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definerer en grænseflade mellem webserveren og Python. Den indeholder en standardiseret grænseflade til at skrive forskellige web-programmer og -rammer ved hjælp af Python. Populære Python web strukturer bruger i dag WSGI. Azure-App-tjenesten Web Apps giver du yder support til sådanne strukturer; Desuden kan erfarne brugere selv opretter sin egen så længe den brugerdefinerede handler følger WSGI specifikation retningslinjerne.

Her er et eksempel på en `app.py` , der definerer en brugerdefineret behandler:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Du kan køre dette program lokalt med `python app.py`, gå derefter til `http://localhost:5555` i din webbrowser.


## <a name="virtual-environment"></a>Virtuelt miljø

Selvom den ovenstående eksempel app ikke kræver en hvilken som helst eksterne pakker, er det sandsynligvis, at dit program kræver nogle.

For at administrere eksterne pakkeafhængigheder, understøtter Azure ciffer installation oprettelse af virtuelle miljøer.

Når Azure registrerer en requirements.txt i roden af lageret, oprettes der automatisk et virtuelt miljø med navnet `env`. Dette sker kun på den første installation, eller under en hvilken som helst installation efter den valgte Python runtime er ændret.

Du vil muligvis gerne have til at oprette et virtuelt miljø lokalt til udvikling, men medtage ikke dem i din ciffer lager.


## <a name="package-management"></a>Pakke administration

Pakker i requirements.txt installeres automatisk i det virtuelle miljø ved hjælp af pip. Dette sker på hver installation, men pip springe installationen, hvis en pakke allerede er installeret.

Eksempel på `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Python Version

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Eksempel på `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config

Du skal oprette en web.config-fil for at angive, hvordan serveren, der skal håndtere anmodninger.

Bemærk, at hvis du har en web.x.y.config fil i dit lager, hvor x.y svarer til den valgte Python runtime og derefter Azure kopieres automatisk den relevante fil som web.config.

Eksemplerne nedenfor web.config skal have et virtuelt miljø proxyscript, som er beskrevet i næste afsnit.  De sammen med den WSGI handler, der bruges i eksemplet `app.py` ovenfor.

Eksempel på `web.config` for Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Eksempel på `web.config` for Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Statiske filer skal håndteres af webserveren direkte, uden at gå gennem Python koden for forbedret ydeevne.

I ovenstående eksempler placeringen af de statiske filer på disken skal svare til placeringen i URL-adressen. Det betyder, at en anmodning om `http://pythonapp.azurewebsites.net/static/site.css` fungerer filen på disken ved `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`er, hvor du kan angive WSGI handler. I ovenstående eksempler, der er `app.wsgi_app` fordi handleren, der er en funktion med navnet `wsgi_app` i `app.py` i rodmappen.

`PYTHONPATH`kan tilpasses, men hvis du installerer alle dine afhængigheder i det virtuelle miljø ved at angive dem i requirements.txt, må du vil ændre den.


## <a name="virtual-environment-proxy"></a>Virtuelt miljø Proxy

Følgende script bruges til at hente WSGI handleren, skal du aktivere virtuelt miljø, og log fejlene. Det er udviklet til at være generisk og anvendte uden ændringer.

Indholdet af `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Tilpasse ciffer installation

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Fejlfinding - pakkeinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Fejlfinding – virtuelt miljø

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Python Developer Center](/develop/python/).

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)





 
