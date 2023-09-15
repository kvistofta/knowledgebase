# The SSH protocol

## About SSH

SSH is a protocol that creates a protected tunnel in which you can transfer various kinds of data. The most common usage for ssh is to connect to a terminal server or a shell to access a remote CLI (command line interface). SSH stands for Secure SHell, but you can also use SSH for secure file transfers as well as a tunneling protocol for TCP-traffic.

SSH is a client/server protocol which means that for any single ssh session there is a client, and a server. The client is the part that initiates the connection (establish a TCP connection) to the server, and the server is that part that waits for incoming connections on a TCP socket.


Which part is client or server is relative to a specific session. A computer can be both a ssh server and a client, and the same goes for a client computer. The client/server roles are important to distinguish when you look into a specific session since there are many different configuration files that are specific to ssh clients or servers.

For example, looking into a Linux-server there can be a number of files in the /etc/ssh folder which are all related to the ssh server waiting for incoming connections.  Here is an example from my lab server Bettan:

```shell
jimmy@bettan:~$ ls -l /etc/ssh
total 580
-rw-r--r-- 1 root root    720 Oct 27 10:42 login_banner
-rw-r--r-- 1 root root 535195 Dec  2  2021 moduli
-rw-r--r-- 1 root root   1603 Dec  2  2021 ssh_config
drwxr-xr-x 2 root root   4096 Dec  2  2021 ssh_config.d
-rw------- 1 root root   1381 Mar 14  2022 ssh_host_dsa_key
-rw-r--r-- 1 root root    601 Mar 14  2022 ssh_host_dsa_key.pub
-rw------- 1 root root    505 Mar 14  2022 ssh_host_ecdsa_key
-rw-r--r-- 1 root root    173 Mar 14  2022 ssh_host_ecdsa_key.pub
-rw------- 1 root root    399 Mar 14  2022 ssh_host_ed25519_key
-rw-r--r-- 1 root root     93 Mar 14  2022 ssh_host_ed25519_key.pub
-rw------- 1 root root   2602 Mar 14  2022 ssh_host_rsa_key
-rw-r--r-- 1 root root    565 Mar 14  2022 ssh_host_rsa_key.pub
-rw-r--r-- 1 root root    342 Mar 14  2022 ssh_import_id
-rw-r--r-- 1 root root   3346 Oct 27 10:37 sshd_config
drwxr-xr-x 2 root root   4096 Dec  2  2021 sshd_config.d
jimmy@bettan:~$
```


On the same compute there can be files in the ~/.ssh folder that are related to when the machine is being used as an ssh client connecting to a remote server. Here is the content of the /home/jimmy/.ssh/ folder of the same server as above.

`````shell
jimmy@bettan:~$ ls -la /home/jimmy/.ssh/
total 20
drwx------ 2 jimmy jimmy 4096 Nov 29 18:20 .
drwx------ 9 jimmy jimmy 4096 Nov 23 07:57 ..
-rw------- 1 jimmy jimmy  412 Mar 22  2022 authorized_keys
-rw------- 1 jimmy jimmy 2602 Nov 29 18:20 id_rsa
-rw-r--r-- 1 jimmy jimmy  566 Nov 29 18:20 id_rsa.pub
jimmy@bettan:~$
`````

~ is a special character (tilde, sinus) that in the Linux-world means "current user´s home directory". When logged in as Bob, ~ is equal to /home/bob/ but for Alice, ~ equals to /home/alice.

~/.ssh is where the ssh client stores its configurations **for that specific user** on a server. 


## SSH connection

The SSH client can have a large number of parameters and options, and I will explain a few of them later on. However, the only mandatory parameter is the hostname which specifies the host to connect to. This can be either a DNS name or an IP-adress:

`````shell
usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-E log_file] [-e escape_char]
           [-F configfile] [-I pkcs11] [-i identity_file]
           [-J [user@]host[:port]] [-L address] [-l login_name] [-m mac_spec]
           [-O ctl_cmd] [-o option] [-p port] [-Q query_option] [-R address]
           [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]
           [user@]hostname [command]
           `````


When connecting we are asked to specify a password of the user we are logging in as. Since we don´t specify a username to the ssh command, it will assume the name of the current logged in user. Since this session belongs to "nisse", we login to the remote ssh server as nisse and must specify the password that belongs to nisse.


