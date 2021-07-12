API2:2019 Verbroken gebruikersauthenticatie
====================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **2** : Detecteerbaarheid **2** | Technisch **3** : Bedrijfsspecifiek |
| Authenticatie in API's is een complex en verwarrend mechanisme. Software- en beveiligingsingenieurs hebben mogelijk misvattingen over wat de grenzen zijn van authenticatie en hoe deze correct te implementeren. Bovendien is het authenticatiemechanisme een gemakkelijk doelwit voor aanvallers, omdat het voor iedereen zichtbaar is. Deze twee punten maken de authenticatiecomponent potentieel kwetsbaar voor veel exploits. | Er zijn twee subproblemen: 1. Gebrek aan beschermingsmechanismen: API's-eindpunten die verantwoordelijk zijn voor authenticatie moeten anders worden behandeld dan reguliere eindpunten en extra beschermingslagen implementeren 2. Verkeerde implementatie van het mechanisme: Het mechanisme wordt gebruikt/geïmplementeerd zonder rekening te houden met de aanvalsvectoren, of het is de verkeerde use case (bijvoorbeeld een authenticatiemechanisme dat is ontworpen voor IoT-clients is misschien niet de juiste keuze voor webtoepassingen). | Aanvallers kunnen controle krijgen over de accounts van andere gebruikers in het systeem, hun persoonlijke gegevens lezen en namens hen gevoelige acties uitvoeren, zoals geldtransacties en het verzenden van persoonlijke berichten. |

## Is de API kwetsbaar?

Verificatie-eindpunten en -stromen zijn activa die moeten worden beschermd. "Wachtwoord vergeten/wachtwoord opnieuw instellen" moet op dezelfde manier worden behandeld als authenticatiemechanismen.

Een API is kwetsbaar als deze:
* [Credential stuffing][1] toe staat waarbij de aanvaller een lijst met geldige
  gebruikersnamen en wachtwoorden heeft.
* Aanvallers toe staat om een brute force-aanval uit te voeren op hetzelfde
  gebruikersaccount, zonder een captcha/accountvergrendelingsmechanisme te
  presenteren.
* Zwakke wachtwoorden toe staat.
* Verzendt gevoelige authenticatiegegevens, zoals authenticatietokens en
  wachtwoorden in de URL.
* De authenticiteit van tokens niet valideert.
* Niet-ondertekende/zwak ondertekende JWT-tokens
  (`"alg":"none"`) accepteert en hun vervaldatum niet valideert.
* Platte tekst, niet-gecodeerde of zwak gehashte wachtwoorden gebruikt.
* Zwakke encryptie sleutels gebruikt.

## Voorbeeld aanvalsscenario's

## Scenario 1

[Credential stuffing][1] (met behulp van
[lijsten met bekende gebruikersnamen/wachtwoorden][2]), is een veelvoorkomende
aanval. Als een toepassing geen automatische beveiliging tegen bedreigingen of
het vullen van inloggegevens implementeert, kan de toepassing worden gebruikt als
wachtwoord tester om te bepalen of de inloggegevens geldig zijn.

## Scenario #2

Een aanvaller start de wachtwoordherstel workflow door een POST-request uit te
voeren: `/api/system/verification-codes` en door de gebruikersnaam op te geven
in de hoofdtekst van het request. Vervolgens wordt een sms-token met 6 cijfers
naar de telefoon van het slachtoffer gestuurd. Omdat de API geen
snelheidsbeperkend beleid implementeert, kan de aanvaller alle mogelijke
combinaties testen met behulp van een multi-threaded script, tegen het
`/api/system/verification-codes/{smsToken}` endpoint om het juiste token binnen
een paar minuten te bemachtigen.

## Hoe te voorkomen

* Zorg ervoor dat u alle mogelijke stromen kent om te authenticeren bij de API
  (mobiel/web/deep links die authenticatie met één klik implementeren/enz.)
* Vraag je engineers welke flow je hebt gemist.
* Lees over uw authenticatiemechanismen. Zorg ervoor dat u begrijpt wat en hoe ze
  worden gebruikt. OAuth is geen authenticatie, ook API-sleutels zijn dat niet.
* Vind het wiel niet opnieuw uit bij authenticatie, het genereren van tokens en
  het opslaan van wachtwoorden. Gebruik de standaarden.
* Endpoints voor gegevensherstel/wachtwoord vergeten moeten worden behandeld
  als inlog endpoints in termen van brute force, snelheidsbeperking en
  vergrendelingsbeveiliging.
* Gebruik de [OWASP Authentication Cheatsheet][3].
* Implementeer waar mogelijk multi-factor authenticatie.
* Implementeer anti-brute force-mechanismen om het opvullen van referenties,
  woordenboekaanvallen en brute force-aanvallen op uw authenticatie-eindpunten
  te verminderen. Dit mechanisme moet strenger zijn dan het reguliere
  snelheidsbeperkende mechanisme op uw API.
* Implementeer [accountvergrendeling][4] / captcha-mechanisme om brute force
  aanvallen tegen specifieke gebruikers te voorkomen. Implementeer zwakke
  wachtwoordcontroles.
* API-sleutels mogen niet worden gebruikt voor gebruikersauthenticatie, maar
  voor [client app/project authenticatie][5].

## Referenties

### OWASP

* [OWASP Key Management Cheat Sheet][6]
* [OWASP Authentication Cheatsheet][3]
* [Credential Stuffing][1]

### Extern

* [CWE-798: Use of Hard-coded Credentials][7]

[1]: https://www.owasp.org/index.php/Credential_stuffing
[2]: https://github.com/danielmiessler/SecLists
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
[4]: https://www.owasp.org/index.php/Testing_for_Weak_lock_out_mechanism_(OTG-AUTHN-003)
[5]: https://cloud.google.com/endpoints/docs/openapi/when-why-api-key
[6]: https://www.owasp.org/index.php/Key_Management_Cheat_Sheet
[7]: https://cwe.mitre.org/data/definitions/798.html
