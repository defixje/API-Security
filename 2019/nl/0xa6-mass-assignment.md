API6:2019 - Massatoewijzing
===========================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **2** | Prevalentie **2** : Detecteerbaarheid **2** | Technisch **2** : Bedrijfsspecifiek |
| Exploitatie vereist meestal inzicht in de bedrijfslogica, de relaties van objecten en de API-structuur. Exploitatie van massatoewijzing is gemakkelijker in API's, omdat ze door hun ontwerp de onderliggende implementatie van de applicatie blootleggen, samen met de namen van de eigenschappen. | Moderne frameworks moedigen ontwikkelaars aan om functies te gebruiken die de input van de client automatisch binden aan codevariabelen en interne objecten. Aanvallers kunnen deze methode gebruiken om de eigenschappen van gevoelige objecten bij te werken of te overschrijven die de ontwikkelaars nooit wilden blootleggen. | Exploitatie kan leiden tot escalatie van bevoegdheden, manipulatie van gegevens, het omzeilen van beveiligingsmechanismen en meer. |

## Is de API kwetsbaar?

Objecten in moderne toepassingen kunnen veel eigenschappen bevatten. Sommige van
deze eigenschappen moeten rechtstreeks door de client worden bijgewerkt (bijv.
`user.first_name` of `user.address`) en sommige niet (bijv. `user.is_vip`-flag).

Een API-endpoint is kwetsbaar als het automatisch clientparameters omzet in
interne objecteigenschappen, zonder rekening te houden met de gevoeligheid en het
blootstellingsniveau van deze eigenschappen. Hierdoor kan een aanvaller
objecteigenschappen bijwerken waartoe hij geen toegang zou moeten hebben.

Voorbeelden voor gevoelige eigenschappen:

* **Permissie-gerelateerde eigenschappen**: `user.is_admin`, `user.is_vip` mogen
  alleen door beheerders worden ingesteld.
* **Procesafhankelijke eigenschappen**: `user.cash` mag alleen intern worden
  ingesteld na verificatie van de betaling.
* **Interne eigenschappen**: `article.created_time` mag alleen intern door de
  applicatie worden ingesteld.

## Example Attack Scenarios

### Scenario 1

Een applicatie voor het delen van ritten biedt een gebruiker de mogelijkheid om
basisinformatie voor hun profiel te bewerken. Tijdens dit proces wordt een
API-request verzonden naar: `PUT /api/v1/users/me` met het volgende legitieme
JSON-object:

```json
{"user_name":"inons","age":24}
```

Het verzoek `GET /api/v1/users/me` bevat een extra credit_balance eigenschap:

```json
{"user_name":"inons","age":24,"credit_balance":10}
```

De aanvaller herhaalt het eerste request met de volgende payload:

```json
{"user_name":"attacker","age":60,"credit_balance":99999}
```

Omdat het eindpunt kwetsbaar is voor massatoewijzing, ontvangt de aanvaller
credits zonder te betalen.

### Scenario #2

Via een portaal voor het delen van video's kunnen gebruikers inhoud uploaden en
inhoud in verschillende formaten downloaden. Een aanvaller die de API verkent,
ontdekte dat het endpoint `GET /api/v1/videos/{video_id}/meta_data` een
JSON-object retourneert met de eigenschappen van de video. Een van de
eigenschappen is `"mp4_conversion_params":"-v codec h264"`, wat aangeeft dat de
toepassing een shell-opdracht gebruikt om de video te converteren.

De aanvaller ontdekte ook dat het endpoint `POST /api/v1/videos/new` kwetsbaar
is voor massatoewijzing en stelt de client in staat om elke eigenschap van het
video-object in te stellen. De aanvaller stelt als volgt een schadelijke waarde
in: `"mp4_conversion_params":"-v codec h264 && format C:/"`. Deze waarde zal een
shell-opdrachtinjectie veroorzaken zodra de aanvaller de video downloadt als MP4.

## Hoe te voorkomen

* Vermijd indien mogelijk het gebruik van functies die de invoer van een klant
  automatisch binden aan codevariabelen of interne objecten.
* Zet alleen de eigenschappen op de whitelist die door de klant moeten worden
  bijgewerkt.
* Gebruik ingebouwde functies om eigenschappen op de blacklist te zetten die
  niet door klanten mogen worden geopend.
* Indien van toepassing, expliciet schema's definiëren en afdwingen voor de
  payloads van request data.

## Referenties

### Extern

* [CWE-915: Improperly Controlled Modification of Dynamically-Determined Object Attributes][1]

[1]: https://cwe.mitre.org/data/definitions/915.html