```shell
nisse@linux5-61:~$ ssh 10.0.5.62
nisse@10.0.5.62s password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 08:50:05 2022 from 10.0.5.61
nisse@linux5-62:~$
```


## Specifying username

There are 2 ways to specify which username we want to login as. Any of these must be used when the locally logged in username is not the same as the remote username.

Either we specify a username with the -l option:
`````shell
nisse@linux5-61:~$ ssh -l pelle 10.0.5.62
pelle@10.0.5.62's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 11:54:28 2022 from 10.0.5.61
pelle@linux5-62:~$
`````


Or we use a more common way to specify the username: preceeding the hostname/ip address followed by an at-sign @:
`````shell
nisse@linux5-61:~$ ssh pelle@10.0.5.62
pelle@10.0.5.62's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Tue Nov 29 13:46:27 2022 from 10.0.5.61
pelle@linux5-62:~$
`````
(Please note that there must be no spaces before or after the @-sign)


## known_hosts

You might have experienced that sometimes when using SSH you get warning and a message that looks like this:

`````
The authenticity of host '10.0.5.62 (10.0.5.62)' can't be established.
ECDSA key fingerprint is SHA256:+FH2torvhNGLWpFG8BcAZ9ce6iS/uA1UrbDO+OmOFV8.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
`````

If we answer "yes" and login using the password, we are let in. If you disconnect and re-connect to the same server you will not get the warning above again. So, what is this?

When connecting to a remote server it will send us its public key (more about keys later). And the key fingerprint above is a hash och the SSH servers public key. This is a warning that you have never connected to this server before, and a question if you want to proceed logging in.

The reason that you will not get the same question every time is that the server key fingerprint is stored locally at the client. The location for SSH fingerprints is ~/.ssh/known_hosts. Since ~ is relative to the logged in user, the location for the known_hosts file is "in the .ssh directory inside of the users home directory" and every single user inside of that client computer has its own known_hosts file.

The known_hosts file can grow, for each server you connect to from that client the fingerprint is stored as a single line within ~/.ssh/known_hosts.

This solution is a security measure to prevent spoofing attacks. If you connect to the same server again and again and see no warning about key fingerprints you can be safe in the fact that you connect to the same server as before. 

Let´s simulate a spoofing attack. I connect to the server, making sure that the ssh client stores the fingerprint in known_hosts:

`````shell
nisse@linux5-61:~$ ssh 10.0.5.62
The authenticity of host '10.0.5.62 (10.0.5.62)' can't be established.
ECDSA key fingerprint is SHA256:+FH2torvhNGLWpFG8BcAZ9ce6iS/uA1UrbDO+OmOFV8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.0.5.62' (ECDSA) to the list of known hosts.
nisse@10.0.5.62's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 12:07:03 2022 from 10.0.5.61
nisse@linux5-62:~$
`````

If I disconnect and re-connects I will get no fingerprint warning:

`````shell
nisse@linux5-62:~$ exit
logout
Connection to 10.0.5.62 closed.
nisse@linux5-61:~$ ssh 10.0.5.62
nisse@10.0.5.62's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 12:07:26 2022 from 10.0.5.61
nisse@linux5-62:~$
`````

Now I will simulate a spoofing attack by logging into the SSH server, delete and re-create the SSH keys forcing the fingerprint to be different:

First I delete the files where the SSH server stores its keys:

`````shell
root@linux5-62:~# ls -l /etc/ssh
total 576
-rw-r--r-- 1 root root 535195 Jul 23  2021 moduli
-rw-r--r-- 1 root root   1603 Jul 23  2021 ssh_config
drwxr-xr-x 2 root root   4096 Jul 23  2021 ssh_config.d
-rw------- 1 root root   1393 Nov 22  2021 ssh_host_dsa_key
-rw-r--r-- 1 root root    610 Nov 22  2021 ssh_host_dsa_key.pub
-rw------- 1 root root    513 Nov 22  2021 ssh_host_ecdsa_key
-rw-r--r-- 1 root root    182 Nov 22  2021 ssh_host_ecdsa_key.pub
-rw------- 1 root root    411 Nov 22  2021 ssh_host_ed25519_key
-rw-r--r-- 1 root root    102 Nov 22  2021 ssh_host_ed25519_key.pub
-rw------- 1 root root   2610 Nov 22  2021 ssh_host_rsa_key
-rw-r--r-- 1 root root    574 Nov 22  2021 ssh_host_rsa_key.pub
-rw-r--r-- 1 root root    342 Nov 22  2021 ssh_import_id
-rw-r--r-- 1 root root   3336 Nov 22  2021 sshd_config
drwxr-xr-x 2 root root   4096 Jul 23  2021 sshd_config.d
root@linux5-62:~# rm /etc/ssh/ssh*.pub
root@linux5-62:~# rm /etc/ssh/ssh*_key
root@linux5-62:~#
`````
(All server side ssh-related files are stored in /etc/ssh)

