# Kali Linux Setup Cheatsheet

```
**Updates:**
1.0 Initial Copy
1.1 Added HexChat installation command
1.2 Creating a normal user with sudo permissions
1.3 Tidying, added detail of Chromium and wmap
1.4 Made into an .md file
.: You can find me on Twitter @ulicbelouve :.
```

## Download, Install, and Credits

Go and [download the Kali Linux distro](http://www.kali.org/downloads/)

I highly recommend you [download the VMware image](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) and download Virtual Player/VirtualBox. Remember that it will be a gz-compressed and tar archived file, so make sure to extract them first and load the vmx file.

A lot of info is derived from [The Hacker Playbook 2](http://www.amazon.com/dp/1512214566/). I have a signed copy, and I intend to get a non-signed copy to bang around and make notes in.

Installation and configuration for Kali Linux is not really "plug and play"...so here are a bunch of config notes.

For your VM, you may need some extra tools.

●	[Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads)

## Once Your Kali VM is Up and Running

```
●	Log in with the username root and the default password toor
●	Open a terminal
●	Change the password
	○	passwd
●	Update the image OR SUPERUPDATE
	○	apt-get update
	○	apt-get dist-upgrade
●	Superupdate
	○	Line to copy-paste if you want to drop it in a terminal:
	○	apt-get update && apt-get -y upgrade && apt-get -y dist-upgrade && apt-get -y autoremove && apt-get -y autoclean
●	Setup Metasploit database
	○	service postgresql start (or, systemctl start postgresql)
●	Make postgresql database start on boot
	○	update-rc.d postgresql enable
●	Start and stop the Metasploit service (this will setup the database.yml file for you)
	○	Try this: msfdb init
		msfconsole
		msf > db_status	
●	Install gedit
	○	apt-get install gedit (Response of 'already latest version')
●	Configure gedit
	○	Preferences
	○	View: Display line numbers, Highlight current line
	○	Editor: Tab width 5, Insert spaces instead of tabs
	○	Color Scheme: Oblivion
●	After this I did a apt autoremove as cleanup
```


## Change the hostname
Many network admins look for systems named Kali in logs like DHCP. It is best to follow the naming standard used by the company you are testing
```
	○	gedit /etc/hostname
		■	Change the hostname (replace kali) and save
		■	I like to rename it loki, the trickster.  Or REDSHIRT, for a test box.  "REDSHIRT is down" is a great error message.
	○	gedit /etc/hosts
		■	Change the hostname (replace kali) and save
	○	reboot
```

### Optional for Metasploit - Enable Logging
○	I list this as optional since logs get pretty big, but you have the ability to log every command and result from Metasploit’s Command Line Interface (CLI). This becomes very useful for bulk attack/queries or if your client requires these logs. *If this is a fresh image, type msfconsole first and exit before configuring logging to create the .msf4 folder.
```	
●	From a command prompt, type:
	○	echo “spool /root/msf_console.log” > /root/.msf4/msfconsole.rc	
●	Logs will be stored at /root/msf_console.log
```
Now may be a good time to setup a new normal user to be able to use sudo, and not run everything as root

See here: https://github.com/belouve/kalicheatsheet/blob/master/setup.md#create-a-new-normal-user-with-sudo-permission-in-kali-linux

## Tool Installation

### The Backdoor Factory:
○	Patch PE, ELF, Mach-O binaries with shellcode.
```
●	git clone https://github.com/secretsquirrel/the-backdoor-factory /opt/the-backdoor-factory
●	cd /opt/the-backdoor-factory
●	./install.sh
```

### HTTPScreenShot
○	HTTPScreenshot is a tool for grabbing screenshots and HTML of large numbers of websites.
```
●	pip install selenium  (in latest run: installed.  Previously: requirement already satisfied in python2.7)
●	git clone https://github.com/breenmachine/httpscreenshot.git /opt/httpscreenshot
●	cd /opt/httpscreenshot
●	chmod +x install-dependencies.sh && ./install-dependencies.sh
●	HTTPScreenShot only works if you are running on a 64-bit Kali by default. If you are running 32-bit PAE, install i686 phatomjs as follows:
	○	wget https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-1.9.8-linux-i686.tar.bz2
	○	bzip2 -d phantomjs-1.9.8-linux-i686.tar.bz2
	○	tar xvf phantomjs-1.9.8-linux-i686.tar
	○	cp phantomjs-1.9.8-linux-i686/bin/phantomjs /usr/bin/
```

### SMBExec
○	A rapid psexec style attack with samba tools.
```
●	git clone https://github.com/pentestgeek/smbexec.git /opt/smbexec
●	cd /opt/smbexec && ./install.sh
●	Select 1 - Debian/Ubuntu and derivatives
●	Select all defaults
●	./install.sh
●	Select 4 to compile smbexec binaries
	Most recent run was fine, Previously: generated error, directed to check prerequisites
●	After compilation, select 5 to exit
```

### Masscan
○	This is the fastest Internet port scanner. It can scan the entire Internet in under six minutes.
```
●	apt-get install git gcc make libpcap-dev
●	git clone https://github.com/robertdavidgraham/masscan.git /opt/masscan
●	cd /opt/masscan
●	make
●	make install
```

### Gitrob
○	Reconnaissance tool for GitHub organizations
```
●	git clone https://github.com/michenriksen/gitrob.git /opt/gitrob
●	cd /opt/gitrob
●	gem install bundler
●	service postgresql start
●	su postgres
●	createuser -s gitrob --pwprompt
	set a default password, 'gitrob' if you like the root/toor combo
●	createdb -O gitrob gitrob
●	exit
●	cd /opt/gitrob/bin
●	gem install gitrob (this step seems to have failed in last three runs)
```

### CMSmap
○	CMSmap is a python open source CMS (Content Management System) scanner that automates the process of detecting security flaws
```
●	git clone https://github.com/Dionach/CMSmap /opt/CMSmap
●	Nothing further after clone
```

### WPScan
○	WordPress vulnerability scanner and brute-force tool
```
●	git clone https://github.com/wpscanteam/wpscan.git /opt/wpscan
●	cd /opt/wpscan && ./wpscan.rb --update
```

### Eyewitness
○	EyeWitness is designed to take screenshots of websites, provide some server header info, and identify default credentials if possible.
```
●	git clone https://github.com/ChrisTruncer/EyeWitness.git /opt/EyeWitness
●	cd /opt/EyeWitness/setup
●	./setup.sh
```

### Printer Exploits
○	Contains a number of commonly found printer exploits
```
●	git clone https://github.com/MooseDojo/praedasploit /opt/praedasploit
```

### SQLMap
○	SQL Injection tool
```
●	git clone https://github.com/sqlmapproject/sqlmap /opt/sqlmap
●	GIT Site gives a command of: git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
●	Nothing further after clone
```

### Recon-ng
○	A full-featured web reconnaissance framework written in Python
```
●	git clone https://bitbucket.org/LaNMaSteR53/recon-ng.git /opt/recon-ng
```

### Discover Scripts
○	Custom bash scripts used to automate various pentesting tasks.
```
●	git clone https://github.com/leebaird/discover.git /opt/discover
	OR USE MINE - git clone https://github.com/belouve/discover.git /opt/discover
●	cd /opt/discover && ./update.sh
```

### BeEF Exploitation Framework
○	A cross-site scripting attack framework
```
●	cd /opt/
●	wget https://raw.github.com/beefproject/beef/a6a7536e/install-beef
●	chmod +x install-beef
●	./install-beef
```

### Responder
○	A LLMNR, NBT-NS and MDNS poisoner, with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server supporting NTLMv1/NTLMv2/LMv2, Extended Security NTLMSSP and Basic HTTP authentication. Responder will be used to gain NTLM challenge/response hashes
```
●	git clone https://github.com/SpiderLabs/Responder.git /opt/Responder
●	Nothing further after clone
```

### The Hacker Playbook 2 - Custom Scripts
○	A number of custom scripts written by Peter Kim for The Hacker Playbook 2.
```
●	git clone https://github.com/cheetz/Easy-P.git /opt/Easy-P
●	git clone https://github.com/cheetz/Password_Plus_One /opt/Password_Plus_One
●	git clone https://github.com/cheetz/PowerShell_Popup /opt/PowerShell_Popup
●	git clone https://github.com/cheetz/icmpshock /opt/icmpshock
●	git clone https://github.com/cheetz/brutescrape /opt/brutescrape
●	git clone https://www.github.com/cheetz/reddit_xss /opt/reddit_xss
```

### The Hacker Playbook 2 - Forked Versions
○	Forked versions of PowerSploit and Powertools used in the book. Make sure you clone your own repositories from the original sources.
```
●	git clone https://github.com/cheetz/PowerSploit /opt/HP_PowerSploit
●	git clone https://github.com/cheetz/PowerTools /opt/HP_PowerTools
Collection of PowerShell scripts for exploitation and post exploitation:
●	git clone https://github.com/cheetz/nishang /opt/nishang
```

### DSHashes:
○	Extracts user hashes in a user-friendly format for NTDSXtract
```
●	wget http://ptscripts.googlecode.com/svn/trunk/dshashes.py -O /opt/NTDSXtract/dshashes.py
     >> Error 404 Not Found
```

### SPARTA:
○	A python GUI application which simplifies network infrastructure penetration testing by aiding the penetration tester in the scanning and enumeration phase.
```
●	git clone https://github.com/secforce/sparta.git /opt/sparta
●	apt-get install python-elixir (response of 'already the latest version')
●	apt-get install ldap-utils rwho rsh-client x11-apps finger
```

### NoSQLMap
○	An automated pentesting toolset for MongoDB database servers and web applications.
```
●	git clone https://github.com/tcstool/NoSQLMap.git /opt/NoSQLMap
●	cd /opt/NoSQLMap
●	python setup.py install
```

### Spiderfoot
○	Open Source Footprinting Tool
```
●	mkdir /opt/spiderfoot/ && cd /opt/spiderfoot
●	wget http://sourceforge.net/projects/spiderfoot/files/spiderfoot-2.7.1-src.tar.gz/download
●	OR:  git clone https://github.com/smicallef/spiderfoot.git /opt/spiderfoot
●	tar xzvf download
●	pip install lxml (Response 'requirement already satisfied')
●	pip install netaddr (Response 'requirement already satisfied')
●	pip install M2Crypto (Response 'requirement already satisfied')
●	pip install cherrypy
●	pip install mako (Response 'requirement already satisfied')
To run spiderfoot (possibly by cd /opt/spiderfoot and then ./sf.py )
```

### WCE
○	Windows Credential Editor (WCE) is used to pull passwords from memory
```
●	Download from: http://www.ampliasecurity.com/research/windows-credentials-editor/ and save to /opt/. For example:
	○	cd /opt/
	○	wget www.ampliasecurity.com/research/wce_v1_4beta_universal.zip
	○	mkdir /opt/wce && unzip wce_v1* -d /opt/wce && rm wce_v1*.zip
```

### Mimikatz
○	Used for pulling cleartext passwords from memory, Golden Ticket, skeleton key and more
```
●	Grab the newest release from https://github.com/gentilkiwi/mimikatz/releases/latest
	○	cd /opt/ && wget https://github.com/gentilkiwi/mimikatz/releases/download/2.1.0-20161126/mimikatz_trunk.zip
	○	unzip -d ./mimikatz mimikatz_trunk.zip
```

### Social Engineering Toolkit (SET)
○	This will be used for the social engineering campaigns
```
●	git clone https://github.com/trustedsec/social-engineer-toolkit/ /opt/set/
●	cd /opt/set && ./setup.py install
```

### PowerSploit (PowerShell)
○	PowerShell scripts for post exploitation
```
●	git clone https://github.com/mattifestation/PowerSploit.git /opt/PowerSploit
●	cd /opt/PowerSploit && wget https://raw.githubusercontent.com/obscuresec/random/master/StartListener.py && wget https://raw.githubusercontent.com/darkoperator/powershell_scripts/master/ps_encoder.py
```

### Kadimuus
○	Tool to check sites for Local File Inclusion (LFI) vulnerability, and also exploit it...
```
●	git clone https://github.com/P0cL4bs/Kadimus.git /opt/Kadimus
●	cd /opt/Kadimus && ./configure
```

### Veil-Framework
○	A red team toolkit focused on evading detection. It currently contains Veil-Evasion for generating AV-evading payloads, Veil-Catapult for delivering them to targets, and Veil-PowerView for gaining situational awareness on Windows domains. Veil will be used to create a python based Meterpreter executable.
```
●	git clone https://github.com/Veil-Framework/Veil /opt/Veil
●	cd /opt/Veil/setup && ./setup.py
●	This can take a chunk of time.  Coffee break.  But does ask for some responses periodically, and latest update allows quiet install.
```

### Burp Suite Pro
○	Web Penetration Testing Tool
```
●	Download: http://portswigger.net/burp/proxy.html. I would highly recommend that you buy the professional version. It is well worth the $299 price tag.
●	Built into current Kali, just make sure you have it updated.  Read the burp file under Notes for ideas on configuration
●	Updating BurpSuite
	○	Download the latest jar file from https://portswigger.net/burp/download.html
	○	Navigate to /usr/bin and rename burpsuite to burpsuite(old)
	○	Copy the jar that was just downloaded into /usr/bin
	○	Rename the jar to burpsuite (yes, just burpsuite, no extension), and Allow executing file as program
	○	Upon successful launch, you can delete burpsuite(old) from /usr/bin.
```
### Fuzzing Lists (SecLists)
○	These are scripts to use with Burp to fuzz parameters
```
●	git clone https://github.com/danielmiessler/SecLists.git /opt/SecLists
```

### Net-Creds Network Parsing 
○	Parse PCAP files for username/passwords
```
●	git clone https://github.com/DanMcInerney/net-creds.git /opt/net-creds
```

### ZAP Proxy Pro
○	OWASP ZAP: An easy-to-use integrated penetration testing tool for discovering vulnerabilities in web applications.
```
●	Download from: https://code.google.com/p/zaproxy/wiki/Downloads?tm=2
●	*Included by default in Kali Linux (owasp-zap)
```
### Wifite
○	Attacks against WiFi networks
```
●	git clone https://github.com/derv82/wifite /opt/wifite
●	chmod +x wifite.py
●	./wifite.py  (to do initial launches)
```

### WIFIPhisher
○	Automated phishing attacks against WiFi networks
```
●	git clone https://github.com/sophron/wifiphisher.git /opt/wifiphisher
```

### Phishing (Optional):
```
●	Phishing-Frenzy
	○	git clone https://github.com/pentestgeek/phishing-frenzy.git /var/www/phishing-frenzy
●	Custom List of Extras
	○	git clone https://github.com/macubergeek/gitlist.git /opt/gitlist
```
### Password Lists
●	For the different password lists, see the section: Special Teams - Cracking, Exploits, and Tricks

## Installing Firefox Add-ons
```
●	Web Developer Add-on: https://addons.mozilla.org/en-US/firefox/addon/web-developer/
●	Tamper Data: https://addons.mozilla.org/en-US/firefox/addon/tamper-data/
●	Foxy Proxy: https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/
●	User Agent Switcher: https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/
●	You can reboot your browser now
```

## Installing Chromium
○	Chromium is "an open-source browser project that aims to build a safer, faster, and more stable way for all users to experience the web." I (@ulicbelouve) am a fan of Firefox, but this is another option, with wmap (see below)
```
●	apt-get install chromium
●	Type 'y' and hit the return key when prompted for confirmation
```

### Configure Chromium / wmap
○	If Chromium is opened, it will close in the background without apparent error.  The following configuration will allow Chromium to function with wmap.  wmap is a mass web screenshot tool for mapping out large web networks. Feed it a list of URL(s) and/or Nmap scan data and it will generate you a screenshot report of those web services. Created to allow people to get a visual "map" of their web infrastructure.
```
●	​​Edit the following file: /etc/chromium.d/default-flags
●	Add the following entries to the default-flags file
	  export CHROMIUM_FLAGS="$CHROMIUM_FLAGS ​--no-sandbox"​
	  export CHROMIUM_FLAGS="$CHROMIUM_FLAGS -user-data-dir"​​​
	     This line is necessary in order for Chromium to work in Kali because Kali runs as root
	  export CHROMIUM_FLAGS="$CHROMIUM_FLAGS --ignore-certificate-er​rors"
	     This line is necessary to make wmap work as well as possible.  If wmap is not needed, then this line is 
	     not needed
●	Launch Chromium
●	A prompt may load explicitly stating that it is a bad idea to run chromium as root.  Ignore this
	  ​The known way around this issue would be to designate a user specifically for chromium usage
●	You can see my additions below, on how to "Create a new normal user with sudo permission in Kali Linux", then it should not balk at the oversight
●	​If Chromium does not start, validate that the entries in the default-flags file are correct
```

### Installing wmap
```
●	​Go to the webstore (url: https://chrome.google.com/webstore/)
●	Search for wmap
●	Click "ADD TO CHROME"
●	A pop up should appear asking for confirmation to Add "wmap".  Click "Add extension"
```

*Remember to check http://thehackerplaybook.com/updates/ for any updates.

It is also recommended to configure a Windows Virtual Machine.  This will be detailed in a separate "cheat sheet"

## Extras from Belouve:

### Create a new normal user with sudo permission in Kali Linux
```
●	Open a terminal and issue the following command:
	●	useradd -m belouve
	●	-m creates a home directory for the user (belouve in this example).
●	Now we have to set a password for the user.
	●	passwd belouve
	●	It will ask you to create a new password. 
●	At this point, we have a new user account. But we might want to add our new user to the “sudoers” group, so that we can use “sudo” to do administrative actions.
	●	usermod -a -G sudo belouve
	●	The option -a means to add and ‘-G sudo’ means to add the user to the sudo group. If you want to know more about the usermod command, issue  #man usermod command to know more about usermod
●	Now we have to specify the shell for our new user.
	●	chsh -s /bin/bash belouve
	●	chsh command is used to change the login shell for a user. 
●	All done.! you are all set. You could logout and login to your new account.
```

### HexChat
○	IRC Client
```
●	apt update && apt install -y hexchat
●	Then theme it by grabbing theme of https://dl.hexchat.net/themes/MatriY.hct
●	More themes at https://hexchat.github.io/themes.html
●	unzip ~/Downloads/MatriY.hct -d ~/.config/hexchat
●	/.config is a hidden folder in your Home directory /home/belouve
```

### SSHpass
○	SSHPass is a tiny utility, which allows you to provide the ssh password without using the prompt. This will very helpful for scripting. SSHPass is not good to use in multi-user environment. If you use SSHPass on your development machine, it don't do anything evil. I use this to get a quick login for my SSH tunnel
```
●	apt-get install sshpass
```

### Setting proxies:
```
Acquire::http::proxy  "http://10.EXA.MPL.E0:3128/";
Acquire::ftp::proxy "http://10.EXA.MPL.E0:8080/";
Acquire::https::proxy "http://10.EXA.MPL.E0:8080/";
●	Hey guess what? You need to have the ; at the end of each line
●	3128 is HTTP proxy, 8080 is HTTP, 443 is HTTPS over SSL
```
