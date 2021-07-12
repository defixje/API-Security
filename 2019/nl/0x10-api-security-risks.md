API-beveiligingsrisico's
==================

Voor de risicoanalyse is gebruik gemaakt van de [OWASP Risicobeoordelingsmethodologie][1].

Onderstaande tabel geeft een overzicht van de terminologie die bij de
risicoscore hoort.

| Dreigingsagenten | Exploiteerbaarheid | Zwakte Prevalentie | Detecteerbaarheid van zwakte | Technische impact | Bedrijfseffecten |
| :-: | :-: | :-: | :-: | :-: | :-: |
| API-specifiek | Eenvoudig: **3** | Wijdverbreid **3** | Makkelijk **3** | Ernstig **3** | Bedrijfsspecifiek |
| API-specifiek | Gemiddeld: **2** | Algemeen **2** | Gemiddeld **2** | Matig **2** | Bedrijfsspecifiek |
| API-specifiek | Moeilijk: **1** | Moeilijk **1** | Moeilijk **1** | Minor **1** | Bedrijfsspecifiek |

**Opmerking**: deze benadering houdt geen rekening met de waarschijnlijkheid van
de bedreigingsagent. Het houdt ook geen rekening met de verschillende technische
details die verband houden met uw specifieke toepassing. Elk van deze factoren
kan de algehele kans dat een aanvaller een bepaalde kwetsbaarheid vindt en
misbruikt aanzienlijk beïnvloeden. Deze beoordeling houdt geen rekening met de
werkelijke impact op uw bedrijf. Uw organisatie zal moeten beslissen hoeveel
beveiligingsrisico's van applicaties en API's de organisatie bereid is te
accepteren, gezien uw cultuur, branche en regelgevende omgeving. Het doel van de
OWASP API Security Top 10 is niet om deze risicoanalyse voor u te doen.

## Referenties

### OWASP

* [OWASP Risk Rating Methodology][1]
* [Article on Threat/Risk Modeling][2]

### Extern

* [ISO 31000: Risk Management Std][3]
* [ISO 27001: ISMS][4]
* [NIST Cyber Framework (US)][5]
* [ASD Strategic Mitigations (AU)][6]
* [NIST CVSS 3.0][7]
* [Microsoft Threat Modeling Tool][8]

[1]: https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology
[2]: https://www.owasp.org/index.php/Threat_Risk_Modeling
[3]: https://www.iso.org/iso-31000-risk-management.html
[4]: https://www.iso.org/isoiec-27001-information-security.html
[5]: https://www.nist.gov/cyberframework
[6]: https://www.asd.gov.au/infosec/mitigationstrategies.htm
[7]: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator
[8]: https://www.microsoft.com/en-us/download/details.aspx?id=49168