Next step I run a few commands to force the SSH server to create new server-side keys:

`````shell
root@linux5-62:~# sudo dpkg-reconfigure openssh-server
Creating SSH2 RSA key; this may take some time ...
3072 SHA256:fLh2dWFSY/5pvumSRXEga0oUwvbEVCT6p5Rv8q2rT6g root@linux5-62 (RSA)
Creating SSH2 ECDSA key; this may take some time ...
256 SHA256:mR3i004pgWZ66tmea0i1mKiXw2peflcbP9WjTpNU4rM root@linux5-62 (ECDSA)
Creating SSH2 ED25519 key; this may take some time ...
256 SHA256:xc96Pp56lpHXdnqES4Mpd/fdr8Z6VFrsMS5XJWh5Tf4 root@linux5-62 (ED25519)
rescue-ssh.target is a disabled or a static unit, not starting it.
root@linux5-62:~#
root@linux5-62:~# systemctl restart ssh
root@linux5-62:~#
`````


Now, when trying to connect to the server from the client that has already stored a key fingerprint we get a nasty error message preventing us from connecting to the server:

`````shell
nisse@linux5-61:~$ ssh 10.0.5.62
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:mR3i004pgWZ66tmea0i1mKiXw2peflcbP9WjTpNU4rM.
Please contact your system administrator.
Add correct host key in /home/nisse/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/nisse/.ssh/known_hosts:1
  remove with:
  ssh-keygen -f "/home/nisse/.ssh/known_hosts" -R "10.0.5.62"
ECDSA host key for 10.0.5.62 has changed and you have requested strict checking.
Host key verification failed.
nisse@linux5-61:~$
`````

This can be solved in 2 different ways:
* Edit ~/.ssh/known_hosts and delete the corresponding line (line 1 in this case as stated in the error message above). We can also delete the entire known_hosts file, with the drawback of deleting **all** fingerprints for all previously connected servers.
* Run the ssh-keygen -f command as stated in the error message above.

Conclusion: Key fingerprint verification is a good thing. You can in most circumstances answer "yes" to the question "are you sure you want to continue connecting". However, if you get the "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED" you should be cautious to connect. Something bad might have happened.

SSH keys

The power of SSH comes when you start using keys instead of passwords. How about logging into the server and this happens:

```shell
nisse@linux5-61:~$ ssh 10.0.5.62
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 12:08:15 2022 from 10.0.5.61
nisse@linux5-62:~$
```

At first glance this might give you a sense of insecurity, but the truth is quite the opposite! Password-less authentications using asymmetric keys is superio in both flexibility and security.

Asymmetric keys and encryption is a separate (large!) chapter. But there are a few key facts that must be known about asymmetric keys before we move on:

* The keys always comes in pairs. You create both keys at the same time. In most implementations we choose to call one of the keys "private" and the other "public".
* Both SSH server and client have separate key pairs. (In the previous chapter I deliberatly deleted the server keys, both the private and public). 
* The private key should *always* remain private. It should stay at the owners disk at all times (unless for backup purposes). Private keys are never transfered.
* The public key can be spread to the entire world. There is no limit to the publicity!


The Server SSH keys are created when the SSH server was configured and we don´t need to bother much about them. However, when it comes to the clients keypair there is a lot to talk about.

The client keys are by default stored in the ~/.ssh/ directory. The private key is named id_rsa and the public key id_rsa.pub.

To understand how key based authentication works in SSH we can establish a session using the -v option for verbosity. Doing so we will see a wall of text:

