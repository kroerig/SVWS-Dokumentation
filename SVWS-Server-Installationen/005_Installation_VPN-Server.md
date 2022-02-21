# Installation des Wireguard-Servers

## Vorbereitungen

### Auswahl der Maschine 

Es gibt mehrer M�glichkeiten wo ein Wireguard-VPN-Server betrieben werden kann:

- in einer Virtualisierungsumgebung (Proxmox, ESXi, ...) 
- im Containersystem (Docker, LXH, ...)
- direkt auf echter Hardware (Raspberry, PC, Server ...)
- im Router (Fritzbox, ...)

### Beipiele f�r Hardwarel�sungen:

Fritzbox: <br>
https://www.computerbild.de/artikel/cb-News-Software-FritzOS-7.39-AVM-integriert-WireGuard-VPN-FritzBox-31230305.html

Shellfire: <br>
https://www.shellfire.de/blog/shellfire-box-4k-wireguard-vpn-router-power/

OpenSense: <br>
https://www.ovpn.com/de/guides/wireguard/opnsense





Im Weitern wird als Basis ein LXH Container auf einem Proxmoxsystem verwendet. Die Installation unterscheidet sich im Wesentlichen nicht von anderen Installationen 


### Installation des Debian 11 System

download debian 11 net install: <br>
https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.1.0-amd64-netinst.iso

Eine typische Debian Installation ausf�hren - einfach der Menuef�hrung folgen und je nach Bedarf auch auf das grafische Frontend verzichten. 
Den SSH-Server direkt mit installieren, es wird in der Regel der SSH Zugang f�r die Wartung ben�tigt.

### Vorbereitung der Installation beim Proxmox-Container

Dies ist nur beim LXH Container unter Proxmox n�tig. Wenn der Container aufgesetzt ist, dann muss auf dem Muttersystem noch die config datei des Containers 
noch um das Netzwer-Tunneldevice erg�nzt werden: 
 
		nano /etc/pve/lxc/NUMER_DES_CONTAINERS.conf

Die folgenden Eintr�ge erg�nzen: 

		lxc.cgroup2.devices.allow: c 10:200 rwm
		lxc.mount.entry: /dev/net dev/net none bind,create=dir
		
und die Rechte auf der Netzwerkschnittstelle erg�nzen: 

		 chown 100000:100000 /dev/net/tun

OKAY: Ich bekomme es nicht hin !

### ggf aktualisieren

Bei einem frisch installierten System sollte dieser Schritt nicht n�tig sein, 
man kann es aber trotzdem einmal ausf�hren, um auch die letzten Aktualisierungen geladen zu haben: 

		apt update
		apt upgrade -y

## Installation von Wireguard per Script 

download: 
	


Als Port kann auch irgendein Port in den H�heren Bereichen gew�



---------------------------------------------

OLD Stuff:


## Installation in einer VM

download debian 11 net install: <br>
https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.1.0-amd64-netinst.iso

Typische Debian installation - einfach der Menuef�hrung folgen und ggf kein grafisches Frontend ausw�hlen. 
Es wird nicht unbedingt ben�tigt. Anschlie�end die folgenden Befehle ausf�hren:

		
		# im Bedarfsfall die Sources erweitern:
		# sh -c "echo 'deb http://deb.debian.org/debian buster-backports main contrib non-free' > /etc/apt/sources.list.d/buster-backports.list"
		
		# wireguard installieren:
		su - 
		apt update
		apt upgrade
		apt install linux-headers-$(uname --kernel-release)
		apt install wireguard iptables net-tools

		# ip_forward einrichten einfach durch dranh�ngen an die sysctl.conf 
		# alternativ und sch�ner per nano die flags in der sysctl.conf suchen und auf 1 setzen
		echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
		echo "net.ipv6.conf.all.ip_forward=1" >> /etc/sysctl.conf
		
		# checken: l�uft das Portforwarding?
		sysctl -p

		# Schl�sselerstellen:
		cd /etc/wireguard/
		umask 077; wg genkey | tee privatekey | wg pubkey > publickey
		chmod 600 /etc/wireguard/privatekey
		
		# checken: habe ich die beiden Schl�ssel?
		cat privatekey
		cat publickey
		
		
Ein Schl�sselpaar f�r einen Client, hier z.B. Client1, kann dann wie folgt erzeugt werden: 
		wg genkey | tee client1_private.key | wg pubkey > client1_public.key
		mv client1_private.key anywhere_the_Client_whats_to_egt_ist
		
Dies kann auch auf einem Clientsystem ausgef�hrt werden - dann m�sste aber der andere client1_public.key auf den Server copiert werden.


Nun wird das Wireguard-Netzwerkinterface angelegt dazu eine Datein wg0.conf erstellen: 

		nano /etc/wireguard/wg0.conf

Inhalt dieser Datei soll sie folgt aussehen: 
		[Interface]
		Address = 192.168.211.1/24
		ListenPort = 51820

		PrivateKey = <Server Private-Key>
		PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
		PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

		# Client1
		[Peer]
		PublicKey = <Client Public-Key>
		AllowedIPs = 192.168.211.2/32

Hier muss eth0 an die entsprechende Netzwerkschnittstelle, die per �iconfig� abgelesen werden kann, angepasst werden. 
Das Wireguard-Netz 192.168.211.XYZ kann frei im privaten Adressbereich gew�hlt werden. Z.B. auch 10.10.10.XYZ 
Ebenso k�nnen auch diverse Subnetze gedacht werden, um dort Berechtigungen zusetzen ... 



## einfaches Aufsetzen per Script von Angristan


https://awesomeopensource.com/project/angristan/wireguard-install


		curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
		chmod +x wireguard-install.sh
		./wireguard-install.sh




## Quellen: 

https://github.com/angristan/wireguard-install 

https://www.bennetrichter.de/anleitungen/wireguard-linux/

https://www.bitblokes.de/wireguard-vpn-server-einrichten-ubuntu-raspberry-pi-linux-android/

https://github.com/angristan/wireguard-install/blob/88ae1c0d0f5d4f48252fd3be27b2f122e40edf83/wireguard-install.sh

https://blog.peterge.de/wireguard-lxc-unter-proxmox/

https://www.youtube.com/watch?v=D1zp6n7ushM