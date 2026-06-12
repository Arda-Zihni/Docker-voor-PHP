# Inleverdocument Docker & PHP

## 1. GitHub Repository
**Link naar repository:** `[Vul hier je GitHub link in, bijvoorbeeld: https://github.com/jouw-gebruikersnaam/php-docker-app]`

---

## 2. Screenshots van localhost:8080

**Screenshot op Windows:**
`[Plak hier je screenshot van http://localhost:8080 op Windows]`

**Screenshot op macOS:**
`[Plak hier je screenshot van http://localhost:8080 op macOS]`

---

## 3. Antwoorden op Onderzoeksvragen

**Onderzoeksvraag 1:** Wat gebeurt er als je de regel `RUN docker-php-ext-install pdo pdo_mysql` weglaat uit de Dockerfile en de applicatie probeert te starten? Welke specifieke PHP-foutmelding genereert de browser dan?

**Antwoord:** 
Als je deze regel weglaat, installeert Docker de vereiste database-extensies niet in de PHP-container. Omdat de PDO MySQL driver dan ontbreekt, zal PHP geen verbinding kunnen maken met MySQL. De specifieke foutmelding die in de browser wordt weergegeven door de Exception is:
`PDOException: could not find driver`


**Onderzoeksvraag 2:** Waarom werkt `host=db` in het PHP-script wel, maar `host=localhost` niet? Naar welk specifiek onderdeel in je `docker-compose.yml` verwijst de naam `db`?

**Antwoord:** 
Binnen een Docker-container verwijst `localhost` altijd naar de container *zelf*, en dus niet naar de hostcomputer of een andere container (zoals de database-container). 
Docker Compose maakt automatisch een intern netwerk aan waar alle services in zitten. Door de hostnaam `db` te gebruiken, weet Docker Compose dat de web-service verbinding wil maken met de service genaamd `db`. Dit verwijst direct naar het blok `db:` (de MySQL database) onder `services:` in het `docker-compose.yml` bestand. Docker zorgt intern voor de vertaling van de naam `db` naar het juiste IP-adres van de database-container.


**Onderzoeksvraag 3:** Wijzig de tekst in de `<h1>` tag in je `src/index.php` bestand en ververs de browser. Zie je de wijziging direct zonder de container te herstarten? Welke regel in de `docker-compose.yml` maakt dit mogelijk en hoe werkt dit mechanisme?

**Antwoord:** 
Ja, de wijziging is direct zichtbaar nadat je de browser ververst, zonder de container te hoeven herstarten of herbouwen. 
Dit wordt mogelijk gemaakt door de zogenaamde *bind-mount* die is gedefinieerd in het `docker-compose.yml` bestand, specifiek door de regel:
`- ./src:/var/www/html` onder de sectie `volumes:` van de `web` service.
Dit mechanisme werkt als volgt: het koppelt de fysieke, lokale map `./src` op je hostcomputer rechtstreeks aan het pad `/var/www/html` binnenin de container. De container 'kijkt' als het ware naar dezelfde bestanden op je harde schijf. Als je het bestand lokaal opslaat, wijzigt het dus ook onmiddellijk in de map waar de Apache-webserver in de container naar kijkt.
