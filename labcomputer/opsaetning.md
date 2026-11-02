# MASKINTEKLAB – sådan genskaber du maskinen

Opdateret 2026-11-01

## Fakta

Maskinen kører Node-RED til LabJack T7 og VectorPro til Omnitest 50.

| Hvad | Værdi |
| --- | --- |
| Repo | https://github.com/absalon-maskintek/maskinteklab |
| Windows | 11 Pro |
| Bruger | `maskinteklab`, intet password, logger selv ind |
| Computernavn | `MASKINTEKLAB-1` (næste maskine `-2`) |
| Sprog / land / tastatur | English (UK) / Denmark / Danish |
| Talformat | Decimalpunktum, ingen tusindtalsseparator, dato `yyyy-MM-dd` |
| LabJack T7 | `192.168.50.20` / 255.255.255.0, DHCP fra, sat i Kipling |
| PC'ens Ethernet | `192.168.50.10` / 255.255.255.0, ingen gateway |
| Reserveret | `192.168.50.1` til en evt. WiFi-router |
| Modbus | Port 502, Unit-Id 1 |
| Node.js / Node-RED | 24.22.2 / 5.0.9 |
| Node-RED-editor | `http://localhost:1880` |
| Node-RED-dashboard | http://localhost:1880/dashboard |
| Omnitest 50, serienummer | 25-1301-09 (bruges ved registrering hos Mecmesin) |
| VectorPro-login | admin / admin (fabriksstandard) |

## 1. Geninstallation af Windows

Maskinen kører Windows 11 Pro. Den kom fra IT nulstillet med HP Sure Recover og Windows 10 Pro og blev derefter opgraderet.

**Normalt (ikke afprøvet endnu):** Settings → System → Recovery → **Reset this PC** → Remove everything. Det giver Windows 11 direkte. Fortsæt med afsnit 2.

**Hvis Windows ikke starter:** HP Sure Recover (Esc ved opstart → F11) lægger Windows 10 på igen. Derefter:

1. Gennemfør Windows-opsætningen (afsnit 2).
2. Spring Windows Update over. Genstart kun, hvis den beder om det.
3. Hent **Windows 11 Installation Assistant** fra `microsoft.com/software-download/windows11`, og kør den. Windows Update tilbyder ikke selv opgraderingen på en frisk maskine.
4. Lad opgraderingen køre. Den tager op til 2 timer og kræver ikke opsyn.
5. Fortsæt med oprydningen (afsnit 3). Den venter til nu, fordi opgraderingen lægger nogle apps tilbage.

