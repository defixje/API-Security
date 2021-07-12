API3:2019 Overmatige gegevensblootstelling
=================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **2** : Detecteerbaarheid **2** | Technisch **2** : Bedrijfsspecifiek |
| Exploitatie van overmatige gegevensblootstelling is eenvoudig en wordt meestal uitgevoerd door het verkeer te sniffen om de API-requests te analyseren, op zoek naar blootstelling aan gevoelige gegevens die niet aan de gebruiker mag worden teruggegeven. | API's zijn afhankelijk van clients om de gegevensfiltering uit te voeren. Omdat API's worden gebruikt als gegevensbronnen, proberen ontwikkelaars ze soms op een generieke manier te implementeren zonder na te denken over de gevoeligheid van de blootgestelde gegevens. Automatische tools kunnen dit type kwetsbaarheid meestal niet detecteren, omdat het moeilijk is om onderscheid te maken tussen legitieme gegevens die door de API worden geretourneerd en gevoelige gegevens die niet moeten worden geretourneerd zonder een diepgaand begrip van de toepassing. | Overmatige blootstelling van gegevens leidt vaak tot blootstelling van gevoelige gegevens. |

## Is de API kwetsbaar?

De API retourneert door ontwerp gevoelige gegevens aan de klant. Deze gegevens
worden meestal aan de clientzijde gefilterd voordat ze aan de gebruiker worden
gepresenteerd. Een aanvaller kan gemakkelijk het verkeer sniffen en de gevoelige
gegevens zien.

## Voorbeeld aanvalsscenario's

### Scenario 1

Het mobiele team gebruikt de `/api/articles/{articleId}/comments/{commentId}`
het endpoint in de artikelweergaven om metadata van opmerkingen weer te geven.
Als een aanvaller het verkeer van mobiele applicaties snifft, ontdekt hij dat
andere gevoelige gegevens met betrekking tot de auteur van de opmerking ook
worden geretourneerd. De endpoint-implementatie gebruikt een generieke
`toJSON()`-methode op het `User`-model, dat PII bevat, om het object te serializen.

### Scenario #2

Met een op IOT gebaseerd bewakingssysteem kunnen beheerders gebruikers met
verschillende machtigingen maken. Een beheerder heeft een gebruikersaccount
gemaakt voor een nieuwe bewaker die alleen toegang mag hebben tot specifieke
gebouwen op het terrein. Zodra de bewaker zijn mobiele app gebruikt, wordt een
API-request geactiveerd zoals:
`/api/sites/111/cameras` om gegevens over de beschikbare camera's te ontvangen
en op het dashboard te tonen. Het response bevat een lijst met details over
camera's in het volgende formaat:
`{"id":"xxx","live_access_token":"xxxx-bbbbb","building_id":"yyy"}`.
Terwijl de client-GUI alleen camera's toont waartoe de bewaker toegang moet
hebben, bevat de eigenlijke API-respons een volledige lijst van alle camera's
op het terrein.

## Hoe te voorkomen

* Vertrouw nooit op de client-side om gevoelige gegevens te filteren.
* Bekijk de responses van de API om er zeker van te zijn dat ze alleen legitieme
  gegevens bevatten.
* Backend-engineers moeten zich altijd afvragen "wie is de consumeerder van de
  gegevens?" voordat een nieuw API-endpoint wordt open gezet.
* Vermijd het gebruik van generieke methoden zoals `to_json()` en `to_string()`.
  Kies in plaats daarvan specifieke eigenschappen die u echt wilt retourneren
* Classificeer gevoelige en persoonlijk identificeerbare informatie (PII) die uw
  toepassing opslaat en waarmee u werkt, en bekijk alle API-requests die
  dergelijke informatie retourneren om te zien of deze reacties een
  beveiligingsprobleem vormen.
* Implementeer een op schema's gebaseerd validatiemechanisme voor responses als
  extra beveiligingslaag. Als onderdeel van dit mechanisme definieert en handhaaft
  u gegevens die worden geretourneerd door alle API-methoden, inclusief fouten.

## Referenties

### Extern

* [CWE-213: Intentional Information Exposure][1]

[1]: https://cwe.mitre.org/data/definitions/213.html