`````
nisse@linux5-61:~$ ssh -v nisse@10.0.5.62
OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to 10.0.5.62 [10.0.5.62] port 22.
debug1: Connection established.
SHA256:mR3i004pgWZ66tmea0i1mKiXw2peflcbP9WjTpNU4rM
debug1: Host '10.0.5.62' is known and matches the ECDSA host key.
debug1: Found key in /home/nisse/.ssh/known_hosts:1
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: publickey
debug1: Trying private key: /home/nisse/.ssh/id_rsa
debug1: Trying private key: /home/nisse/.ssh/id_dsa
debug1: Trying private key: /home/nisse/.ssh/id_ecdsa
debug1: Trying private key: /home/nisse/.ssh/id_ecdsa_sk
debug1: Trying private key: /home/nisse/.ssh/id_ed25519
debug1: Trying private key: /home/nisse/.ssh/id_ed25519_sk
debug1: Trying private key: /home/nisse/.ssh/id_xmss
debug1: Next authentication method: password
nisse@10.0.5.62's password:
debug1: Authentication succeeded (password).
Authenticated to 10.0.5.62 ([10.0.5.62]:22).
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 13:51:12 2022 from 10.0.5.61
nisse@linux5-62:~$
`````
(I have deleted part of the output to make it easier to read.

On the 9:th line we can see that the server supports two authentication methods: publickey and password (and in that order!)

On the following lines we can see that our client tries to open a number of key files. Note the first one: /home/nisse/.ssh/id_rsa. At the time when I did the connection there was no key files (yet), hence the "Next authentication method: password". After the client failing to authenticate with keys it tries with password, prompts the user for password and authenticates: "Authentication succeded"-

So this server tells the client to **first** try to authenticate with keys, and **then** with passwords. 

Lets jump ahead and see how a session establishment looks like in verbose mode when we **do** have keys in place (I will explain further below how to create and transfer those files but for now just trust that I have setup key based authentications on the client and server):

```
nisse@linux5-61:~$ ssh -v nisse@10.0.5.62
OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 10.0.5.62 [10.0.5.62] port 22.
debug1: Connection established.
SHA256:mR3i004pgWZ66tmea0i1mKiXw2peflcbP9WjTpNU4rM
debug1: Host '10.0.5.62' is known and matches the ECDSA host key.
debug1: Found key in /home/nisse/.ssh/known_hosts:1
debug1: Will attempt key: /home/nisse/.ssh/id_rsa RSA SHA256:a3lmbl4pkNplR7LTnSiduGBA1j3q7TAedjufWJdO2Oo
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: publickey
debug1: Offering public key: /home/nisse/.ssh/id_rsa RSA SHA256:a3lmbl4pkNplR7LTnSiduGBA1j3q7TAedjufWJdO2Oo
debug1: Server accepts key: /home/nisse/.ssh/id_rsa RSA SHA256:a3lmbl4pkNplR7LTnSiduGBA1j3q7TAedjufWJdO2Oo
debug1: Authentication succeeded (publickey).
Authenticated to 10.0.5.62 ([10.0.5.62]:22).
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 14:02:02 2022 from 10.0.5.61
nisse@linux5-62:~$
```

There are a few major differences between the output above and the previous one:
* The client found a key: /home/nisse/.ssh/id_rsa
* The client "offered" that key to the server
* Authentication succeeded and there was no need to ask the user for a password
* We were logged in.

Without the debug output, this what the user sees:

```shell
nisse@linux5-61:~$ ssh nisse@10.0.5.62
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 14:11:50 2022 from 10.0.5.61
nisse@linux5-62:~$
```


## Some crypto

When authenticating we (the client user) proove our identity by proving the ownership of the private key. That private id_rsa key file is what is needed in order to login to the server. If we loose the private key file we can no longer login to that server with key based authentication.

so we proove that we possess the private key file. But it is never sent to the server! That is a very important concept of key based authentication. If the solution was to send the private key over to the server it could be caught in transit or at the server. Remember, the private file is never transfered!

So how can we "show" the private key to the server without sending it? 

The solution is something called "challenge response". A quite simplified solution comes here:

The key(!) concept of assymmetric encryption is that anything that is encrypted with one key can only be decrypted with the other key in that key pair. But this is about authentication, not encryption.

Challenge response means that the server "challenges" the client by creating a random blob of data (nonse) and sending that random data to the client. The client encrypts the data with its private key and sends it back to the server who decypts the date with the client **public** key. If the decrypted data is identical to the original (unencrypted) blob, the server knows that the client possess the private key whose "twin" (the public key) is in the server associated with that user.

It´s a kind of magic!

Note: Crypto is also confidentiality. I mentioned above that the keys are being used for authentication, and that´s true. SSH is also encrypted, all data sent between the client and server is encrypted, but not using these assymetric keys. There are several reasons for that:

* Even without client keys (when we logged in with passwords) the SSH connection needs to be encrypted, with no keys available.
* Assymmetric encryption is too slow to be used for encryption of large amounts of data.

As a matter of fact: almost all SSH traffic is encrypted with symmetric encryption methods as AES. But that´s another story. Back to the assymmetric keys!


## Creating ssh keys

In order to use key based authentication we must either create a new ssh keypair on the client or move already created keys over to this client. 

A word of caution: I earlier said that private keys should never be moved. Remember that. If you have a computer A where you have already created ssh client keys to login to server X, and you now want to login to the same server X from the another computer B, it is tempting to take a copy of the keys from A and place them on B so that you can connect to X from either of these computers. That is fully possible. But if you start working in that direction, you might later realize that your keys are residing "all over the place.".

You can login to server X as nisse both from A and B, using one keypair on A and **another** keypair on B. That´s a much better solution! You just add 2 different public keys into the same /home/nisse/.ssh/authorized_keys file (we will talk about that file in the next section) on different lines. Way better!

(You can also use SSH jump hosts or agent forwarding, but that´s an "advanced" feature of SSH and now and here is not the correct time and place for explaining those techniques. Maybe later...)



It´s time to explain how these keys are created. The client keys are stored in ~/.ssh/id_rsa (the private key) and ~/.ssh/id_rsa.pub (the public key).  In the below example those files does not exist yet:

```shell
nisse@linux5-61:~$ ls -l .ssh
total 4
-rw-r--r-- 1 nisse nisse 222 Nov 30 13:50 known_hosts
nisse@linux5-61:~$
```

The command for creating new keys is ssh-keygen. When we run this command (if we dont have any keys created yet) this will happen:

```shell
nisse@linux5-61:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/nisse/.ssh/id_rsa):
```

First of we are giving an opportunity to where to save the key(s). The default location is in most cases correct. I choose to just press Enter:

```shell
Enter passphrase (empty for no passphrase):
```

Now we are asked for a passphrase. A passphrase is a way to protect your private key. If someone else gets hold of your precious private key and it is not passphrase protected, that someone can use that key to login as you. If you add a passphrase, that someone needs to know that passphrase aswell in order to inpersonize you.

On the other hand, if you passphrase your key, you have to enter that passphrase whenever you use that key to login somewhere (unless you use a ssh agent, more about that later). Also, som automated tasks (i.e scp) cannot be done easily with passphrase protected keys.

In most cases I recommend **not** to passphrase your keys. In this example I wont, so I just press Enter:

```shell
Enter same passphrase again:
```

And Enter again :)

