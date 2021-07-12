API1:2019 Autorisatie op gebroken objectniveau
===========================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **3** : Detecteerbaarheid **2** | Technisch **3** : Bedrijfsspecifiek |
| Aanvallers kunnen misbruik maken van API-endpoints die kwetsbaar zijn voor verbroken autorisatie op objectniveau door de ID te manipuleren van een object dat binnen het verzoek wordt verzonden. Dit kan leiden tot ongeautoriseerde toegang tot gevoelige gegevens. Dit probleem komt heel vaak voor in op API gebaseerde applicaties, omdat de servercomponent de status van de client meestal niet volledig volgt, en in plaats daarvan meer afhankelijk is van parameters zoals object-ID's, die door de client worden verzonden om te beslissen tot welke objecten toegang moet worden verkregen. | Dit is de meest voorkomende en impactvolle aanval op API's. Autorisatie- en toegangscontrolemechanismen in moderne applicaties zijn complex en wijdverbreid. Zelfs als de toepassing een goede infrastructuur voor autorisatiecontroles implementeert, kunnen ontwikkelaars vergeten deze controles te gebruiken voordat ze toegang krijgen tot een gevoelig object. Toegangscontroledetectie is doorgaans niet vatbaar voor geautomatiseerde statische of dynamische tests. | Ongeautoriseerde toegang kan leiden tot openbaarmaking van gegevens aan onbevoegde partijen, gegevensverlies of gegevensmanipulatie. Ongeautoriseerde toegang tot objecten kan ook leiden tot een volledige accountovername. |

## Is de API kwetsbaar?

Autorisatie op objectniveau is een toegangscontrolemechanisme dat meestal op
codeniveau wordt geïmplementeerd om te valideren dat één gebruiker alleen toegang
heeft tot objecten waartoe hij toegang zou moeten hebben.

Elk API-endpoint dat een ID van een object ontvangt en elk type actie op het
object uitvoert, moet autorisatiecontroles op objectniveau implementeren. De
controles moeten valideren dat de ingelogde gebruiker wel toegang heeft om de
gevraagde actie op het aangevraagde object uit te voeren.

Storingen in dit mechanisme leiden doorgaans tot ongeoorloofde openbaarmaking,
wijziging of vernietiging van alle gegevens.

## Voorbeeld aanvalsscenario's

### Scenario 1

Een e-commerceplatform voor online winkels (winkels) biedt een lijstpagina met de
omzetgrafieken voor hun gehoste winkels. Door de browserverzoeken te inspecteren,
kan een aanvaller de API-endpoints identificeren die worden gebruikt als
gegevensbron voor die grafieken en hun patronen
`/shops/{shopName}/revenue_data.json`. Door de andere API endpoint te gebruiken
kan de aanvaller de lijst met alle gehoste winkelnamen krijgen. Met een eenvoudig
script om de namen in de lijst te manipuleren en `{shopName}` in de URL te
vervangen, krijgt de aanvaller toegang tot de verkoopgegevens van duizenden
e-commerce winkels.

### Scenario #2

Terwijl het netwerkverkeer van een draagbaar apparaat wordt gecontroleerd, trekt
het volgende HTTP `PATCH` request de aandacht van een aanvaller vanwege de
aanwezigheid van een aangepaste HTTP request header `X-User-Id: 54796`. Door de
waarde 'X-User-Id' te vervangen door '54795', ontvangt de aanvaller een succesvol
HTTP response en kan hij de accountgegevens van andere gebruikers wijzigen.

## Hoe te voorkomen

* Implementeer een goed autorisatiemechanisme dat afhankelijk is van het
  gebruikersbeleid en de hiërarchie.
* Gebruik een autorisatiemechanisme om te controleren of de ingelogde gebruiker
  toegang heeft om de gevraagde actie op het record uit te voeren in elke functie
  die een invoer van de client gebruikt om toegang te krijgen tot een record in
  de database.
* Gebruik liever willekeurige en onvoorspelbare waarden als GUID's voor de ID's
  van records.
* Schrijven van tests om het autorisatiemechanisme te evalueren. Implementeer
  geen kwetsbare wijzigingen die de tests doorbreken.

## Referenties

### Extern

* [CWE-284: Improper Access Control][1]
* [CWE-285: Improper Authorization][2]
* [CWE-639: Authorization Bypass Through User-Controlled Key][3]

[1]: https://cwe.mitre.org/data/definitions/284.html
[2]: https://cwe.mitre.org/data/definitions/285.html
[3]: https://cwe.mitre.org/data/definitions/639.html
