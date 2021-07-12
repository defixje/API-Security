API8:2019 Injectie
===================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **2** : Detecteerbaarheid **3** | Technisch **3** : Bedrijfsspecifiek |
| Aanvallers voeden de API met kwaadaardige gegevens via welke injectievectoren dan ook die beschikbaar zijn (bijvoorbeeld directe invoer, parameters, geïntegreerde services, enz.), in de verwachting dat deze naar een tolk worden verzonden. | Injectiefouten komen veel voor en worden vaak aangetroffen in SQL-, LDAP- of NoSQL-query's, OS-opdrachten, XML-parsers en ORM. Deze gebreken zijn gemakkelijk te ontdekken bij het bekijken van de broncode. Aanvallers kunnen scanners en fuzzers gebruiken. | Injectie kan leiden tot het vrijgeven van informatie en gegevensverlies. Het kan ook leiden tot DoS of volledige overname van de host. |

## Is de API kwetsbaar?

De API is kwetsbaar voor injectiefouten als:

* Door de klant aangeleverde gegevens niet worden gevalideerd, gefilterd of
  opgeschoond door de API.
* Door de klant geleverde gegevens direct worden gebruikt of samengevoegd met
  SQL/NoSQL/LDAP-query's, OS-opdrachten, XML-parsers en Object Relational Mapping
  (ORM)/Object Document Mapper (ODM).
* Gegevens afkomstig van externe systemen (bijv. geïntegreerde systemen)
  niet worden gevalideerd, gefilterd of opgeschoond door de API.

## Voorbeeld aanvalsscenario's

### Scenario 1

Firmware van een apparaat voor ouderlijk toezicht biedt het eindpunt
`/api/CONFIG/restore` die verwacht dat een appId wordt verzonden als een
parameter met meerdere parameters. Met behulp van een decompiler ontdekt een
aanvaller dat de appId rechtstreeks wordt doorgegeven aan een systeemaanroep
zonder enige opschoning:

```c
snprintf(cmd, 128, "%srestore_backup.sh /tmp/postfile.bin %s %d",
         "/mnt/shares/usr/bin/scripts/", appid, 66);
system(cmd);
```

Met de volgende opdracht kan de aanvaller elk apparaat met dezelfde kwetsbare
firmware afsluiten:

```
$ curl -k "https://${deviceIP}:4567/api/CONFIG/restore" -F 'appid=$(/etc/pod/power_down.sh)'
```

### Scenario #2

We hebben een applicatie met basis CRUD-functionaliteit voor bewerkingen met
boekingen. Een aanvaller slaagde erin te identificeren dat NoSQL-injectie
mogelijk was via de `bookingId`-querystringparameter in het
verwijderboekingsverzoek. Zo ziet het verzoek eruit: `DELETE /api/bookings?bookingId=678`.

De API-server gebruikt de volgende functie om verwijderings requests af te
handelen:

```javascript
router.delete('/bookings', async function (req, res, next) {
  try {
      const deletedBooking = await Bookings.findOneAndRemove({'_id' : req.query.bookingId});
      res.status(200);
  } catch (err) {
     res.status(400).json({error: 'Unexpected error occured while processing a request'});
  }
});
```

De aanvaller heeft het verzoek onderschept en de parameter `bookingId`-queryreeks
gewijzigd, zoals hieronder wordt weergegeven. In dit geval slaagde de aanvaller
erin de boeking van een andere gebruiker te verwijderen:

```
DELETE /api/bookings?bookingId[$ne]=678
```

## Hoe te voorkomen

Om injectie te voorkomen, moeten gegevens gescheiden worden gehouden van
opdrachten en query's.

* Voer gegevensvalidatie uit met behulp van een enkele, betrouwbare en actief
  onderhouden bibliotheek.
* Valideer, filter en zuiver alle door de klant verstrekte gegevens of andere
  gegevens afkomstig van geïntegreerde systemen.
* Speciale tekens moeten worden ge-escped met behulp van de specifieke syntaxis
  voor de doelinterpreter.
* Geef de voorkeur aan een veilige API die een geparametriseerde interface biedt.
* Beperk altijd het aantal geretourneerde records om massale openbaarmaking in
  geval van injectie te voorkomen.
* Valideer binnenkomende gegevens met voldoende filters om alleen geldige waarden
  voor elke invoerparameter toe te staan.
* Definieer datatypes en strikte patronen voor alle stringparameters.

## Referenties

### OWASP

* [OWASP Injection Flaws][1]
* [SQL Injection][2]
* [NoSQL Injection Fun with Objects and Arrays][3]
* [Command Injection][4]

### Extern

* [CWE-77: Command Injection][5]
* [CWE-89: SQL Injection][6]

[1]: https://www.owasp.org/index.php/Injection_Flaws
[2]: https://www.owasp.org/index.php/SQL_Injection
[3]: https://www.owasp.org/images/e/ed/GOD16-NOSQL.pdf
[4]: https://www.owasp.org/index.php/Command_Injection
[5]: https://cwe.mitre.org/data/definitions/77.html
[6]: https://cwe.mitre.org/data/definitions/89.html