```shell
Your identification has been saved in /home/nisse/.ssh/id_rsa
Your public key has been saved in /home/nisse/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:xW1PgEKZj+Y1hJRVFdhx8qsPgBnnwKy7ifU/cyCabds nisse@linux5-61
The key's randomart image is:
+---[RSA 3072]----+
|       oo*.oo+=o.|
|        O.o....+ |
|         Xo.o . .|
|        +.@. o  .|
|       +S+ +  .. |
|        o. .. .  |
|       o+ . .o   |
|      oo+o.o .o  |
|     . o.ooE+  . |
+----[SHA256]-----+
```

Done! What what is this ASCII art? In daily life it is something that no one cares about. :) It´s a way for those with NASA/CSI/NSA grade of security requirements to visually recognize this key. The fingerprint SHA256:xW1Pg... in a "graphical" presentation. 

If we look inside of our .ssh directory the two ssh key files have now shown up as stated in the output above:

```shell
nisse@linux5-61:~$ ls -l .ssh
total 12
-rw------- 1 nisse nisse 2602 Dec  1 12:01 id_rsa
-rw-r--r-- 1 nisse nisse  569 Dec  1 12:01 id_rsa.pub
-rw-r--r-- 1 nisse nisse  222 Nov 30 13:50 known_hosts
nisse@linux5-61:~$
```


