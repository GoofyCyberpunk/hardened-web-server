# hardened-web-server
Hardened Ubuntu Server setup with SSH key auth, firewall, and Fail2ban

Projekt in Arbeit - Ziel: ein Ubuntu-Server aufsetzten und absichern.

- [ ] SSH-Key-Auth einrichten, Passwort-Login deaktivieren
- [ ] Firewall (ufw) konfigurieren
- [ ] Fail2ban installieren und konfigurieren
- [ ] nginx installieren

## Notizen
(folgt während der Arbeit)

Virtual box heruntergeladen
ubuntu-26.04-live-server-amd64.iso heruntergeladen für die VM

## VM Setup — Ubuntu Server (VirtualBox)

- VirtualBox installiert via `sudo apt install virtualbox`
- Neue VM erstellt: Name `ubuntu-server-webserver`, Typ Linux/Ubuntu 64-bit
- Ressourcen: 2048 MB RAM, 2 CPU-Kerne, 20 GB dynamische Festplatte
- Netzwerkadapter: erst auf NAT (Standard), zeigte falsche IP (10.0.2.15) 
  → korrigiert auf "Bridged Adapter", damit VM eine echte IP im Heimnetzwerk bekommt
- Ubuntu Server ISO (26.04 LTS) heruntergeladen von ubuntu.com/download/server
- Installation durchlaufen:
  - Sprache: Englisch
  - Tastaturlayout: German
  - Netzwerk: automatisch per DHCP, IP jetzt korrekt im Heimnetz-Bereich
  - Festplatte: "Use entire disk" (gesamte virtuelle Platte)
  - Profil erstellt: Hostname `webserver-lab`, eigener Benutzername + Passwort vergeben
  - Ubuntu Pro Upgrade: übersprungen (nicht benötigt für Lab-Projekt)
  - OpenSSH Server: aktiviert (wichtig für späteren SSH-Zugriff)
  - SSH-Key-Import beim Setup: übersprungen, wird manuell nachträglich eingerichtet
  - Populäre Snaps: keine ausgewählt, Software wird gezielt später über apt installiert
- Installation abgeschlossen, Reboot durchgeführt
- Erster Login erfolgreich, System läuft
- IP-Adresse der VM geprüft mit `ip a` (Interface enp0s3)

## SSH Hardening

- SSH-Key-Paar erzeugt (ed25519) für VM-Zugriff, separat vom GitHub-Key
- Public Key via `ssh-copy-id` auf die VM übertragen
- Key-Login erfolgreich getestet
- Passwort-Login in /etc/ssh/sshd_config auf "no" gesetzt
- Problem: Änderung griff zunächst nicht — Ursache war eine zusätzliche 
  Config-Datei unter /etc/ssh/sshd_config.d/, die PasswordAuthentication yes 
  erzwang und die Haupt-Config überschrieb
- Fix: Wert auch in dieser Datei auf "no" geändert, SSH-Dienst neu gestartet
- Verifiziert: Verbindungsversuch mit `-o PubkeyAuthentication=no` wird jetzt 
  korrekt mit "Permission denied (publickey)" abgelehnt