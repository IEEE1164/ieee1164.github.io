<!-- soft hyphen &shy; -->
# Secure Shell

Als Secure Shell werden sowohl die unter&shy;lagerten Protokolle, als auch die Programme die zusammen die Funktionalität der Secure Shell darstellen bezeichnet. Sie er&shy;möglichen den verschlüsselten Zugang zur Kommando&shy;zeile entfernter Rechner und den ebenfalls ver&shy;schlüsselten Daten&shy;transfer. Secure Shell wurde ursprünglich von dem Finnen Tatu Ylönen entwickelt. Dessen kommerzielle Software von der Firma Tectia entwickelt wird. OpenSSH ist eine weit verbreitete quelloffene Variante die auch in diesem Dokument genutzt wird. Nennenswert ist noch die Secure Shell Variante namens Dropbear, deren Fokus auf ein&shy;gebetteten Systemen liegt.

Die Authen&shy;ti&shy;fizierung des Nutzers kann über verschiedene Wege geschehen, u. a. durch die Eingabe von Benutzer&shy;name und Passwort oder durch das Public-Key Verfahren. Letzteres bietet neben einer sicheren Einwahl ebenfalls den Komfort der passwortlosen Einwahl. Im weiteren wird die Ein&shy;richtung und Nutzung des Public-Key Verfahrens beschrieben.
Um eine passwortlose Einwahl mit Hilfe von Public-Keys zu ermöglichen müssen folgende Schritte durch&shy;laufen werden.

1. Erzeugung des Schlüssel&shy;paars (Private-Key und Public-Key).
1. Übertragen des Public-Keys auf den entfernten Rechner (eintragen in die SSH-Konfigurations&shy;datei).
1. Anpassen der Zugriffsrechte für die Konfigurationsdatei.
1. Testen der Verbindung. 

Es ist darauf zu achten, dass der Private-Key mittels Zugriffsrechten versehen und vor fremdem Zugriff geschützt werden muss. Der Public-Key kann und muss jedoch frei verteilt werden. Es ist ratsam den Datei&shy;namen zu definieren. Zum einen sorgt dies für eine einfache Zu&shy;ordnung von Schlüsseldateien zu Verbindungs&shy;partnern und zum anderen wird damit vermieden versehentlich falsche Schlüssel zu übertragen. Das Übertragen des Public-Keys auf den entfernten Rechner. Dabei wird der öffentliche Schlüssel des Nutzers in die Datei `~/.ssh/authorized_keys` kopiert bzw. angehängt. Der Befehl `ssh-copy-id` ist eine ausführbare Datei, sondern ein Shell Skript. Er steht somit nur unter Linux zur Verfügung. Die Namens&shy;konvention von OpenSSH versieht den öffentlichen Schlüssel mit einer Endung, den privaten jedoch nicht (public `file.pub`, privat `file`). 

> Der Private-Key wird nie übertragen, er verbleibt auf dem Rechner von dem aus die Einwahl initiiert wird.

Hier eine Beispielsitzung auf dem lokalen Rechner inklusive des erstellens der Konfigurations&shy;datei für die Nutzung des Schnell&shy;zugriffs:   
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

## Public-Key Schlüsselformate
Mit Hilfe eines Schlüsselspaars lässt sich die Einwahl auf mehrere entfernte Rechner realisieren. Es ist jedoch ratsam für jeden entfernten Rechner ein eigenes Schlüsselpaar anzulegen um ein das entfernen und hinzu&shy;fügen einzelner Schlüssels zu ermöglichen. Es existieren zwei verbreitete Formate für Schlüssel. Zum einen das von Tectia über&shy;nommene SSH2-Format (wird von PuTTY genutzt) und das OpenSSH-Format. Soll exemplarisch ein Schlüssel der unter PuTTY erzeugt wurde auf einem Linux-Rechner verwendet werden, so muss zuvor vom  SSH2-Format in das OpenSSH-Format ge&shy;wandelt werden.  
Wandlung vom SSH2-Format in OpenSSH-Format:  
`$ ssh-keygen -i -f ssh2.pub > openssh.pub`  
Wandlung vom OpenSSH-Format in SSH2-Format:  
`$ ssh-keygen -e -f openssh.pub > ssh2.pub`  

## Wahl des Verschlüsselungsverfahrens
Die Auswahl spezifischer Verschlüsselungs&shy;verfahren geschieht über den Schalter `-c`. Bei der Auswahl eines nicht unterstützten Verfahrens wird eine Liste der unter&shy;stützten Verfahren ausgegeben: `$ ssh -c aes128-ctr user@host`
Die Ausgabe der unterstützten Verschlüsselungs&shy;verfahren (seit Version v6.3) über: `$ ssh -Q cipher`. Zur Fehlersuche lassen sich detaillierte Informationen ausgeben über: `$ ssh -v`. Aus&shy;gabe der OpenSSH-Version per: `$ ssh -V`.

## SSH-Alternative für Verbindungen über roaming
Die mosh (mobile shell) ermöglicht roaming und handover von bestehenden Verbind&shy;ungen. Somit ist es möglich eine Einwahl zwischen verschiedenen Verbind&shy;ungs&shy;arten (GSM, LTE, WLAN) und der Nutzung des Ruhemodus des Rechners ohne Verbindungsabbruch zu nutzen. Weiterhin ist der im Vergleich zu SSH niedrigere Bandbreitenbedarf und eine verbesserte Latenz zu nennen. Dies ist möglich, da mosh UDP (connection-less) anstelle von TCP (connection-oriented) verwendet, dadurch entfällt das Handshake. Des weiteren verwendet mosh ein lokales Echo (Eingaben werden soweit möglich lokal dargestellt ohne übertragen zu werden) und es werden Verfahren genutzt, die ähnlich zu diff und patch arbeiten. Daher muss nicht der gesamt Bildschirminhalt bei einer Änderung übertragen werden.

## Einstellungen für PuTTY
Start von PuTTY mit einer gespeicherten Session:  
`putty.exe -load "Session Name"`  
Start von Pageant mit privaten Schlüsseln:  
`pageant.exe C:\private1.ppk C:\private2.ppk`
