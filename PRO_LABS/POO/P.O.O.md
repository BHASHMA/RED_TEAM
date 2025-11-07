
## INTRODUCTION

```
Professional Offensive Operations

By eks and mrb3n

Professional Offensive Operations is a rising name in the cyber security world.

Lately they've been working into migrating core services and components to a state of the art cluster which offers cutting edge software and hardware.

P.O.O. is designed to put your skills in enumeration, lateral movement, and privilege escalation to the test within a small Active Directory environment that is configured with the latest operating systems and technologies.

The goal is to compromise the perimeter host, escalate privileges and ultimately compromise the domain while collecting several flags along the way.

Entry Point: 10.13.38.11
```


## RECON

```
PORT     STATE SERVICE  VERSION
80/tcp   open  http     Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.2056.00; RTM+
|_ssl-date: 2025-11-07T13:48:48+00:00; -2s from scanner time.
| ms-sql-info: 
|   10.13.38.11:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM+
|       number: 14.00.2056.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: true
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.13.38.11:1433: 
|     Target_Name: POO
|     NetBIOS_Domain_Name: POO
|     NetBIOS_Computer_Name: COMPATIBILITY
|     DNS_Domain_Name: intranet.poo
|     DNS_Computer_Name: COMPATIBILITY.intranet.poo
|     DNS_Tree_Name: intranet.poo
|_    Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-11-07T03:21:04
|_Not valid after:  2055-11-07T03:21:04

```

