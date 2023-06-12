**Apache installeren en configureren**
1.	Open konsole
2.	Log in met root account “su”
3.	zypper install apache2
4.	vi /srv/www/htdocs/index.html
voeg een tekst in dit bestand, bijvoorbeeld “Ik ben Peter”

sla dit document op “ESC daarna :wq!”

5.	vi /etc/hosts
onder 127.0.0.1 voeg het ip adres van de Linux machine en druk op Tab en daarna naam van de website.

192.168.27.135		mijnwebsite.test
 

6.	systemctl restart apache2.service
7.	open Firefox en typ mijnwebsite.test
 
 
**** Handleiding Samba server  opdracht 1 ** **
Machine1 Linux
1.	Open konsole
2.	su       “daarna wachwoord van de root”
3.	cd /home
4.	mkdir Administratie
5.	mkdir Verkoop
6.	groupadd administratie
7.	groupadd verkoop
8.	chgrp administratie Administratie
9.	chgrp verkoop Verkoop
10.	chmod 070 Administratie
11.	chmod 070 Verkoop
12.	useradd cora
13.	passwd cora               “geef twee keer Linux wachtwoord, bijvoorbeeld Welkom01 en Welkom01”
14.	smbpasswd -a cora     “geef twee keer samba wachtwoord, bijvoorbeeld Welkom01 en Wekom1 
15.	useradd jan
16.	passwd jan                “geef twee keer Linux wachtwoord, bijvoorbeeld Welkom01 en Welkom01”
17.	smbpasswd -a jan     “geef twee keer samba wachtwoord, bijvoorbeeld Welkom01 en Wekom1 
18.	usermod -aG administratie cora
19.	usermod -aG verkoop jan
20.	systemctl start smb.service
21.	systemctl stop firewalld.service
22.	firewall-cmd --add-service=samba --permanent  "Samba service permanent toevoegen aan firewall zodat de services niet meer geblokkeerd worden"
firewall-cmd --reload

uitzondering samba server: 
setfacl -m g:groupB:r-x /home/samba/Test      "Toegang verlenen aan bepaalde groep tot bepaalde map"

setfacl -m o::--- /home/samba/Test      "Geen toegang voor other"

23.	vi /etc/samba/smb.conf
voeg de volgende regels in configuratiebestand:
[Administratie]
                   path = /home/Administratie
                   writeable = yes
[Verkoop]
                   path = /home/Verkoop
                   writeable = yes

sla dit op “ESC daarna :wq! En druk op enter”
23.	systemctl restart smb.service
24.	ip address   “kopier het ip-adres (ip-adres begint altijd met 172. of 192. of 10.)

**Handleiding Samba server  opdracht 2**
Bij opdracht mogen de gebruikers van de beide groepen de data van elkaar niet zien maar nu mogen ze wel de data van elkaar allen lezen “read en excute”.  
Machine1 Linux
1.	Open konsole
2.	su       “daarna wachwoord van de root”
3.	cd /home
4.	vi /etc/samba/smb.conf   “hier moeten nog twee regels toegevoegd worden”

[Administratie]
                   path = /home/Administratie
                   writeable = yes
                   valid user = @administratie,@verkoop
[Verkoop]
                   path = /home/Verkoop
                   writeable = yes
                   valid user = @verkoop,@administratie

sla dit op “ESC daarna :wq! En druk op enter”
5.	setfaclt -m g:administratie:r-x Verkoop
6.	setfacl -m g:verkoop:r-x Administratie
7.	systemctl restart smb.service
Nu kan opnieuw verbinding maken met de shares en dan testen of de gebruiker van Administratie les toegang heeft tot de share Verkoop.

**backup maken**
1.	Open konsole
2.	Log in met root account
3.	cd /home
4.	mkdir backup
5.	vi backup.sh
rsync -av /home/Administratie /home/backup
rsync -av /home/Verkoop /home/backup
rsync -av /home /home/backup

sla dit bestand op met ESC daarna :wq!

6.	chmod +x backup.sh
7.	crontab -e
0 0 * * * /home/backup.sh >> /home/logfiles

sla dit ook op met ESC :wq!

8.	om crontab te cotroleren typ je crontab -l
9.	om backup.sh te controleren typ je ./backup.sh

