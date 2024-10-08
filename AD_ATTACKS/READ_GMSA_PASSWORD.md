
[Read_Blog](https://www.thehacker.recipes/a-d/movement/dacl/readgmsapassword)


Group Managed Service Accounts are a special type of Active Directory object, where the password for that object is mananaged by and automatically changed by Domain Controllers on a set interval.


The intended use of a GMSA is to allow certain computer accounts to retrieve the password for the GMSA, then run local services as the GMSA. An attacker with control of an authorized principal may abuse that privilege to impersonate the GMSA


How to find ?

--> Bloodhound [Explicit Object Controllers [Inbound]]
				[First Degree Object Control [Outbound]]


## ABUSE 

There are several ways to abuse the ability to read the GMSA password. The most straight forward abuse is possible when the GMSA is currently logged on to a computer, which is the intended behavior for a GMSA. If the GMSA is logged on to the computer account which is granted the ability to retrieve the GMSA's password, simply steal the token from the process running as the GMSA, or inject into that process.

If the GMSA is not logged onto the computer, you may create a scheduled task or service set to run as the GMSA. The computer account will start the sheduled task or service as the GMSA, and then you may abuse the GMSA logon in the same fashion you would a standard user running processes on the machine (see the "HasSession" help modal for more details).

Finally, it is possible to remotely retrieve the password for the GMSA and convert that password to its equivalent NT hash, then perform overpass-the-hash to retrieve a Kerberos ticket for the GMSA:


### LINUX_ABUSE

Finally, it is possible to remotely retrieve the password for the GMSA and convert that password to its equivalent NT hash.[gMSADumper.py](https://github.com/micahvandeusen/gMSADumper) can be used for that purpose.


```zsh
gMSADumper.py -u 'user' -p 'password' -d 'domain.local'
```

At this point you are ready to use the NT hash the same way you would with a regular user account. You can perform pass-the-hash, overpass-the-hash, or any other technique that takes an NT hash as an input.


### WINDOWS_ABUSE

Build GMSAPasswordReader.exe from its source : [GMSAPasswordReader](https://github.com/rvazarkar/GMSAPasswordReader) (C#).


```powershell
GMSAPasswordReader.exe --AccountName 'Target_Account'
```


At this point you are ready to use the NT hash the same way you would with a regular user account. You can perform pass-the-hash, overpass-the-hash, or any other technique that takes an NT hash as an input.
