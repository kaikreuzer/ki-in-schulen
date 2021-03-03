# Nutzeranleitung Gruppenspielmodus

Voraussetzung ist eine erfolgreich durchgeführte und getestete Installation unserers Projektes anhand der Installationsanleitungen:
* [Windows](./INSTALL-Win.md)
* [Linux](./INSTALL-Lin.md)
* [MacOS](./INSTALL-Mac.md)

Der "__Gruppenspielmodus__" ist für Unter- und Mittelstufe empfohlen aufgrund der weniger aufwändigen Installation. Im "Gruppenspielmodus" wird __pro Schülergruppe eine künstliche Intelligenz trainiert__. Hierfür ist eine Basisinstallation auf einem Schülergruppenrechner erforderlich, damit Daten pro Schülergruppe auf diesem gesammelt werden können.

### Durchführung "Gruppenspielmodus"

#### Einleitung

Pro Schülergruppe wird 1 "Datensammler-Calliope" benötigt, zu dem mehrere "Rennspiel-Calliopes" ihre Rohdaten per Funk senden können. Jede Schülergruppe erhält hierfür einen eigenen Funkkanal, um die Robustheit des Workshops zu erhöhen.

#### Schritt 1 - Rennspiel auf den Calliope Minis der Schüler installieren

Diesen Schritt mit angepasster Funkgruppe (__funkgruppe1__, __funkgruppe2__, ...) wiederholen, bis alle "Rennspiel-Calliopes" für alle Schüler aller Schülergruppen installiert sind.

Einen Calliope Mini per USB an den Schülergruppenrechner anschließen

__MakeCode-Variante__

* Die Datei `/ki-in-schulen-master/Calliope-Rennspiel/Makecode/rennspiel-funkgruppe1-makecode.hex` auf den per USB angeschlossenen Calliope Mini kopieren.

__OpenRoberta-Variante__

* Die Datei `/ki-in-schulen-master/Calliope-Rennspiel/OpenRoberta/RaceCar-funkgruppe1.hex` auf den per USB angeschlossenen Calliope Mini kopieren


#### Schritt 2 - Datensammler pro Schülergruppe installieren

Diesen Schritt mit angepasster Funkgruppe (__funkgruppe1__, __funkgruppe2__, ...) wiederholen, bis die gewünschte Anzahl der Schülergruppen erreicht ist.

Der "Datensammler-Calliope" wird nun an einen USB-Port des Schülergruppenrechners angeschlossen und bleibt dort angeschlossen.

__MakeCode-Variante__

* Die Datei `/ki-in-schulen-master/Calliope-Rennspiel/Makecode/datensammler-funkgruppe1-makecode.hex` auf den per USB angeschlossenen Calliope Mini kopieren

__OpenRoberta-Variante__

* Die Datei `/ki-in-schulen-master/Calliope-Rennspiel/OpenRoberta/RaceCarDataCollector-funkgruppe1.hex` auf den per USB angeschlossenen Calliope Mini kopieren

#### Schritt 3 - COM-Ports der per USB angeschlossenen "Datensammler-Calliopes" herausfinden

* Windows: via Gerätemanager, wird als "USB Serial Device" angezeigt

* Linux: `ls -al /dev/ttyACM*`

  ggf. Nutzer zur `dialout` Nutzergruppe hinzufügen, damit dieser auf den Calliope Mini auch zugreifen darf.

  `sudo usermod -aG <username> dialout`

  `sudo adduser <goethe> dialout`

####Schritt 4 - Datensammel-Phase

* Auf jedem Schülergruppenrechner ausführen: `python ki-datenlogger.py <COM-Port>` (COM-Port in Schritt 3 herausgefunden)

* __Startschuss__ für das Rennspiel auf den "Rennspiel"-Calliopes der Schülergruppen. Schüler spielen das Rennspiel, so lange vorgegeben; dabei werden die Rohdaten per Funk an den "Datensammler-Calliope" übertragen. Am Ende ist eine beliebige Taste am Schülergruppenrechner zu drücken, um das Datensammeln zu beenden und die Rohdaten in eine Datei zu speichern.

* Im Unterverzeichnis `csv-rohdaten` werden die Rohdaten abgelegt unter dem angezeigten Dateinamen, bspw. `ki-rennspiel-log-20210303111213.csv`

####Schritt 5 - Trainingsphase (KI anlernen)

* Auf jedem Schülergruppenrechner ausführen: `python ki-trainieren-sklearn.py <CSV-Datei>` (CSV-Datei in Schritt 4 gespeichert)

* Im Unterverzeichnis `modelle` wird ein trainiertes neuronales Netzwerk abgelegt unter dem angezeigten Dateinamen, bspw. `sklearn-py-modell-20210303114413.pkcls`

####Schritt 6 - Testphase (KI die Steuerung übernehmen lassen)

* Auf jedem Schülergruppenrechner ausführen: `python ki-rennspiel.py sklearn <PKCLS-Datei>` (PKCLS-Datei in Schritt 5 gespeichert)

* Um zu sehen, wie gut oder schlecht die angelernte KI steuert:

  * Turbo ausgeschaltet lassen (Standard)
  * KI spielen lassen durch Drücken von Taste __2__

* Da es sich um ein zufallsgesteuertes Rennspiel handelt, wird für eine Auswertung der KI-Leistung das Rennspiel 50-mal durch die KI gespielt und dann der Medianwert der erreichten Punktzahl angezeigt:

  * Turbo anschalten durch Drücken Taste __T__
  * KI spielen lassen durch Drücken von Taste __2__
  * 50 Episoden werden durch die KI gespielt
  * Medianwert der erreichten Punktzahl wird angezeigt.