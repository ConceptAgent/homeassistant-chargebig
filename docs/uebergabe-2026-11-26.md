# Übergabe: chargeBIG für Home Assistant

Stand: 26. November 2026 · Autor: ConceptAgent <mail@conceptagent.de>

## Aktueller Stand

- **Version:** 0.1.4, Lizenz GPL-3.0.
- **Repository:** `github.com/ConceptAgent/homeassistant-chargebig` (umgezogen von
  `KevinCluse/homeassistant-chargebig`; die alte URL leitet automatisch weiter).
- **Listung im offiziellen Katalog:** Die Integration ist im HACS-Default-Katalog
  gelistet, der seit Home Assistant 2026.11 im Core als **Marketplace** läuft
  (Settings → Marketplace). Für ältere Home-Assistant-Versionen funktioniert der
  klassische HACS-Weg unverändert weiter.
- **Tests/CI:** 47 Tests, Lint (ruff) und die Validate-Pipeline (hassfest + HACS-Action)
  sind grün auf `main`.
- **Mindestversion Home Assistant:** 2025.8 (wegen `OptionsFlowWithReload` im
  Options-Flow).
- **Funktionsumfang:** Start/Pause/Resume von Ladevorgängen, Live-Werte (Leistung,
  Energie, Kosten), Online-/Lade-Status als `binary_sensor`, Diagnose-Export mit
  automatischer Schwärzung sensibler Felder. Details zu Entities/Services stehen in der
  README.

## Offene Punkte

- **Keine offenen Einreichungen mehr beim Katalog.** Die ursprüngliche Aufnahme
  (`hacs/default#10342`) wurde am 16.11. gemerged; die anschließende Umbenennung des
  Katalogeintrags auf `ConceptAgent/homeassistant-chargebig` (`hacs/default#14334`)
  wurde am 26.11. ebenfalls gemerged. Aktuell ist nichts beim Maintainer offen.
- **"Ende eines Ladevorgangs" ist nicht abbildbar**, da der Backend-Capture dafür keinen
  eigenen Aufruf zeigt — nur Pause/Resume. Ein Nutzer-Issue mit einem HAR-Capture des
  entsprechenden App-Vorgangs wäre der Weg, das nachzurüsten.
- **Kein History-Endpunkt** im Backend gefunden; vergangene Ladevorgänge laufen über
  Home Assistants eigene Langzeitstatistik der Energie-Sensor-Entity, nicht über einen
  importierten Verlauf.

## Entscheidungen

- Die Integration sollte **öffentlich für alle nutzbar** sein, nicht nur privat —
  daher die offizielle Einreichung beim HACS-Default-Katalog statt eines reinen
  Custom-Repository-Setups.
- **Keine "Carica"-Markenbegriffe** in nutzerseitigen Texten (technische Hostnamen im
  Code/Doku sind davon ausgenommen, da funktional nötig).
- **Keine reale Wallbox-ID** in Beispielen, Doku oder Tests — durchgängig Platzhalter
  `XXYXX`. Eine zuvor versehentlich verwendete echte ID wurde vollständig aus der
  Git-Historie entfernt.
- **Icon/Branding:** ein selbst gezeichnetes, abstraktes Symbol in generischem
  technischem Blau/Weiß — bewusst **nicht** das tatsächliche MAHLE-Blau oder das
  chargeBIG-/MAHLE-Logo, um keine Markenverwechslung nahezulegen.
- Reverse-Engineering-Charakter ist in README/Doku offen benannt (keine offizielle
  chargeBIG-API; es existiert ein Nutzungsbedingungen-Passus, der Reverse Engineering
  untersagt — das Risiko ist bekannt und wurde bewusst in Kauf genommen).

## Bekannte Fallen

- **Diagnose-Redaction ist vollständig zu halten.** Die Liste der zu schwärzenden Felder
  (`TO_REDACT` in `custom_components/chargebig/diagnostics.py`) muss bei jedem neuen
  API-Feld geprüft werden — ein fehlendes Feld (`userId`) war der letzte blockierende
  Punkt vor der Katalog-Aufnahme.
- **Niemals rohe HAR-Captures committen** — sie enthalten Klartext-Zugangsdaten und
  Session-Tokens.
- **Test-Umgebung braucht Python 3.13**, da `pytest-homeassistant-custom-component`
  das zwingend voraussetzt. `pytest`, `pytest-asyncio` und `pytest-cov` nicht separat in
  `requirements_test.txt` pinnen — das führt zu Versionskonflikten; sie werden transitiv
  von `pytest-homeassistant-custom-component` aufgelöst.
- **Releases/Tags lassen sich nicht aus jeder Umgebung pushen** (manche Setups haben
  keine Tag-Push-Berechtigung). In dem Fall muss die GitHub-Release manuell über die
  Weboberfläche angelegt werden — und der Release-Link in einer laufenden
  Katalog-Einreichung muss dann von Hand aktualisiert werden.
- **Icon-Rendering per Headless-Browser** erzeugt bei direkter Auflösung sichtbare
  Kanten-Artefakte (keine Kantenglättung, Viewport-Clipping). Abhilfe: deutlich größer
  rendern und mit Lanczos-Filter auf die Zielgröße herunterskalieren.

## Nächste Schritte

- Laufendes Monitoring von Nutzer-Issues im neuen Repository (`ConceptAgent/…`), da die
  Integration als reverse-engineert jederzeit durch Änderungen am chargeBIG-Backend
  brechen kann.
- Bei Gelegenheit prüfen, ob sich ein expliziter "Ladevorgang beenden"-Aufruf im Backend
  finden lässt (siehe "Offene Punkte").
- Owner-Referenzen in `manifest.json`/`hacs.json`/README sind bereits auf ConceptAgent
  umgestellt — hier ist nichts weiter zu tun.
