
![](Kaiju_Cover_.png)


```
Host is up (0.019s latency).
Not shown: 999 filtered ports
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Host is up (0.019s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
3389/tcp open  ms-wbt-server

Host is up (0.018s latency).
Not shown: 999 filtered ports
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
```





There are three users, but only two of them actually enabled. We can see that `ftp` user is mounted to `E:\\Public`, while `backup` user is mounted from `E:\\Private` and it is password protected.

We can try to crack that hash… After some research we see that this hash is generated through `filezilla-server-crypt` and basically it’s hashing the password using `pbkdf2` with `hmac_sha256`, and then `base64-encoding` it without padding altogether with a random salt. So we could try to build the correct hash and crack it with hashcat.

Upon revieweng hashcat forums, we see that hashcat (module 10900) expects the following format:

```
"sha256", ":", iterations, ":", base64 salt, ":", base64 digest
```



Before cracking it, let’s write a custom wordlist as xct advised, something like:


```
kaiju123
backup123
kaiju2024
backup2024
kaiju123!
backup123!
kaiju2024!
backup2024!
```