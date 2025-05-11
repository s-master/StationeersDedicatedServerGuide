# Stationeers Dedicated Server Anleitung

Alle Anweisungen funktionieren sowohl unter Windows- als auch unter Linux-Servern. Im Folgenden verwende ich die Linux-Syntax. In den meisten Fällen können Sie einfach `./rocketstation_DedicatedServer.x86_64` anstelle von `.\rocketstation_DedicatedServer.exe` verwenden; der Rest bleibt gleich.

## Arbeitsverzeichnis

In allen Fällen sollte das Arbeitsverzeichnis dort sein, wo die Installation liegt.

```bash
~/.steam/debian-installation/steamapps/common/Stationeers Dedicated Server
```

## Unity-Befehle

Sie können zudem die Liste der Befehlszeilenargumente von Unity [hier](https://docs.unity3d.com/Manual/CommandLineArguments.html) einsehen.

### Protokolldatei

Am bemerkenswertesten ist `-logFile`: Legt fest, wohin Unity die Editor- oder Standalone-Protokolldatei für Windows/Linux/OSX schreibt. Um die Ausgabe in der Konsole anzuzeigen, geben Sie `-` als Pfadnamen an.

Unter Windows verwenden Sie `-logfile -`, um die Ausgabe auf `stdout` umzuleiten, welches standardmäßig nicht die Konsole ist. Dadurch ist keine Eingabe über die Konsole möglich. Speichern kann daher nur über den Autosave erfolgen. Dies wird in Zukunft verbessert, sobald wir etwas wie RCON hinzufügen.

## Befehle

Alle Befehle, die mit einem `-` beginnen, sind Startbefehle; die meisten davon können auch zur Laufzeit verwendet werden. Die Laufzeitbefehle haben denselben Namen, jedoch ohne das `-`.

**Beispiel:**
`-load "Mein gespeichertes Spiel"` wird zu `load "Mein gespeichertes Spiel"`.

## Welt-Namen

* moon
* mars
* europa
* europa2
* mimas
* vulcan
* vulcan2
* space
* loulan
* venus

# Ein neues Spiel starten

Der Befehl `-new` startet ein neues Spiel mit dem angegebenen Weltnamen als Argument.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -new moon
```

Damit wird das Spiel jedoch nicht automatisch gespeichert, da noch kein Verzeichnis für den Speicherstand erstellt wurde.
Sie müssen anschließend den Befehl `save` ausführen, damit der Autosave fortgesetzt wird. Aus diesem Grund wird empfohlen, `-new` nur zum Testen Ihres Servers zu verwenden.

# Laden eines gespeicherten Spiels

Der Befehl `-load` überprüft das Verzeichnis `saves`, das sich im Stammverzeichnis der ausführbaren Datei befindet. Er verwendet den Verzeichnisnamen als Speicherstandsnamen und prüft, ob darin eine `world.xml` vorhanden ist. Wenn weder das Verzeichnis noch die `world.xml` existiert, wird ein Fehler protokolliert und das Spiel wird nicht geladen.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -load "Mein gespeichertes Spiel"
```

Zusätzlich kann dem Befehl `-load` ein zweites Argument hinzugefügt werden, das ein neues Spiel erstellt, falls das Verzeichnis aus dem ersten Argument nicht existiert. Dies erspart einen zusätzlichen Schritt, wenn Sie einen neuen Server starten möchten, ohne sich Gedanken über bestehende Speicherstände machen zu müssen.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -load "Mein gespeichertes Spiel" moon
```

## Neueste Speicherung laden

Der Befehl `-loadlatest` durchsucht das Verzeichnis (Speicherstand basierend auf dem ersten Argument) und verwendet die zuletzt geänderte `world.xml`, sowohl im Haupt-Speicherverzeichnis als auch im Unterordner `Backup`. `-loadlatest` funktioniert hinsichtlich der Fallbacks identisch zu `-load`. `-loadlatest` ersetzt den Befehl `-load` und darf nicht gleichzeitig mit diesem verwendet werden, da es sonst nicht funktioniert.

Wenn keine `world.xml` im Verzeichnis gefunden wird, wird auf das erste Argument (den benannten Speicherstand) zurückgegriffen. Schlägt auch dies fehl, wird der zweite Argument-Weltenname verwendet.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -loadlatest "Mein gespeichertes Spiel" moon
```

# Einstellungen

Mit `-settings` können Sie Eigenschaften in der `setting.xml` festlegen, die sich im Stammverzeichnis der ausführbaren Datei befindet. Dies ist nützlich, um Servernamen, Ports, Passwörter, Sichtbarkeit usw. zu konfigurieren.

Wichtige Eigenschaften sind:

* **StartLocalHost**: Startet den Server lokal auf der Maschine und erlaubt alle Client-Verbindungen. Standard: `false`
* **ServerVisible**: Sendet Anfragen an unseren Master-Server, damit der Server in der Serverliste des Clients angezeigt wird. Standard: `true`
* **LocalIpAddress**: Die IP-Adresse, an die der Server gebunden wird. Standard: `0.0.0.0`
* **GamePort**: Der Port, auf dem der Server lauscht. Standard: `27016`
* **ServerName**: Der Name des Servers, der in der Serverliste angezeigt wird. Standard: `Stationeers`
* **ServerPassword**: Ein optionales Passwort zur Sperrung des Servers. Standard: `null`
* **AutoSave**: Aktiviert/deaktiviert den Autosave im Spiel. Standard: `true`
* **SaveInterval**: Wenn Autosave aktiviert ist, die Anzahl der Sekunden zwischen den Speicherpunkten. Standard: `300`
* **ServerMaxPlayers**: Maximale Anzahl der Spieler auf dem Server (1–30). Standard: `10`
* **UPNPEnabled**: Aktiviert/deaktiviert die UPnP-Funktion. Standard: `true`
* **SavePath**: Ein optionaler benutzerdefinierter Pfad für Spielstände. Standard: `null`

Die Einstellungen werden als Schlüssel-Wert-Paare angegeben, getrennt durch Leerzeichen.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -settings ServerName "Mein cooler Server" StartLocalHost true ServerVisible true GamePort 27016 AutoSave true SaveInterval 300 ServerPassword abc123 ServerMaxPlayers 13 UPNPEnabled true
```

## Pfad der Einstellungen

Mit dem Befehl `-settingspath` kann ein benutzerdefinierter Speicherort für die Einstellungen festgelegt werden. Dieser Befehl muss vor `-settings` verwendet werden und der Pfad muss `.xml` enthalten, da er sonst nicht funktioniert.

**Beispiel:**

```bash
$ ./rocketstation_DedicatedServer.x86_64 -settingspath "~/Neuer Ordner/CustomSettings.xml" -settings ServerName "Mein cooler Server"
```

# Spiel speichern

Der Befehl `save` kann nur zur Laufzeit auf dem Server verwendet werden. Er kann den aktuellen Speicherstand überschreiben oder einen neuen anlegen, genau wie im Client.

**Beispiel:**

```bash
save "Mein gespeichertes Spiel"
```

# In-Game-Hilfe

Der Befehl `help` zeigt zur Laufzeit alle Hilfetexte zu den Befehlen an. Da die Liste sehr umfangreich ist, können Sie mit `help <Befehl>` nur die Hilfe zum gewünschten Befehl anzeigen lassen. Eine Liste aller Befehle erhalten Sie mit `help list` oder `help l`.

**Beispiel:**

```bash
help version
```

# Spieler kicken und bannen

Der Befehl `kick <clientId>` entfernt einen Spieler sofort vom Server. Hinweis: Der Spieler muss in der Welt vorhanden sein.

Der Befehl `ban <clientId>` kann jederzeit verwendet werden, auch wenn der Spieler nicht online ist. Er fügt die ClientID des Spielers zur Blacklist hinzu, die sich im `SavePath` Ihrer Einstellungen befindet. Um den Bann aufzuheben, entfernen Sie die ClientID aus der Datei und führen `ban refresh` aus, um die Blacklist ohne Serverneustart zu aktualisieren.

# Client-autorisierte Server-Befehle

Sie können Server-Befehle von jedem verbundenen Client ausführen. Voraussetzungen:

1. Ein `ServerAuthSecret` in der `setting.xml` des Servers, z. B.:

   ```xml
   stationeers
   ```
2. Dieselbe Einstellung auch im Client

Anschließend können Sie den Befehl `serverrun` auf dem Client verwenden. Dieser sendet bei jeder Nachricht das Secret. Alle Nachrichten werden abgelehnt, wenn die Secrets nicht übereinstimmen oder kein Secret gesetzt ist.

**Beispiel:**

```bash
serverrun save "Meine Welt"
```

# Beispiele

Alle Beispielskripte befinden sich im [examples-Ordner](./examples/) dieses Repositories.

# Bekannte Probleme

* Auf Linux-Servern mit mehreren IP-Adressen kann es beim Verbinden zu Timeouts kommen: Der Server erscheint im Ingame-Browser, aber die Verbindung schlägt fehl. Abhilfe schafft das Setzen von `LocalIpAddress` auf die gewünschte IP in der Konfiguration.
* Unter Linux führt das Löschen der Konsole zu Pufferproblemen und seltsamem Verhalten. Eingaben (z. B. manuelles Speichern mit `save`) funktionieren, jedoch werden keine Rückmeldungen angezeigt.
* Die Formatierung der Hilfetexte ist sehr schwer lesbar.

# Stationeers-Befehle – 0.2.3649.17688

| Befehl               | Startbefehl? | Argumente                                                                  | Hilfe                                                                                                                                                           |
| :------------------- | :----------: | :------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `help`               |     False    | commands, list (l), \<key>, tofile                                         | Gibt die Hilfsausgabe in eine Datei aus                                                                                                                         |
| `clear`              |     False    |                                                                            | Löscht den gesamten Konsolentext                                                                                                                                |
| `quit`               |     False    |                                                                            | Beendet das Spiel sofort ohne Eingabeaufforderungen                                                                                                             |
| `exit`               |     False    |                                                                            | Verlässt eine Sitzung und kehrt zum 'StartMenu' zurück                                                                                                          |
| `leave`              |     False    |                                                                            | Verlässt eine Sitzung und kehrt zum 'StartMenu' zurück                                                                                                          |
| `newgame`            |     True     | worldName                                                                  | Startet automatisch ein neues Spiel in einer angegebenen Welt. Es muss ein Weltname als Argument übergeben werden                                               |
| `new`                |     True     | worldName                                                                  | Startet automatisch ein neues Spiel in einer angegebenen Welt. Es muss ein Weltname als Argument übergeben werden                                               |
| `loadgame`           |     True     | list, \<filename>, \<filename> (optional)\<worldname>                      | Lädt eine gespeicherte Weltdatei. Kann auch verwendet werden, um ein neues Spiel via Startbefehl zu starten, z. B. `-load "Mein gespeichertes Spiel" moon`      |
| `load`               |     True     | list, \<filename>, \<filename> (optional)\<worldname>                      | Lädt eine gespeicherte Weltdatei. Kann auch verwendet werden, um ein neues Spiel via Startbefehl zu starten, z. B. `-load "Mein gespeichertes Spiel" moon`      |
| `loadlatest`         |     True     | list, \<filename>, \<filename> (optional)\<worldname>                      | Lädt die zuletzt gespeicherte Datei, einschließlich Autosaves                                                                                                   |
| `joingame`           |     True     | \[address]:\[port]                                                         | Verbindet einen Client mit dem Server                                                                                                                           |
| `join`               |     True     | \[address]:\[port]                                                         | Verbindet einen Client mit dem Server                                                                                                                           |
| `steam`              |     False    |                                                                            | Befehle zum Testen der Facepunch-API. Prüft, ob Steam initialisiert ist und ob ein DLC gekauft wurde                                                            |
| `listnetworkdevices` |     False    | id                                                                         | Listet alle Geräte in einem Netzwerk auf, z. B. PipeNetwork, CableNetwork, ChuteNetwork                                                                         |
| `testbytearray`      |     False    |                                                                            | Testet jedes Item in der Welt, um die Netzwerk-Lese/Schreib-Funktionen zu prüfen. Nur im Editor aktiviert. Liefert eine Referenz-ID für die Prüfung eines Items |
| `rocketbinary`       |     False    | togglelogbps                                                               | Beginnt mit der Protokollierung der Größe jedes Abschnitts eines Delta-Updates                                                                                  |
| `imgui`              |     False    |                                                                            | Schaltet ImguiInWorldTestCube ein/aus                                                                                                                           |
| `atmos`              |     False    | pipe, world, room, global, thing, cleanup, count                           | Aktiviert Atmosphären-Debugging                                                                                                                                 |
| `thing`              |     False    |                                                                            | Gibt ohne Argument die Gesamtanzahl der Dinge zurück. Optionen: find, delete, spawn, info                                                                       |
| `keybindings`        |     False    | reset                                                                      | Setzt den Keybindings-Stack zurück. Zeigt alle an LocalHuman gebundenen Tastbelegungen an                                                                       |
| `reset`              |     False    |                                                                            | Startet die Anwendung neu                                                                                                                                       |
| `version`            |     False    |                                                                            | Gibt die Spielversion zurück                                                                                                                                    |
| `logtoclipboard`     |     False    |                                                                            | Kopiert den Inhalt des Konsolenpuffers in die System-Zwischenablage                                                                                             |
| `kick`               |     False    | \<clientId>                                                                | Kickt Clients vom Server                                                                                                                                        |
| `ban`                |     False    | \<clientId>, refresh                                                       | Bannt einen Client vom Server (nur Server-Befehl)                                                                                                               |
| `upnp`               |     False    |                                                                            | Gibt den UPnP-Status zurück                                                                                                                                     |
| `network`            |     False    |                                                                            | Gibt den aktuellen Netzwerkstatus zurück                                                                                                                        |
| `pause`              |     False    | true, false                                                                | Pausiert/setzt das Spiel fort (inklusive Clients)                                                                                                               |
| `say`                |     False    |                                                                            | Sendet eine Nachricht an alle verbundenen Spieler                                                                                                               |
| `save`               |     False    | \<filename>, delete (d \| rm) \<filename>, list (l)                        | Speichert das aktuelle Spiel am angegebenen Pfad                                                                                                                |
| `log`                |     False    | \<logname> (optional), clear                                               | Gibt alle Protokolle in eine Datei aus                                                                                                                          |
| `discord`            |     False    |                                                                            | Interaktion mit dem Discord-SDK                                                                                                                                 |
| `settings`           |     True     | list, print, \<PropertyName> \<Value>                                      | Ändert die settings.xml, z. B. `settings servermaxplayers 5`                                                                                                    |
| `netconfig`          |     True     | list, print, \<PropertyName> \<Value>                                      | Ändert die NetConfig.xml, z. B. `netconfig ip 127.0.0.1`                                                                                                        |
| `settingspath`       |     True     | \<full-directory-path>                                                     | Legt den Standardpfad für Einstellungen auf einen neuen Speicherort fest. Nur Startbefehl. Wenn keiner gefunden wird, wird der Standard verwendet               |
| `regeneraterooms`    |     False    |                                                                            | Erzeugt alle Räume für die Welt neu                                                                                                                             |
| `stormbegin`         |     False    |                                                                            | Startet ein Wetterereignis                                                                                                                                      |
| `stormend`           |     False    |                                                                            | Beendet ein Wetterereignis                                                                                                                                      |
| `debugthreads`       |     False    | GameTick                                                                   | Zeigt die Laufzeiten der Arbeitsthreads                                                                                                                         |
| `status`             |     False    |                                                                            | Zeigt eine Reihe von Informationen zum Zustand des Servers                                                                                                      |
| `masterserver`       |     False    | refresh                                                                    | Befehle zur Interaktion mit dem Master-Server                                                                                                                   |
| `deletelooseitems`   |     False    |                                                                            | Entfernt alle nicht platzierten Gegenstände aus der Welt                                                                                                        |
| `emote`              |     False    | emoteName                                                                  | Lässt den Spielercharakter den gewünschten Emote ausführen                                                                                                      |
| `serverrun`          |     False    | Command                                                                    | Sendet eine Nachricht an den Server, um serverseitige Befehle auszuführen                                                                                       |
| `suntime`            |     False    | time                                                                       | Setzt die Tageszeit zwischen 0 und 1 (0 = Sonnenaufgang, 0.5 = Sonnenuntergang)                                                                                 |
| `windowheight`       |     False    | \<height>, reset (r)                                                       | Legt die Fensterhöhe fest oder setzt sie zurück                                                                                                                 |
| `cleanupplayers`     |     False    | dead, disconnected, all                                                    | Bereinigt Spielerkörper                                                                                                                                         |
| `networkdebug`       |     False    |                                                                            | Zeigt das Netzwerk-Debug-Fenster                                                                                                                                |
| `difficulty`         |     True     | \<difficulty>                                                              | Legt die Schwierigkeitsstufe auf eine vordefinierte Einstellung fest                                                                                            |
| `addgas`             |     False    | Oxygen, Nitrogen, CarbonDioxide, Volatiles, Pollutant, Water, NitrousOxide | Fügt dem Zielobjekt eine Gasart in einer bestimmten Menge und Temperatur hinzu                                                                                  |
| `legacycpu`          |     False    | enable, disable                                                            | Aktiviert den Legacy-CPU-Modus. Empfohlen für Nutzer mit CPUs unterhalb der empfohlenen Spezifikation                                                           |
| `test`               |     False    |                                                                            | Testet alle Regenbogenfarben                                                                                                                                    |
| `autosavecancel`     |     False    |                                                                            | Startet einen Autosave-Prozess und stoppt das Spiel sofort                                                                                                      |
