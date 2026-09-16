Hiermit lässt sich Text in Bildern speichern und mit einem Passwort schützen. Für ein verlustfreies Speichern wird das PNG-Format empfohlen.

Ich persönlich war nur für denn Input/Output zuständig, daher kenne ich mich mit der Funktion des Codes nicht genau aus. Da ich trotz erklärung es nicht verstand habe, ist hier eine Erklärung von KI:

1. beim encoden (--encode)
-------------------------------------------------------------------
- PASSWORT-SCHUTZ (OPTIONAL):
  Wird --password übergeben, erzeugt der Code über PBKDF2 (100.000 
  Iterationen + Salt) einen AES-Key und setzt ein Flag (0x01).
  Ohne Passwort wird ein zufälliger Key erzeugt und Flag 0x00 gesetzt.

- SCHLÜSSEL-AUFTEILUNG (SHAMIR'S SECRET SHARING):
  Der AES-Schlüssel wird mathematisch in 5 voneinander unabhängige
  Teile (Shares) zerlegt. Man benötigt später mindestens 3 Teile,
  um den Schlüssel wiederherzustellen (N=5, K=3).

- FEHLERSCHUTZ & INTEGRITÄT:
  Der Ciphertext erhält eine SHA-256 Prüfsumme sowie eine 3-fache
  Bit-Verdopplung (Redundanz), um spätere Beschädigungen abzufangen.

- STEGANOGRAPHIE (LSB-METHODE):
  In jedem der 5 PNG-Bilder wird jeweils 1 Key-Teil zusammen mit den
  verschlüsselten Daten versteckt. Dabei wird nur das niederwertigste
  Bit (LSB) der Farbkanäle angepasst – das Bild bleibt optisch exakt
  identisch.


2. BEIM DECODEN (--decode)
-------------------------------------------------------------------
- BIT-EXTRAKTION:
  Aus mindestens 3 Bildern werden die versteckten LSB-Bits ausgelesen.
  Über die Redundanz werden eventuelle Lese-Fehler korrigiert und die
  SHA-256 Prüfsumme verifiziert.

- SCHLÜSSEL-REKONSTRUKTION:
  Aus den 3 ausgelesenen Teilen wird der ursprüngliche AES-Schlüssel
  mathematisch wieder zusammengesetzt.

- AUTOMATISCHE PASSWORT-ERKENNUNG:
  Das Skript prüft das Flag in den Daten:
  * Flag 0x00: Entschlüsselt die Daten direkt mit dem Shamir-Key.
  * Flag 0x01: Erkennt Passwortschutz. Fehlt der Parameter --password,
               erscheint eine interaktive Passwortabfrage.

- AUSGABE:
  Der entschlüsselte Text wird in der Konsole ausgegeben und bei
  Verwendung von --save zusätzlich als .txt-Datei gespeichert.
