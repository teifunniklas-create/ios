# 📱 Filmtagebuch – iOS-App (Capacitor)

Dieses Projekt verpackt deine bestehende Filmtagebuch-Web-App (`www/index.html` –
identisch mit der Electron-Version, inkl. Serien, Staffeln/Folgen, Animationen)
mit [Capacitor](https://capacitorjs.com) in ein natives iOS-Projekt. GitHub
Actions baut daraus automatisch eine `.ipa`-Datei — auf einem macOS-Runner,
komplett kostenlos, ganz ohne eigenen Mac.

## Wichtig: Diese IPA ist UNSIGNIERT

Ohne Apple Developer Account (99 $/Jahr) kann niemand eine `.ipa` erzeugen,
die sich direkt wie eine App-Store-App installieren lässt — das verlangt
Apple so. Was hier gebaut wird, ist eine **unsignierte** `.ipa`. Die
installierst du über ein Sideload-Tool, das im Moment der Installation selbst
ein kostenloses Ad-hoc-Zertifikat zieht:

- **[Sideloadly](https://sideloadly.io/)** (Windows & Mac) – IPA per USB-Kabel
  aufs iPhone laden, mit deiner normalen Apple-ID (kein Entwicklerkonto nötig).
- **[AltStore](https://altstore.io/)** – ähnlich, mit automatischer
  Neuinstallation im Hintergrund.

**Die Einschränkung dabei:** Mit einer kostenlosen Apple-ID läuft so eine App
nur **7 Tage**, danach musst du sie über dieselbe App neu installieren
(Sideloadly/AltStore merken sich das und können es automatisieren). Das ist
eine Grenze von Apple selbst, keine, die sich durch bessere Build-Skripte
umgehen lässt — nur ein bezahlter Developer Account (oder eine Neuinstallation
alle 7 Tage) hebt sie auf.

## So baust du die IPA

1. Dieses gesamte Projekt (`filmtagebuch-ios/`) in ein neues GitHub-Repository
   hochladen (privates Repo ist völlig ausreichend).
2. Im Reiter **Actions** des Repos den Workflow **"Build unsigned iOS IPA"**
   sehen und auf **"Run workflow"** klicken (oder: jeder Push auf `main`
   startet ihn automatisch).
3. Warten, bis der Job durchläuft (macOS-Runner, dauert meist 8–15 Minuten).
4. Am Ende des Laufs unter **Artifacts** die Datei
   `Filmtagebuch-unsigned-ipa` herunterladen — das ist ein ZIP, darin liegt
   `Filmtagebuch-unsigned.ipa`.

## So installierst du sie aufs iPhone

1. Sideloadly oder AltStore auf deinem PC/Mac installieren.
2. iPhone per Kabel verbinden.
3. Die `.ipa`-Datei in Sideloadly/AltStore ziehen.
4. Mit deiner Apple-ID anmelden (die normale, kostenlose reicht).
5. Installieren lassen — auf dem iPhone dann noch unter
   **Einstellungen → Allgemein → VPN & Geräteverwaltung** dem Entwickler-Profil
   vertrauen (einmalig nötig, sonst startet die App nicht).

## Projektstruktur

```
filmtagebuch-ios/
├── www/index.html          ← deine App (identisch zur Electron-Version)
├── icon-source.png         ← App-Icon-Quelle (wird im Workflow skaliert)
├── capacitor.config.json   ← App-ID, Name, Startverzeichnis
├── package.json            ← Capacitor-Abhängigkeiten
└── .github/workflows/
    └── build-ipa.yml       ← baut auf macOS-Runner die .ipa
```

Es gibt noch keinen `ios/`-Ordner im Repo — der wird vom Workflow selbst bei
jedem Lauf über `npx cap add ios` frisch erzeugt (das braucht Xcode-Templates,
die es nur auf macOS gibt, deshalb passiert das im Runner und nicht hier).

## Später einen echten Developer Account einbinden

Solltest du dir später einen Apple Developer Account zulegen, lässt sich der
Workflow um echtes Code-Signing erweitern (Zertifikat + Provisioning Profile
als GitHub Secrets, `xcodebuild -exportArchive` mit einem echten Export-Plist
statt der aktuellen `CODE_SIGNING_ALLOWED=NO`-Variante). Das ist dann kein
Rewrite, nur eine Erweiterung dieses Workflows.
