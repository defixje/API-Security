API4:2019 Gebrek aan resources en snelheidsbeperkingen
===========================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **2** | Prevalentie **3** : Detecteerbaarheid **3** | Technisch **2** : Bedrijfsspecifiek |
| Exploitatie vereist eenvoudige API-requests. Er is geen authenticatie vereist. Meerdere gelijktijdige verzoeken kunnen worden uitgevoerd vanaf een enkele lokale computer of door gebruik te maken van cloud computing-bronnen. | Het is gebruikelijk om API's te vinden die geen snelheidsbeperking implementeren of API's waar de limieten niet correct zijn ingesteld. | Exploitatie kan leiden tot DoS, waardoor de API niet meer reageert of zelfs niet beschikbaar is. |

## Is de API kwetsbaar?

API-requests verbruiken bronnen zoals netwerk, CPU, geheugen en opslag. De
hoeveelheid resources die nodig is om aan een aanvraag te voldoen, is sterk
afhankelijk van de gebruikersinvoer en de bedrijfslogica van het endpoint. Houd
ook rekening met het feit dat verzoeken van meerdere API-clients strijden om
resources. Een API is kwetsbaar als ten minste een van de volgende limieten
ontbreekt of onjuist is ingesteld (bijvoorbeeld te laag/hoog):

* Execution time-outs
* Maximaal toewijsbaar geheugen
* Aantal bestandsbeschrijvingen
* Aantal processen
* Request payload-grootte (bijv. uploads)
* Aantal verzoeken per klant/resource
* Aantal records per pagina dat moet worden geretourneerd in één request-response

## Voorbeeld aanvalsscenario's

### Scenario 1

Een aanvaller uploadt een grote afbeelding door een POST-request te sturen naar
`/api/v1/images`. Wanneer het uploaden is voltooid, maakt de API meerdere
miniaturen met verschillende formaten. Vanwege de grootte van de geüploade
afbeelding raakt het beschikbare geheugen uitgeput tijdens het maken van
miniaturen en reageert de API niet meer.

### Scenario #2

We hebben een applicatie die de gebruikerslijst bevat op een gebruikerspagina
met een limiet van `200` gebruikers per pagina. De gebruikerslijst wordt opgehaald
van de server met de volgende query: `/api/users?page=1&size=100`. Een aanvaller
verandert de parameter `size` in `200000`, waardoor prestatieproblemen op de
database ontstaan. Ondertussen reageert de API niet meer en kan deze geen verdere
verzoeken van deze of andere clients (ook wel DoS genoemd) afhandelen.

Hetzelfde scenario kan worden gebruikt om Integer Overflow- of Buffer Overflow-fouten
te veroorzaken.

## Hoe te voorkomen

* Docker maakt het gemakkelijk om [geheugen][1], [CPU][2], [aantal herstarts][3],
  [bestandsbeschrijvingen en processen][4] te beperken.
* Implementeer een limiet voor hoe vaak een klant de API kan aanroepen binnen een
  gedefinieerd tijdsbestek.
* Breng de klant op de hoogte wanneer de limiet wordt overschreden door het
  limiets aantal en het tijdstip waarop de limiet wordt gereset op te geven.
* Voeg de juiste server-side validatie toe voor query string en request body
  parameters, met name degene die het aantal records bepaalt dat in het response
  moet worden geretourneerd.
* Definieer en handhaaf de maximale gegevensgrootte op alle inkomende parameters
  en payloads, zoals maximale lengte voor strings en een maximaal aantal
  elementen in arrays.

## Referenties

### OWASP

* [Blocking Brute Force Attacks][5]
* [Docker Cheat Sheet - Limit resources (memory, CPU, file descriptors,
  processes, restarts)][6]
* [REST Assessment Cheat Sheet][7]

### Extern

* [CWE-307: Improper Restriction of Excessive Authentication Attempts][8]
* [CWE-770: Allocation of Resources Without Limits or Throttling][9]
* “_Rate Limiting (Throttling)_” - [Security Strategies for Microservices-based
  Application Systems][10], NIST

[1]: https://docs.docker.com/config/containers/resource_constraints/#memory
[2]: https://docs.docker.com/config/containers/resource_constraints/#cpu
[3]: https://docs.docker.com/engine/reference/commandline/run/#restart-policies---restart
[4]: https://docs.docker.com/engine/reference/commandline/run/#set-ulimits-in-container---ulimit
[5]: https://www.owasp.org/index.php/Blocking_Brute_Force_Attacks
[6]: https://github.com/OWASP/CheatSheetSeries/blob/3a8134d792528a775142471b1cb14433b4fda3fb/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-7---limit-resources-memory-cpu-file-descriptors-processes-restarts
[7]: https://github.com/OWASP/CheatSheetSeries/blob/3a8134d792528a775142471b1cb14433b4fda3fb/cheatsheets/REST_Assessment_Cheat_Sheet.md
[8]: https://cwe.mitre.org/data/definitions/307.html
[9]: https://cwe.mitre.org/data/definitions/770.html
[10]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204-draft.pdf
