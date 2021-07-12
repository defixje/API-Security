API5:2019 Verbroken autorisatie op functieniveau
=============================================

| Dreigingsagenten/aanvalsvectoren | Beveiligingszwakte | Gevolgen |
| - | - | - |
| API-specifiek: exploiteerbaarheid **3** | Prevalentie **2** : Detecteerbaarheid **1** | Technisch **2** : Bedrijfsspecifiek |
| Exploitatie vereist dat de aanvaller legitieme API-request verzendt naar het API-endpoint waartoe ze geen toegang zouden moeten hebben. Deze endpoints kunnen worden blootgesteld aan anonieme gebruikers of gewone, niet-bevoorrechte gebruikers. Het is gemakkelijker om deze gebreken in API's te ontdekken, aangezien API's meer gestructureerd zijn en de manier om toegang te krijgen tot bepaalde functies voorspelbaarder is (bijv. door de HTTP-methode te vervangen van GET naar PUT of de string "users" in de URL te wijzigen in "admins" ). | Autorisatiecontroles voor een functie of resource worden meestal beheerd via configuratie en soms op codeniveau. Het implementeren van de juiste controles kan een verwarrende taak zijn, aangezien moderne applicaties vele soorten rollen of groepen en complexe gebruikershiërarchieën kunnen bevatten (bijv. subgebruikers, gebruikers met meer dan één rol). | Dergelijke fouten geven aanvallers toegang tot ongeautoriseerde functionaliteit. Administrator functies zijn belangrijke doelwitten voor dit type aanval. |

## Is de API kwetsbaar?

De beste manier om autorisatieproblemen op functieniveau te vinden, is door een
grondige analyse van het autorisatiemechanisme uit te voeren, rekening houdend
met de gebruikershiërarchie, verschillende rollen of groepen in de toepassing,
en de volgende vragen te stellen:

* Kan een gewone gebruiker toegang krijgen tot beheerder endpoints?
* Kan een gebruiker gevoelige acties uitvoeren (bijv. aanmaken, wijzigen of
  wissen) waartoe ze geen toegang zouden moeten hebben door simpelweg de
  HTTP-methode te wijzigen (bijv. van `GET` naar `DELETE`)?
* Kan een gebruiker uit groep X toegang krijgen tot een functie die alleen aan
  gebruikers uit groep Y zou moeten worden getoond, door simpelweg de endpoint-URL
  en parameters te raden (bijv. `/api/v1/users/export_all`)?

Ga er niet vanuit dat een API-endpoint regulier of alleen beheerder is op
basis van het URL-pad.

Hoewel ontwikkelaars ervoor kunnen kiezen om de meeste beheerder endpoints
onder een specifiek relatief pad te plaatsen, zoals `api/admins`, is het heel
gebruikelijk om deze beheerder endpoints onder andere relatieve paden te
vinden, samen met reguliere endpoints, zoals `api/users`.

## Voorbeeld aanvalsscenario's

### Scenario 1

Tijdens het registratieproces voor een applicatie waarmee alleen uitgenodigde
gebruikers kunnen deelnemen, activeert de mobiele applicatie een API-request naar:
`GET /api/invites/{invite_guid}`. Het response bevat een JSON met details over
de uitnodiging, inclusief de rol van de gebruiker en het e-mailadres van de
gebruiker.

Een aanvaller heeft het verzoek gedupliceerd en de HTTP-methode en het endpoint
gemanipuleerd naar `POST /api/invites/new`. Dit endpoint mag alleen worden
aangeroepen door beheerders met behulp van de beheerdersconsole, die geen
autorisatiecontroles op functieniveau implementeert.

De aanvaller maakt misbruik van het probleem en stuurt zichzelf een uitnodiging
om een beheerdersaccount aan te maken:

```
POST /api/invites/new

{“email”:”hugo@malicious.com”,”role”:”admin”}
```

### Scenario #2

Een API bevat een endpoint dat alleen zichtbaar moet zijn voor beheerders -
`GET /api/admin/v1/users/all`. Dit endpoint retourneert de details van alle
gebruikers van de toepassing en implementeert geen autorisatiecontroles op
functieniveau. Een aanvaller die kennis heeft genomen van de API-structuur, neemt
een weloverwogen gok en slaagt erin toegang te krijgen tot dit endpoint, waardoor
gevoelige details van de gebruikers van de applicatie worden blootgelegd.

## Hoe te voorkomen

Uw toepassing moet een consistente en eenvoudig te analyseren autorisatiemodule
hebben die vanuit al uw bedrijfsfuncties wordt aangeroepen. Vaak wordt een
dergelijke bescherming geboden door een of meer componenten buiten de
applicatiecode.

* De handhavingsmechanismen moeten standaard alle toegang weigeren, waarbij
  expliciete toekenningen aan specifieke rollen nodig zijn voor toegang tot elke
  functie.
* Controleer uw API-endpoints op autorisatiefouten op functieniveau, terwijl u
  rekening houdt met de bedrijfslogica van de applicatie- en groepenhiërarchie.
* Zorg ervoor dat al uw beheerders controllers erven van een beheerder
  abstracte controller die autorisatiecontroles implementeert op basis van de
  groep/rol van de gebruiker.
* Zorg ervoor dat beheerders functies binnen een reguliere controller
  autorisatiecontroles uitvoeren op basis van de groep en rol van de gebruiker.

## Referenties

### OWASP

* [OWASP Article on Forced Browsing][1]
* [OWASP Top 10 2013-A7-Missing Function Level Access Control][2]
* [OWASP Development Guide: Chapter on Authorization][3]

### Extern

* [CWE-285: Improper Authorization][4]

[1]: https://www.owasp.org/index.php/Forced_browsing
[2]: https://www.owasp.org/index.php/Top_10_2013-A7-Missing_Function_Level_Access_Control
[3]: https://www.owasp.org/index.php/Category:Access_Control
[4]: https://cwe.mitre.org/data/definitions/285.html