Let´s have a look at these keys. First, the private key in id_rsa:

`````shell
nisse@linux5-61:~$ cat .ssh/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAlZtJgtWLigjZXa4nc8GxRKRDXdHFB4L0OrUcX8h23Efl0JswhNWr
(A lot of lines removed for easier reading)
RlBTzKLDUZD6EUXIVF7oZJIcCtiWWtkY6rPcfC5dYgJHdG+AHCQfZfZ+K7Y8JrJemNI0mJ
2+BVyqIMmBA0TZAAAAD25pc3NlQGxpbnV4NS02MQECAw==
-----END OPENSSH PRIVATE KEY-----
nisse@linux5-61:~$
`````

If you ever want to copy/paste the content of tis file, remember to include both lines with the dashes (-----). They are part of the key.

Now, the public key in id_rsa.pub:

```shell
nisse@linux5-61:~$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCVm0mC1YuKCNldridzwbFEpENd0cUHgvQ6tRxfyHbcR+XQmzCE1auffR/CnD5kcht2zX9scr2tFjXwZid18VTGyMm+nEMeM1nK/bhhiZ0o1d9v2FQbM9iFuD1huX2/iLqO1Wieub1qD1LywIwDcMz1eG3LPxDiqsiaYY2YLd2G2vru2DVWyQQ5y16aSMB1ZTIUqLoCoXA4Xiel6Kdzh71KzjG8Cr3delUU90TRdf2cbpV+0fykb0sEmxXW6yv8AwdYn28o0eIZvb4fLjsn7ESe5IxKJvX3X66W8sIDNEgwVHdhdmbr5riWk7YUCM8Ag1LnBG5MVX2DkMmaA2lxbc7sRye/fnNWfMAa2462Lay50mE4b0hHZE3Lv8LSHaxUC7o2F8RUDhx1WuVeno+HC1pjhvrhXEnYhnSuwlwe0jihCGR17V7XbWoMAa1dAswA1YUi+0pXEIcR7ghoSBerDedytAKbQ48wXa/SB2gCeADldTvrhy/+sekhetCklEI/TQM= nisse@linux5-61
nisse@linux5-61:~$
```

It might sound strange when I tell you that the public key is one single line. But it´s true. It might wrap differently depending on the width of your screen but is just shows with line breaks to make it possible to read the entire key. When you copy/paste this key remember to mark the entire line including the "ssh-rsa" in the beginning and the final string, in this example "nisse@linux5-61". They must follow along and it is all on one single line.

(The trailer "nisse@linux5-610" is just an identifier telling who this key belongs to. It can actually be edited to anything, the text is ignored when using the key to login.)

## Transfer public key to server


In order to use your newly created keypair you must associate the public key with a remote login. This is done by copying the public key to the server ~/.ssh/authorized_keys file. This can be done in multiple ways: manually or with ssh-copy-id.

### Manually copying the public key

To manually copy the public key from the client to the server you mark and copy the entire key ("ssh-rsa .......... nisse@linux5.61" in my example), start an editor on the server, edit the ~/.ssh/autorized_keys file and paste the text on a new line, save the file and exit. 

### Transfer the public key with ssh-copy-id

I often prefer to use the tool ssh-copy-id to transfer the public key. It automates the process for you and it works like this:

```shell
nisse@linux5-61:~$ ssh-copy-id nisse@10.0.5.62
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/nisse/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
nisse@10.0.5.62's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'nisse@10.0.5.62'"
and check to make sure that only the key(s) you wanted were added.

nisse@linux5-61:~$
```

You use the same syntax for specifying username and host as with the ssh-command: username@hostname, but the command is ssh-copy-id. The tool creates a SSH session to the server (asking you for your password to authenticate, this is probably the last time you have to enter that password, yay!), and inside of the protected tunnel it transfers the public key and writes it into your .ssh/authorized_keys file on the server. Cool, huh?

Next time I login to the server:

```shell
nisse@linux5-61:~$ ssh nisse@10.0.5.62
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Wed Nov 30 14:16:43 2022 from 10.0.5.61
nisse@linux5-62:~$
```

Like magic!

However, there are a few pros and cons for the ssh-copy-id tool:

Pros:
* The simplicity. Everything is taken care for you, including file permissions (see next section)

