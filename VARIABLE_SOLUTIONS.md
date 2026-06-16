
Public IP: 103.233.58.240 

Port 2083 : C-panel - hosting.accessworld.net


www.variablesolutions.com [Wordpress SIte]


```
Subdomains : 

ctf  / support / demo -- CTF Cyber Drill

docx - nextcloud

mm - matter most
```


## CTF

https://ctf.variablesolutions.com.np


### Forensics :

#### Picture Perfect :  `flag{Doctor}`
```
─# stegcracker converted.jpeg /usr/share/wordlists/rockyou.txt
```


#### Silent Frequencies : `flag{red}`

```
Just upload your wav file and see the sonogram:

https://sinestools.univie.ac.at/audioanalyzer.htm
```


#### Flag in Flame :: 
`picoCTF{forensics_analysis_is_amazing_ec1984fc}`
 
```
1. Its png file ; encoded in bas64 ; Decode it with cyberchef
   
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)Render_Image('Raw')
   
   
1. You will see the png and see some encoded data ; copy that and decode it again to get the flag: From Hex

7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F65633139383466637D
```


#### Operation "Eye Echo"  [xxxx]
 We cant do this one online it hangs the browser , aaile download garera heraula.....



### Obfuscation

#### Layers of Lies  `Flag{solu}`

As the challenge suggests obfuscation , we get flag.txt. 

```
First its hexdump:: xxd -r will reverse it:

└─# cat flag.txt | xxd -r > check

└─# file check   
check: XZ compressed data, checksum CRC64


The next layer is XZ compression, decompressed with the xz binary:

└─# xz -dc check > check2

└─# file check2
check2: bzip2 compressed data, block size = 900k


next layer is bzip2 compression, bzip2 -d will decompress:

└─# bzip2 -d check2     
bzip2: Can't guess original name for check2 -- using check2.out

└─# file check2.out 
check2.out: gzip compressed data, was "red.bin", last modified: Wed May 13 05:44:57 2026, from Unix, original size modulo 2^32 11


Next Layer is gzip compressed. 

First change the extension to .gz 

└─# mv check2.out check2.gz    

└─# gunzip check2.gz 

└─# cat check2                   
```


####  The Enigmatic Key [XXX]


There's index.html file encoded with binary ; Decoding it : Its a cool website with 1010101:  and  text  `V@r!aBl3` 

```
VkByIWFCbDM= | base64 -d

V@r!aBl3
```


Found a signal.zip file which is password protected ; Password not found in rockyou.txt !



### Network



