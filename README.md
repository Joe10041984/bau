# Anwesen-Finanzierungsdashboard

Statische Website für GitHub Pages. Der Datenstand liegt verschlüsselt in einem privaten Gist bzw. in `data/stand.json`.

## Inhalt

- `index.html` – die App
- `support.js` – Laufzeit
- `styles.css`, `ds-bundle.js` – Stile
- `fonts/` – Schriften (lokal, keine Verbindung zu Google)
- `vendor/` – React (lokal, keine Verbindung zu unpkg.com)

Die App lädt nichts von fremden Servern. Eine Content-Security-Policy in `index.html` erlaubt Verbindungen nur zur eigenen Seite und zu `api.github.com` / `gist.githubusercontent.com`.

Aus älteren Uploads löschen: `_ds_bundle.js`.

Nicht enthalten: `data/stand.json` (wird verschlüsselt aus der App erzeugt) und `setup.json`.

## 1. Repo und Pages

1. github.com → New repository, z. B. `anwesen`, mit README.
2. Inhalt dieses Ordners hochladen (Add file → Upload files).
3. Settings → Pages → Source: „Deploy from a branch“, Branch `main`, Ordner `/ (root)`.
4. Nach 1–2 Minuten erreichbar unter `https://<benutzer>.github.io/anwesen/`.

Bei einem kostenlosen Account muss das Repo öffentlich sein. Es dürfen daher nur verschlüsselte Datendateien ins Repo.

## 2. Token (Fine-grained)

Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token

- Resource owner: eigener Account
- Expiration: max. 1 Jahr
- Repository access: Public repositories (read-only)
- Account permissions → **Gists: Read and write**
- sonst keine Rechte

Token kopieren – er wird nur einmal angezeigt.

## 3. Ersteinrichtung in der App

1. Pages-URL öffnen → Datenstand übernehmen: Ohne Daten zeigt die App nur den Sperrbildschirm. Dort „Datenstand-Datei öffnen“ und die exportierte Datei wählen (Klartext-Export oder verschlüsselte stand.json + Einrichtungs-Passwort).
2. Einstellungen → **PIN setzen** (vor dem ersten Hochladen, sonst landet der Stand unverschlüsselt im Gist).
3. Token eintragen, Gist-ID leer lassen → „Hochladen“. Die App legt einen privaten Gist an und merkt sich die ID.

## 4. Weitere Geräte

Empfohlen: Token und Gist-ID im Passwortmanager ablegen und auf dem neuen Gerät unter „Mit bestehendem Gist verbinden“ eintragen, danach PIN eingeben.

Alternativ über `setup.json` (liegt öffentlich, nur mit langem Einrichtungs-Passwort):

1. Einstellungen → Einrichtungs-Passwort (mind. 8 Zeichen) → „setup.json erzeugen“.
2. Optional mit demselben Passwort „stand.json verschlüsseln“.
3. `setup.json` ins Repo-Root, `stand.json` nach `data/` hochladen.
4. Neues Gerät: Pages-URL öffnen → Einrichtungs-Passwort → PIN.

## Token erneuern

Läuft der Token ab, schlägt der Sync fehl: neuen Token erzeugen, in der App eintragen und `setup.json` neu erzeugen und hochladen.

## Sicherheit

- Datenstand und Token (Browser, Gist, Sicherungsdatei): AES-256-GCM mit zufälligem Datenschlüssel; dieser ist mit der PIN geschützt (PBKDF2-SHA-256, 600.000 Iterationen). Kein PIN-Hash, Schlüssel nur im Arbeitsspeicher, automatische Sperre nach 15 Minuten.
- `setup.json` / `data/stand.json`: AES-256-GCM, PBKDF2 (600.000 Iterationen) aus dem Einrichtungs-Passwort.
- Niemals eine Klartext-`stand.json` ins Repo legen.
