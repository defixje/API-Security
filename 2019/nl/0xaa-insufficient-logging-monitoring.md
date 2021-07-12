API10:2019 Onvoldoende logging en monitoring
============================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **2** | Prevalentie **3** : Detecteerbaarheid **1** | Technisch **2** : Bedrijfsspecifiek |
| Aanvallers profiteren van een gebrek aan logging en monitoring om systemen te misbruiken zonder opgemerkt te worden. | Zonder logging en monitoring, of met onvoldoende logging en monitoring, is het bijna onmogelijk om verdachte activiteiten op te sporen en er tijdig op te reageren. | Zonder zicht op lopende kwaadaardige activiteiten hebben aanvallers voldoende tijd om systemen volledig te compromitteren. |

## Is de API kwetsbaar?

De API is kwetsbaar als:

* Het geen logs produceert, het logniveau is niet correct ingesteld, of
  logberichten bevatten niet genoeg details.
* Logintegriteit is niet gegarandeerd (bijv. [Loginjectie][1]).
* Logboeken worden niet continu gecontroleerd.
* API-infrastructuur wordt niet continu gemonitord.

## Voorbeeld aanvalsscenario's

### Scenario 1

Toegangssleutels van een administratieve API zijn gelekt op een openbare
repository. De eigenaar van de repository werd per e-mail op de hoogte gebracht
van het potentiële lek, maar het duurde meer dan 48 uur om op het incident te
reageren, en blootstelling van de toegangssleutels heeft mogelijk toegang tot
gevoelige gegevens mogelijk gemaakt. Door onvoldoende logging kan het bedrijf
niet beoordelen tot welke gegevens kwaadwillenden toegang hebben gehad.

### Scenario #2

Een platform voor het delen van video's werd getroffen door een "grootschalige"
aanval op het vullen van inloggegevens. Ondanks dat mislukte inlog pogingen
werden geregistreerd, werden er tijdens de aanvalsperiode geen waarschuwingen
geactiveerd. Als reactie op klachten van gebruikers werden API-logs geanalyseerd
en werd de aanval gedetecteerd. Het bedrijf moest een openbare aankondiging doen
waarin gebruikers werden gevraagd hun wachtwoord opnieuw in te stellen en het
incident te melden aan regelgevende instanties.

## Hoe te voorkomen

* Registreer alle mislukte authenticatiepogingen, geweigerde toegang en
  invoervalidatiefouten.
* Logs moeten worden geschreven in een formaat dat geschikt is voor gebruik door
  een oplossing voor logbeheer en moeten voldoende details bevatten om de
  kwaadwillende actor te identificeren.
* Logs moeten worden behandeld als gevoelige gegevens en hun integriteit moet
  worden gegarandeerd tijdens transport.
* Configureer een monitoringsysteem om de infrastructuur, het netwerk en de
  API-functionaliteit continu te bewaken.
* Gebruik een Security Information and Event Management (SIEM)-systeem om logs
  van alle componenten van de API-stack en hosts te verzamelen en te beheren.
* Configureer aangepaste dashboards en waarschuwingen, zodat verdachte
  activiteiten eerder kunnen worden gedetecteerd en beantwoord.

## Referenties

### OWASP

* [OWASP Logging Cheat Sheet][2]
* [OWASP Proactive Controls: Implement Logging and Intrusion Detection][3]
* [OWASP Application Security Verification Standard: V7: Error Handling and
  Logging Verification Requirements][4]

### Extern

* [CWE-223: Omission of Security-relevant Information][5]
* [CWE-778: Insufficient Logging][6]

[1]: https://www.owasp.org/index.php/Log_Injection
[2]: https://www.owasp.org/index.php/Logging_Cheat_Sheet
[3]: https://www.owasp.org/index.php/OWASP_Proactive_Controls
[4]: https://github.com/OWASP/ASVS/blob/master/4.0/en/0x15-V7-Error-Logging.md
[5]: https://cwe.mitre.org/data/definitions/223.html
[6]: https://cwe.mitre.org/data/definitions/778.html
