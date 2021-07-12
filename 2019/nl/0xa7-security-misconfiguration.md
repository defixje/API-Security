API7:2019 Verkeerde configuratie van beveiliging
===================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **3** : Detecteerbaarheid **3** | Technisch **2** : Bedrijfsspecifiek |
| Aanvallers zullen vaak proberen om niet-gepatchte fouten, veelvoorkomende endpoints of onbeveiligde bestanden en mappen te vinden om ongeoorloofde toegang tot of kennis van het systeem te krijgen. | Een verkeerde configuratie van de beveiliging kan op elk niveau van de API-stack plaatsvinden, van netwerkniveau tot applicatieniveau. Er zijn geautomatiseerde tools beschikbaar om verkeerde configuraties, zoals onnodige services of legacy-opties, te detecteren en te exploiteren. | Verkeerde beveiligingsconfiguraties kunnen niet alleen gevoelige gebruikersgegevens blootleggen, maar ook systeemdetails die kunnen leiden tot volledige serveraantasting. |

## Is de API kwetsbaar?

De API kan kwetsbaar zijn als:

* Passende beveiliging ontbreekt in elk deel van de applicatie-stack, of als deze
  onjuist geconfigureerde machtigingen voor cloudservices heeft.
* De nieuwste beveiligingspatches ontbreken of de systemen zijn verouderd.
* Onnodige functies zijn ingeschakeld (bijv. HTTP-gerelateerd).
* Transport Layer Security (TLS) ontbreekt.
* Beveiligingsrichtlijnen worden niet naar clients verzonden (bijv.
  [Security Headers][1]).
* Een Cross-Origin Resource Sharing (CORS)-beleid ontbreekt of is onjuist
  ingesteld.
* Foutmeldingen bevatten stacktraces of andere gevoelige informatie die wordt
  blootgelegd.

## Voorbeeld aanvalsscenario's

### Scenario 1

Een aanvaller vindt het bestand `.bash_history` in de hoofdmap van de
server, die opdrachten bevat die door het DevOps-team worden gebruikt om toegang
te krijgen tot de API:

```
$ curl -X GET 'https://api.server/endpoint/' -H 'authorization: Basic Zm9vOmJhcg=='
```

Een aanvaller kan ook nieuwe endpoints op de API vinden die alleen door het
DevOps-team worden gebruikt en niet zijn gedocumenteerd.

### Scenario #2

Om zich op een specifieke service te richten, gebruikt een aanvaller een
populaire zoekmachine om te zoeken naar computers die rechtstreeks vanaf internet
toegankelijk zijn. De aanvaller vond een host met een populair
databasebeheersysteem, luisterend op de standaardpoort. De host gebruikte de
standaardconfiguratie, waarbij authenticatie standaard is uitgeschakeld, en de
aanvaller kreeg toegang tot miljoenen records met PII, persoonlijke voorkeuren
en authenticatiegegevens.

### Scenario #3

Bij het inspecteren van het verkeer van een mobiele applicatie komt een aanvaller
erachter dat niet al het HTTP-verkeer wordt uitgevoerd op een beveiligd protocol
(bijvoorbeeld TLS). De aanvaller constateert dat dit waar is, met name voor het
downloaden van profielafbeeldingen. Aangezien gebruikersinteractie binair is,
vindt de aanvaller, ondanks het feit dat API-verkeer wordt uitgevoerd op een
beveiligd protocol, een patroon in de grootte van API-responses, die hij
gebruikt om gebruikersvoorkeuren bij te houden over de weergegeven inhoud (bijv.
profielafbeeldingen).

## Hoe te voorkomen

De levenscyclus van de API moet het volgende omvatten:

* Een herhaalbaar verhardingsproces dat leidt tot de snelle en gemakkelijke
  implementatie van een goed afgesloten omgeving.
* Een taak om configuraties in de gehele API-stack te bekijken en bij te werken.
  De beoordeling moet het volgende omvatten: orkestratiebestanden,
  API-componenten en cloudservices (bijv. S3-bucketmachtigingen).
* Een veilig communicatiekanaal voor alle API-interacties, toegang tot statische
  activa (bijv. afbeeldingen).
* Een geautomatiseerd proces om continu de effectiviteit van de configuratie en
  instellingen in alle omgevingen te beoordelen.

Verder:

* Om te voorkomen dat uitzonderingssporen en andere waardevolle informatie worden
  teruggestuurd naar aanvallers, moet u, indien van toepassing, alle
  API-responspayloadschema's definiëren en afdwingen, inclusief foutreacties.
* Zorg ervoor dat API alleen toegankelijk is voor de opgegeven HTTP-methodes.
  Alle andere HTTP-methodes moeten worden uitgeschakeld (bijvoorbeeld `HEAD`).
* API's die toegang verwachten vanaf browsergebaseerde clients (bijv. WebApp
  front-end) moeten een correct Cross-Origin Resource Sharing (CORS)-beleid
  implementeren.

## Referenties

### OWASP

* [OWASP Secure Headers Project][1]
* [OWASP Testing Guide: Configuration Management][2]
* [OWASP Testing Guide: Testing for Error Codes][3]
* [OWASP Testing Guide: Test Cross Origin Resource Sharing][9]

### Extern

* [CWE-2: Environmental Security Flaws][4]
* [CWE-16: Configuration][5]
* [CWE-388: Error Handling][6]
* [Guide to General Server Security][7], NIST
* [Let’s Encrypt: a free, automated, and open Certificate Authority][8]

[1]: https://www.owasp.org/index.php/OWASP_Secure_Headers_Project
[2]: https://www.owasp.org/index.php/Testing_for_configuration_management
[3]: https://www.owasp.org/index.php/Testing_for_Error_Code_(OTG-ERR-001)
[4]: https://cwe.mitre.org/data/definitions/2.html
[5]: https://cwe.mitre.org/data/definitions/16.html
[6]: https://cwe.mitre.org/data/definitions/388.html
[7]: https://csrc.nist.gov/publications/detail/sp/800-123/final
[8]: https://letsencrypt.org/
[9]: https://www.owasp.org/index.php/Test_Cross_Origin_Resource_Sharing_(OTG-CLIENT-007)