Kilde: [Microsoft: Download Windows 11](https://www.microsoft.com/software-download/windows11)

## 2. Windows-opsætning

Valgene i Windows' første opsætning, skærm for skærm:

| Skærm | Valg |
| --- | --- |
| Language | English (United Kingdom) |
| Country or region | Denmark. Giver rigtig tidszone, og EU-reglerne gør det muligt at afinstallere Bing-søgningen. Kan kun ændres ved nulstilling. |
| Keyboard | Danish. Spring andet tastatur over. |
| How would you like to set up this device? | **Set up for work or school**. Personal use kræver en Microsoft-konto. |
| Sign in with Microsoft | **Domain join instead**, nederst på skærmen. Der meldes ikke noget domæne til. |
| Who's going to use this PC? | `maskinteklab` |
| Password | Tomt. Giver auto-login og springer sikkerhedsspørgsmålene over. |
| Privacy | Alt fra. Diagnostic data: Required only. |
| Customize your experience | Skip |
| Browse the web with Microsoft Edge | Maybe later |

## 3. Oprydning

Laves én gang, efter en eventuel opgradering.

**Afinstallér** under Settings → Apps → Installed apps:

- Teams og Teams Machine-Wide Installer. Uden den sidste kommer Teams tilbage.
- Skype, OneDrive, Copilot, Clipchamp, Outlook (new), Microsoft 365-prøveversionen
- Xbox-apps, Solitaire, News, To Do, Phone Link
- Alt med "trial" i navnet
- HP Wolf Security / Sure Click. Den lægger downloads i sandbox og driller ved installationer.
- **Web Search from Microsoft Bing**. Kan kun fjernes, fordi landet er Denmark.

**Behold:** Edge, Notepad, Calculator, Snipping Tool, Windows Security og HP Support Assistant (BIOS og drivere).

**Startup** under Settings → Apps → Startup: Realtek USB Audio Universal Service fra. Windows Security notification icon til.

**Widgets:** Settings → Personalization → Taskbar → Widgets fra. Settings → Personalization → Lock screen → Your lock screen status → None.

**Start:** Settings → Personalization → Start: slå recently added apps, recommended files, recommendations og account notifications fra. Unpin resten af skrammel fra startmenuen.

**Søgning:** Settings → Privacy & security → Search permissions → Show search highlights fra.

## 4. Talformat og navn

**Talformat:** Win+R → `intl.cpl` → fanen Formats:

| Felt | Værdi |
| --- | --- |
| Format | English (United Kingdom). Ikke "Match Windows display language". |
| Short date | `yyyy-MM-dd`. Feltet ligner en dropdown, men man kan skrive i det. |
| Additional settings → Decimal symbol | `.` |
| Additional settings → Digit grouping symbol | Tom |
| Additional settings → List separator | `,` |

Tjek bagefter, at Calculator viser punktum.

Hvorfor punktum: Node-RED og Kipling skriver altid punktum. Med punktum i Windows skriver VectorPro og LJLogM det samme, så alle filer fra maskinen er ens. Tusindtalsseparatoren er fjernet, fordi `231,213` ellers kan læses på to måder.

**Computernavn:** Settings → System → Rename this PC → `MASKINTEKLAB-1`. Næste maskine bliver `-2`. Maks 15 tegn, kræver genstart.

## 5. Browser

Firefox er standardbrowser. Edge bliver på maskinen, fordi Windows bruger den internt.

1. Installér Firefox fra mozilla.org, eller i Terminal: `winget install Mozilla.Firefox`
2. Valgfrit, men anbefalet: læg [`labcomputer/policies.json`](https://github.com/absalon-maskintek/maskinteklab/blob/main/labcomputer/policies.json) fra repoet her, **før Firefox startes første gang**: `C:\Program Files\Mozilla Firefox\distribution\policies.json`. Den giver blank ny fane og dashboardet som startside og slår reklamer, anbefalinger, AI og telemetri fra.
3. Start Firefox, åbn `about:policies` og tjek, at der står Active.
4. Gør Firefox til standard: Settings → Apps → Default apps → Firefox → **Set default**
5. Sæt også Edges startside til dashboardet, så man lander samme sted, hvis Edge bliver startet: Edge → Settings → Start, home, and new tabs → When Edge starts → **Open these pages** → `http://localhost:1880/dashboard`

## 6. Netværk til T7

T7 og PC'ens Ethernet har deres eget net, 192.168.50.x, med direkte kabel og ingen router. WiFi kan samtidig bruges til internet.

**PC'en:** Win+R → `ncpa.cpl` → højreklik **Ethernet** → Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties → Use the following IP address:

| Felt | Værdi |
| --- | --- |
| IP address | `192.168.50.10` |
| Subnet mask | `255.255.255.0` |
| Default gateway | tom |

**T7'eren**, kun hvis den er nulstillet: Kipling over USB → Ethernet-fanen → DHCP fra → IP `192.168.50.20`, maske `255.255.255.0`, gateway tom. Feltet viser 0.0.0.0, indtil der er link på kablet, selvom værdien er gemt.

**Test** med T7 tilsluttet: `ping 192.168.50.20`

Hvorfor .50: Mange hjemmeroutere bruger 192.168.1.x. Kommer PC'en på sådan et WiFi, mens Ethernet også er på 192.168.1.x, har den to netkort i samme net. Så svarer LabJacken kun nogle gange.

## 7. LabJack-software

LabJacks samlede installer giver Kipling, LJLogM, LJStreamM og LJM-driveren ([LabJack: LJM Software Installer – Windows](https://support.labjack.com/docs/ljm-software-installer-windows)).

1. Hent **Normal Installer** fra linket, og kør den. Fravælg UD-driveren: den er kun til de ældre U3/U6/UE9, og T7 bruger LJM.
2. Tjek, at Kipling og LJLogM ligger i startmenuen.

**Én styrer ad gangen.** Range, negative channel og resolution ligger i selve T7'eren, så Kipling, LJLogM og Node-RED deler dem. Efter brug af Kipling eller LJLogM: tryk **Reset to default** i Node-RED-dashboardet.

## 8. Node.js og Node-RED

Node-RED 5 kræver Node.js 22 eller nyere og anbefaler **Node.js 24** ([Node-RED: Supported Node versions](https://nodered.org/docs/faq/node-versions), opdateret juni 2026).

### Installation

1. Hent **v24** som Windows Installer (.msi), x64, fra [nodejs.org/en/download](https://nodejs.org/en/download). Forsiden kan pege på en nyere version, så vælg 24 eksplicit.
2. Kør installeren med standardvalgene, men lad "Automatically install the necessary tools" være tom. Den installerer Chocolatey, som vi ikke skal bruge. npm følger med.
3. Tjek i Terminal: `node -v` og `npm -v`
4. Installér Node-RED ([Node-RED: Running on Windows](https://nodered.org/docs/getting-started/windows)): `npm install -g node-red`
   - `-g` installerer globalt, så `node-red` kan startes fra enhver mappe.
   - Node-RED's vejledning skriver også `--unsafe-perm`, men nyere npm kender ikke den indstilling og giver kun en advarsel. Den er derfor udeladt.
5. Start Node-RED i Terminal med `node-red`, og åbn `http://localhost:1880` i Firefox.
6. Installér paletten: Menu → Manage palette → Install:
   - `node-red-contrib-modbus`, version **5.62.1 eller nyere**. Der findes flere pakker med næsten samme navn, som ikke vedligeholdes.
   - `@flowfuse/node-red-dashboard`
   - `@flowfuse/node-red-dashboard-2-ui-led`

### Flow

1. Importér et flow fra [`node-red-flows/`](https://github.com/absalon-maskintek/maskinteklab/tree/main/node-red-flows): Menu → Import → vælg filen → Import → Deploy.
2. Lås målefanen: højreklik på fanen → Lock Flow.

Editoren har bevidst intet password. Den låste fane og flowene i repoet er sikringen.

### Autostart

1. Gem [`labcomputer/Start Node-RED.cmd`](https://github.com/absalon-maskintek/maskinteklab/blob/main/labcomputer/Start%20Node-RED.cmd) fra repoet her: `C:\Users\maskinteklab\Desktop\Start Node-RED.cmd`
2. Læg en genvej til den her, sat til **Run: Minimized**: `C:\Users\maskinteklab\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`
3. Slå Firefox' egen autostart fra. Ellers åbner Firefox dashboardet, før Node-RED kører.

```bat
@echo off
title Node-RED
call node-red
pause
```

`call` er nødvendig, fordi `node-red` selv er en `.cmd`-fil. `pause` holder vinduet åbent, så fejlbeskeder kan læses.

Node-RED-vinduet på proceslinjen er også pauseknappen: luk det for at stoppe Node-RED, fx når Kipling skal bruges. Dobbeltklik på skrivebordsfilen starter igen. Starter man den to gange, klager nummer to over, at port 1880 er optaget. Det er ufarligt.

Firewallen åbnes først, når andre enheder skal på, fx via en router.

## 9. VectorPro til Omnitest 50

VectorPro hentes fra [Mecmesin Support](https://help.mecmesin.com/docs/support/vectorpro). Det kræver en brugerkonto, og registreringen beder om Omnitest'ens serienummer: `25-1301-09`.

1. Opret en bruger på Mecmesins supportside, og hent VectorPro.
2. Kør installeren.
3. Giv `maskinteklab` **Full control** på `C:\ProgramData\Mecmesin\VectorPro\`: højreklik → Properties → Security → Edit.
4. Log ind i VectorPro med fabriksstandarden: brugernavn `admin`, password `admin`.

Mecmesins vejledning nævner også `C:\Program Files (x86)\Mecmesin\VectorPro\`, men i praksis var det kun ProgramData, der skulle have Full control (afprøvet 1. november 2026). Se [Mecmesin: VectorPro – Introduction and Initial Setup](https://help.mecmesin.com/node/323).
