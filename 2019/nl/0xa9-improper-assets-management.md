API9:2019 Onjuiste activa beheer
====================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **3** : Detecteerbaarheid **2** | Technisch **2** : Bedrijfsspecifiek |
| Oude API-versies zijn meestal niet gepatcht en zijn een gemakkelijke manier om systemen te compromitteren zonder de modernste beveiligingsmechanismen te hoeven bestrijden, die mogelijk aanwezig zijn om de meest recente API-versies te beschermen. | Verouderde documentatie maakt het moeilijker om kwetsbaarheden te vinden en/of op te lossen. Gebrek aan inventarisatie van activa en strategieën voor buitengebruikstelling leidt tot het uitvoeren van niet-gepatchte systemen, wat resulteert in het lekken van gevoelige gegevens. Het is gebruikelijk om onnodig blootgestelde API-hosts te vinden vanwege moderne concepten zoals microservices, die applicaties eenvoudig te implementeren en onafhankelijk maken (bijvoorbeeld cloud computing, k8s). | Aanvallers kunnen toegang krijgen tot gevoelige gegevens of zelfs de server overnemen via oude, niet-gepatchte API-versies die op dezelfde database zijn aangesloten. |

## Is de API kwetsbaar?

De API kan kwetsbaar zijn als:

* Het doel van een API-host onduidelijk is en er geen expliciete antwoorden zijn
  op:
  de volgende vragen:
    * In welke omgeving draait de API (bijv. productie, staging, test,
      ontwikkeling)?
    * Wie moet netwerktoegang hebben tot de API (bijv. openbaar, intern,
      partners)?
    * Welke API-versie is actief?
    * Welke gegevens worden verzameld en verwerkt door de API (bijv. PII)?
    * Wat is de gegevensstroom?
* Er is geen documentatie of de bestaande documentatie is niet bijgewerkt.
* Er is geen pensioenplan voor elke API-versie.
* Hosts-inventaris ontbreekt of is verouderd.
* Geïntegreerde inventaris van services, van eerste of derde partij, ontbreekt
  of is verouderd.
* Oude of eerdere API-versies worden ongepatcht uitgevoerd.

## Voorbeeld aanvalsscenario's

### Scenario 1

Na het herontwerpen van hun applicaties, liet een lokale zoekservice een oude
API-versie (`api.someservice.com/v1`) draaien, onbeschermd en met toegang tot de
gebruikersdatabase. Terwijl hij zich op een van de laatst uitgebrachte
applicaties richtte, vond een aanvaller het endpoint (`api.someservice.com/v2`).
Door `v2` te vervangen door `v1` in de URL kreeg de aanvaller toegang tot de oude,
onbeschermde API, waardoor de persoonlijk identificeerbare informatie (PII) van
meer dan 100 miljoen gebruikers werd blootgelegd.

### Scenario #2

Een sociaal netwerk implementeerde een snelheidsbeperkend mechanisme dat
aanvallers blokkeert brute-force te gebruiken om wachtwoorden te raden. Dit
mechanisme is niet geïmplementeerd als onderdeel van de API-code zelf, maar in
een aparte component tussen de client en de officiële API
(`www.socialnetwork.com`). Een onderzoeker vond een bèta-API-host
(`www.mbasic.beta.socialnetwork.com`) die dezelfde API bediend, inclusief het
reset-wachtwoordmechanisme, maar het snelheidsbeperkende mechanisme was hier niet
aanwezig. De onderzoeker was in staat om het wachtwoord van elke gebruiker
opnieuw in te stellen door een simpele brute-force te gebruiken om het
6-cijferige token te raden.

## Hoe te voorkomen

* Inventariseer alle API-hosts en documenteer belangrijke aspecten van elk van
  hen, met de nadruk op de API-omgeving (bijv. productie, staging, test,
  ontwikkeling), die netwerktoegang tot de host moet hebben (bijv. openbaar,
  intern, partners) en de API-versie.
* Inventariseren van geïntegreerde diensten en documenteren van belangrijke
  aspecten zoals hun rol in het systeem, welke gegevens worden uitgewisseld
  (datastroom) en de gevoeligheid ervan.
* Documenteer alle aspecten van uw API, zoals authenticatie, fouten, omleidingen,
  snelheidsbeperking, CORS-beleid (cross-origin resource sharing) en endpoints,
  inclusief hun parameters, verzoeken en antwoorden.
* Genereer automatisch documentatie door gebruik te maken van open standaarden.
  Neem de documentatie-build op in uw CI/CD-pijplijn.
* API-documentatie beschikbaar stellen aan degenen die geautoriseerd zijn om de
  API te gebruiken.
* Gebruik externe beschermingsmaatregelen zoals API-beveiligingsfirewalls voor
  alle blootgestelde versies van uw API's, niet alleen voor de huidige
  productieversie.
* Vermijd het gebruik van productiegegevens bij niet-productie-API-implementaties.
  Als dit onvermijdelijk is, moeten deze endpoints dezelfde
  beveiligingsbehandeling krijgen als de productie-endpoints.
* Wanneer nieuwere versies van API's beveiligingsverbeteringen bevatten, voer dan
  een risicoanalyse uit om de beslissing te nemen over de beperkende acties die
  nodig zijn voor de oudere versie: bijvoorbeeld of het mogelijk is om de
  verbeteringen te backporteren zonder de API-compatibiliteit te verbreken of dat
  u de oudere versie snel uitfaseert en alle clients dwingt om naar de nieuwste
  versie te gaan.

## Referenties

### Extern

* [CWE-1059: Incomplete Documentation][1]
* [OpenAPI Initiative][2]

[1]: https://cwe.mitre.org/data/definitions/1059.html
[2]: https://www.openapis.org/