Cons:
* The tool doesn´t always exist. As far as I know Windows 11 comes with cli-based SSH client but not the ssh-copy-id tool (please correct me if I am wrong)
* If password-based authentication is disabled on the server you cannot login with password to add the key.



## File permissions

Both the client and the server are a bit "picky" about the permissions of the files involved. There are well founded reasons for that: On a multi user server you don´t want a user to steal another users private key! And you don´t want someone else adding **their** public key to **your** authoried_keys file! When logging in both the client and server verifies the permissions of these files before using them.

### File permissions on the client

Let´s have a look at what happens if you try to login to a remote server using a private key that is not "protected". 

```shell
nisse@linux5-61:~$ ls -la .ssh
total 20
drwx------ 2 nisse nisse 4096 Dec  1 13:28 .
drwxr-xr-x 3 nisse nisse 4096 Nov 30 07:51 ..
-rw------- 1 nisse nisse 2602 Dec  1 12:01 id_rsa
-rw-r--r-- 1 nisse nisse  569 Dec  1 12:01 id_rsa.pub
-rw-r--r-- 1 nisse nisse  222 Nov 30 13:50 known_hosts
nisse@linux5-61:~$
```

As you can see above the file permissions for id_rsa (the private key) is 600 and it is owned by nisse:nisse. This means that no other user can do anything to the file. Lets break that:

```shell
nisse@linux5-61:~$ chmod go+r .ssh/id_rsa
nisse@linux5-61:~$
nisse@linux5-61:~$ ls -la .ssh
total 20
drwx------ 2 nisse nisse 4096 Dec  1 13:28 .
drwxr-xr-x 3 nisse nisse 4096 Nov 30 07:51 ..
-rw-r--r-- 1 nisse nisse 2602 Dec  1 12:01 id_rsa
-rw-r--r-- 1 nisse nisse  569 Dec  1 12:01 id_rsa.pub
-rw-r--r-- 1 nisse nisse  222 Nov 30 13:50 known_hosts
nisse@linux5-61:~$
```


Now any user on the system has read access to the private key file. What happens when we try to use that file to login to a server?

```shell
nisse@linux5-61:~$ ssh nisse@10.0.5.62
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/home/nisse/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/nisse/.ssh/id_rsa": bad permissions
nisse@10.0.5.62's password:
```

We are not allowed to use that key, and the client falls back to trying to authenticate with password!

Best practice for file permissions on the client is the way it was before I broke it:

```shell
nisse@linux5-61:~$ ls -la .ssh
total 20
drwx------ 2 nisse nisse 4096 Dec  1 13:28 .
drwxr-xr-x 3 nisse nisse 4096 Nov 30 07:51 ..
-rw------- 1 nisse nisse 2602 Dec  1 12:01 id_rsa
-rw-r--r-- 1 nisse nisse  569 Dec  1 12:01 id_rsa.pub
-rw-r--r-- 1 nisse nisse  222 Nov 30 13:50 known_hosts
nisse@linux5-61:~$
```

* The ~/.ssh directory should only be accessible by the user.
* No files in the ~/.ssh directory should be writable by anyone except the user.
* The private key file should not be readable by anyone except the user.

### File permissions on the server

Also on the server there are requirements for file permissions. This is how it should look:

```shell
nisse@linux5-62:~$ ls -la .ssh
total 12
drwx------ 2 nisse nisse 4096 Dec  1 14:16 .
drwxr-xr-x 4 nisse nisse 4096 Nov 30 13:51 ..
-rw------- 1 nisse nisse 1707 Dec  1 13:28 authorized_keys
nisse@linux5-62:~$
```


Let´s brake it!

```shell
nisse@linux5-62:~$ chmod go+rwx .ssh/authorized_keys
nisse@linux5-62:~$
nisse@linux5-62:~$ ls -la .ssh
total 12
drwx------ 2 nisse nisse 4096 Dec  1 14:16 .
drwxr-xr-x 4 nisse nisse 4096 Nov 30 13:51 ..
-rw-rwxrwx 1 nisse nisse 1707 Dec  1 13:28 authorized_keys
nisse@linux5-62:~$

After logging out and trying to login again:

nisse@linux5-61:~$ ssh nisse@10.0.5.62
nisse@10.0.5.62's password:
```

We can no longer login with our key! And from the client perspective we get no clue whatsoever why! 

Conclusion:

