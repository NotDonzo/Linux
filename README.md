# Linux
##. DOE ERMEE WAT JE WILT

1. GEBRUIKERS & GROEPEN
Gebruiker aanmaken
useradd -m student
passwd student
- -m maakt een home directory aan.
- Daarna stel je een wachtwoord in.
Groep aanmaken
groupadd ictgroep
Gebruiker aan groep toevoegen
usermod -g ictgroep student # Standaardgroep
usermod -aG vboxusers student # Extra groep (bijv. VirtualBox)
Daarna opnieuw inloggen of herstarten!
2. NAVIGATIE EN BESTANDSBEHEER
Navigatie
cd mapnaam # Naar map
cd ~ # Naar home
pwd # Huidige locatie tonen
Bestanden maken & bekijken
echo 'dit is een voorbeeld' > nieuwbestand
cat nieuwbestand
Verwijderen
rm bestand
rm -d map # Lege map verwijderen
rm -r map # Map + inhoud verwijderen
rm -i bestand # Vraagt bevestiging
Kopiëren
cp bestand1 bestand2
cp -i bestand1 bestand2 # Vraagt bevestiging
cp -n bestand1 bestand2 # Overschrijft niet
3. BESTANDSRECHTEN & EIGENAARSCHAP
Symbolische rechten
- r = 4 → lezen
- w = 2 → schrijven
- x = 1 → uitvoeren
Voorbeeld: chmod 770 map betekent
- Eigenaar: lezen, schrijven, uitvoeren
- Groep: lezen, schrijven, uitvoeren
- Anderen: geen rechten
Veelgebruikte rechten
Rechten | Uitleg
777 | Iedereen alles (onveilig)
770 | Eigenaar + groep alles
755 | Eigenaar alles, anderen lezen/x
700 | Alleen eigenaar
644 | Eigenaar schrijven, rest lezen
Rechten en eigenaarschap instellen
chmod 770 /home/student/project
chgrp ictgroep /home/student/project
chown student:ictgroep /home/student/project
chmod g+s /home/student/project # Nieuwe bestanden krijgen juiste groep
ls -ld /home/student/project # Controleer rechten
4. SOFTWARE INSTALLATIE (Zypper & RPM)
Zypper (OpenSUSE)
zypper install pakketnaam
zypper update
zypper search samba
RPM
rpm -i pakket.rpm # Installeren
rpm -U pakket.rpm # Updaten
rpm -e pakketnaam # Verwijderen
rpm -vh pakket.rpm # Met voortgang
5. RUNLEVELS (TARGETS)
Doel | Commando
Grafische omgeving (runlevel 5) | graphical.target
Multi-user mode (3) | multi-user.target
Uitzetten (0) | poweroff / halt.target
Herstarten (6) | reboot.target
Single-user mode (1) | rescue.target
6. SAMBA (Bestanden delen met Windows)
Share aanmaken
vi /etc/samba/smb.conf
[projectmap]
path = /samba/projectmap
valid users = @ictgroep
read only = no
write list = @ictgroep
browseable = yes
Voorbereiden
mkdir -p /samba/projectmap
chown root:ictgroep /samba/projectmap
chmod 770 /samba/projectmap
Samba gebruiker toevoegen
smbpasswd -a student
smbpasswd -e student
Service beheren
systemctl start smb.service
systemctl enable smb.service
systemctl status smb.service
systemctl restart smb.service
Testen via Windows: Verkenner → Deze pc → 3 puntjes → Netwerkverbinding maken
Typ: \192.168.72.133\projectmap
7. FTP SERVER
Installatie en opstarten
zypper install vsftpd
systemctl start vsftpd
systemctl enable vsftpd
Bestanden delen
mkdir -p /srv/ftp
vi /srv/ftp/testbestand
Eigen map per gebruiker
mkdir -p /home/student/ftp/uploads
chown nobody:nogroup /home/student/ftp
chown student:student /home/student/ftp/uploads
Configuratie /etc/vsftpd.conf
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
local_root=/home/$USER/ftp
Testen via FileZilla: Host: IP-server, Gebruikersnaam + wachtwoord, Upload/download in map
uploads
8. WEBSERVER (Apache2)
Installeren en starten
zypper install apache2
systemctl start apache2
systemctl enable apache2
Index.html maken
<HTML>
 <HEAD></HEAD>
 <BODY>
 Dit is de website van student
 </BODY>
</HTML>
Bestand plaatsen
mv index.html /srv/www/htdocs
Eigen vhost maken
cp /etc/apache2/vhosts.d/vhost.template /etc/apache2/vhosts.d/student.conf
vi student.conf
# Vervang dummy-host.example.com door student.test
Configuratie testen
apache2ctl configtest
9. NFS (Linux <-> Linux bestandsdeling)
Op de server
zypper install nfs
mkdir -p /srv/nfs/delen
chown nobody:nogroup /srv/nfs/delen
vi /etc/exports
/srv/nfs/delen 192.168.1.0/24(rw,sync,no_subtree_check)
exportfs -a
systemctl restart nfsserver.service
Op de client
zypper install nfs
mkdir -p /mnt/nfs/delen
mount 192.168.1.10:/srv/nfs/delen /mnt/nfs/delen
10. CRONTAB & SCRIPTS
Crontab instellen
crontab -e
12 18 * * 0 /backup.sh # Elke zondag om 18:12
Uitleg velden:
minuut, uur, dag, maand, dag-van-week
Bijv. */1 * * * * = elke minuut
Script maken (voorbeeld)
vi backup.sh
#!/bin/bash
echo "Back-up wordt gemaakt" >> /var/log/backup.log
Script uitvoerbaar maken:
chmod +x backup.sh
./backup.sh
