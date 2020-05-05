<!-- soft hyphen &shy; -->
# Secure Shell

Als Secure Shell werden sowohl die unterlagerten Protokolle, als auch die Programme die zusammen die Funktionalität der Secure Shell darstellen bezeichnet. Sie ermöglichen den verschlüsselten Zugang auf die Kommandozeile entfernter Rechner und die Möglichkeit des ebenfalls verschlüsselten Datentransfers.
Authen&shy;ti&shy;fizierung des Nutzers kann über verschiedene Wege geschehen, u. a. durch die Eingabe von Benutzer&shy;name und Passwort oder durch das Public-Key Verfahren. Letzteres bietet neben einer sicheren Einwahl ebenfalls den Komfort der passwortlosen Einwahl. Im weiteren wird die Ein&shy;richtung und Nutzung des Public-Key Verfahrens beschrieben.
Um eine passwortlose Einwahl mit Hilfe von Public-Keys zu ermöglichen müssen folgende Schritte durchlaufen werden. Erstens wird das Schlüssel&shy;paar (Private-Key und Public-Key) erzeugt. Zweitens wird der Public-Key auf den entfernten Rechner übertragen und in der dortigen SSH-Konfigurationsdatei eingetragen. Im Anschluss werden die Zugriffsrechte der Konfigurationsdatei angepasst und die Verbindung über&shy;prüft. Es ist darauf zu achten, dass der Private-Key mittels Zugriffsrechten versehen und vor fremdem Zugriff geschützt werden muss, während der Public-Key frei verteilt werden darf. Der Private-Key wird nie übertragen, er verbleibt auf dem Rechner von dem aus die Einwahl initiiert wird. Es ist ratsam den Dateinamen zu definieren. Zum einen sorgt dies für eine einfache Zu&shy;ordnung von Schlüsseldateien zu Verbindungspartnern und zum anderen wird damit vermieden versehentlich falsche Schlüssel zu übertragen. Das Übertragen des Public-Keys auf den entfernten Rechner. Dabei wird der öffentliche Schlüssel des Nutzers in die Datei `~/.ssh/authorized_keys` kopiert bzw. angehängt. Der Befehl `ssh-copy-id` ist eine ausführbare Datei, sondern ein Shell Skript. Er steht somit nur unter Linux zur Verfügung. Die Namenskonvention von OpenSSH versieht den öffentlichen Schlüssel mit einer Endung, den privaten jedoch nicht (public `file.pub`, privat `file`). Hier eine Beispielsitzung auf dem lokalen Rechner:  
Erzeugen eines neuen Verzeichnisses:  
`$ cd ~ && mkdir .ssh && cd $_`  
Schlüsselpaar (Private-Key und Public-Key) erzeugen:  
`$ ssh-keygen -f key`  
Kopieren des Public-Key auf den entfernten Rechner:  
`$ ssh-copy-id -i ~/.ssh/key.pub user@host.org`  
Erzeugen einer Konfigurationsdatei:  
`$ touch config`  
Setzen der Rechte der Konfigurationsdatei:  
`$ chmod 600 config`  

## Public-Key Schlüsselformate
Mit Hilfe eines Schlüsselspaars lässt sich die Einwahl auf mehrere entfernte Rechner realisieren. Es ist jedoch ratsam für jeden entfernten Rechner ein eigenes Schlüsselpaar anzulegen um ein das entfernen und hinzufügen einzelner Schlüssels zu ermöglichen. Es existieren zwei verbreitete Formate für Schlüssel. Zum einen das von Tectia übernommene SSH2-Format (wird von PuTTY genutzt) und das OpenSSH-Format. Soll exemplarisch ein Schlüssel der unter PuTTY erzeugt wurde auf einem Linux-Rechner verwendet werden, so muss zuvor vom  SSH2-Format in das OpenSSH-Format gewandelt werden.  
Wandlung vom SSH2-Format in OpenSSH-Format:  
`$ ssh-keygen -i -f ssh2.pub > openssh.pub`  
Wandlung vom OpenSSH-Format in SSH2-Format:  
`$ ssh-keygen -e -f openssh.pub > ssh2.pub`  

## Schnellzugriff per Konsole
Um einen Schnellzugriff auf SSH-Verbindungen aus der Konsole heraus zu ermöglichen sind folgende Schritte erforderlich. Erstens Anlegen der Datei `~/.ssh/config`. Zweitens kopieren des nebenstehenden Inhalts in diese Datei und der Einstellungen. Danach lässt sich eine OpenSSH-&shy;Verbindung in der Konsole über die Eingabe von `$ ssh shortcut` aufbauen.
```
### ~/.ssh/config ###
Host shortcut
  HostName host.net
  Compression yes
  User user
  IdentityFile ~/.ssh/prk_file
```
## Wahl des Verschlüsselungsverfahrens
Die Auswahl spezifischer Verschlüsselungsverfahren geschieht über den Schalter `-c`. Bei der Auswahl eines nicht unterstützten Verfahrens wird eine Liste der unterstützten Verfahren ausgegeben: `$ ssh -c aes128-ctr user@host`
Die Ausgabe der unterstützten Verschlüsselungsverfahren (seit Version v6.3) über: `$ ssh -Q cipher`. Zur Fehlersuche lassen sich detaillierte Informationen ausgeben über: `$ ssh -v`. Ausgabe der OpenSSH-Version per: `$ ssh -V`.

## SSH-Alternative für Verbindungen über roaming
Die mosh (mobile shell) ermöglicht roaming und handover von bestehenden Verbindungen. Somit ist es möglich eine Einwahl zwischen verschiedenen Verbindungsarten (GSM, LTE, WLAN) und der Nutzung des Ruhemodus des Rechners ohne Verbindungsabbruch zu nutzen. Weiterhin ist der im Vergleich zu SSH niedrigere Bandbreitenbedarf und eine verbesserte Latenz zu nennen. Dies ist möglich, da mosh UDP (connection-less) anstelle von TCP (connection-oriented) verwendet, dadurch entfällt das Handshake. Des weiteren verwendet mosh ein lokales Echo (Eingaben werden soweit möglich lokal dargestellt ohne übertragen zu werden) und es werden Verfahren genutzt, die ähnlich zu diff und patch arbeiten. Daher muss nicht der gesamt Bildschirminhalt bei einer Änderung übertragen werden.

## Einstellungen für PuTTY
Start von PuTTY mit einer gespeicherten Session:  
`putty.exe -load "Session Name"`  
Start von Pageant mit privaten Schlüsseln:  
`pageant.exe C:\private1.ppk C:\private2.ppk`