You can create the .ssh-directory manually, create the authorized_keys file by hand, copying your key files from other computers into .ssh/id_rsa and .ssh/id_rsa.pub. It is just files with special names and content. But if you do it "by hand" you must be aware of the ownership and permissons of the .ssh-directory and the files within.

When creating keys with ssh-keygen, and when copying the public key with ssh-copy-id, the tools sets the permissions according to best practice for you. How nice of them!


## Different client keys

There are many different type of ssh keys. The most common key types are RSA-based, which are stored in id_rsa and id_rsa.pub. As you can see if you run ssh with the -v option, the client tries to find several different key files when attempting to authenticate:

```
debug1: Trying private key: /home/nisse/.ssh/id_dsa
debug1: Trying private key: /home/nisse/.ssh/id_ecdsa
debug1: Trying private key: /home/nisse/.ssh/id_ecdsa_sk
debug1: Trying private key: /home/nisse/.ssh/id_ed25519
debug1: Trying private key: /home/nisse/.ssh/id_ed25519_sk
debug1: Trying private key: /home/nisse/.ssh/id_xmss
```

In the above order, it first looks for id_rsa and if that exists it doesn´t look any further. It is beyond the scope of this document to describe the other files but if you want to understand more about the different key types [here](https://goteleport.com/blog/comparing-ssh-keys/).

## Extract the public key from the private

If you, for any reason, lose your public key, you can extract it from the private key file with the following command:

```shell
ssh-keygen -y -f .ssh/id_rsa
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCVm0mC1YuKCNldridzwbFEpENd0cUHgvQ6tRxfyHbcR+XQmzCE1auffR/CnD5kcht2zX9scr2tFjXwZid18VTGyMm+nEMeM1nK/bhhiZ0o1d9v2FQbM9iFuD1huX2/iLqO1Wieub1qD1LywIwDcMz1eG3LPxDiqsiaYY2YLd2G2vru2DVWyQQ5y16aSMB1ZTIUqLoCoXA4Xiel6Kdzh71KzjG8Cr3delUU90TRdf2cbpV+0fykb0sEmxXW6yv8AwdYn28o0eIZvb4fLjsn7ESe5IxKJvX3X66W8sIDNEgwVHdhdmbr5riWk7YUCM8Ag1LnBG5MVX2DkMmaA2lxbc7sRye/fnNWfMAa2462Lay50mE4b0hHZE3Lv8LSHaxUC7o2F8RUDhx1WuVeno+HC1pjhvrhXEnYhnSuwlwe0jihCGR17V7XbWoMAa1dAswA1YUi+0pXEIcR7ghoSBerDedytAKbQ48wXa/SB2gCeADldTvrhy/+sekhetCklEI/TQM= nisse@linux5-61
```

If you for any reason lose your **private** key, restore your backup. If that fails, maybe you should work with something else. :)


## Key passphrases

Passphrases adds an extra layer of security to ssh by implementing MFA (multi factor authentication). The key is something you **have**, and if you also require something you know **(**the passphrase) you are multi factored!

To add a passphrase to an existing private key you run this command:

```shell
nisse@linux5-61:~$ ssh-keygen -p -f .ssh/id_rsa
Key has comment 'nisse@linux5-61'
Enter new passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved with the new passphrase.
nisse@linux5-61:~$
```

From now on, every time you summon the holy private key for authentication, you will get asked about the passphrase:

```shell
nisse@linux5-61:~$ ssh  nisse@10.0.5.62
Enter passphrase for key '/home/nisse/.ssh/id_rsa':
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-132-generic x86_64)

Last login: Thu Dec  1 18:37:52 2022 from 10.0.5.61
nisse@linux5-62:~$
```

Please not the distinction between "Enter passphrase for key" and "nisse@10.0.5.62´s password:".

To remove the passphrase from the key or change to another passphrase:

```shell
nisse@linux5-61:~$ ssh-keygen -p
Enter file in which the key is (/home/nisse/.ssh/id_rsa):
Enter old passphrase:
Key has comment 'nisse@linux5-61'
Enter new passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved with the new passphrase.
nisse@linux5-61:~$
```




## TODO:



## Running commands with ssh

## SCP file transfers

## Port forwarding

## Escape to the SSH prompt

## SSH agent (007?)

## SSH agent forwarding
