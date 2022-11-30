# The SSH protocol

##About SSH

SSH is a protocol that creates a protected tunnel in which you can transfer various kinds of data. The most common usage for ssh is to connect to a terminal server or a shell to access a remote CLI (command line interface). SSH stands for Secure SHell, but you can also use SSH for secure file transfers as well as a tunneling protocol for TCP-traffic.

SSH is a client/server protocol which means that for any single ssh session there is a client, and a server. The client is the part that initiates the connection (establish a TCP connection) to the server, and the server is that part that waits for incoming connections on a TCP socket.

![Client-server Application - OOSE](data:image/png;base64,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)


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


## creating ssh keys

In order to use key based authentication we must either create a new ssh keypair on the client or move already created keys over to this client. 


- [ ] ssh keys
- [ ] logging in with keys, authorized_keys
- [ ] specifying key identity file
- [ ] Creating ssh keys with ssh-keygen
- [ ] Transfer public key manually and file permissions
- [ ] ssh-copy-id
- [ ] Extract the public key from the private
- [ ] key passphrases
- [ ] Running commands with ssh
- [ ] Local and remote port forwarding
- [ ] Escape to the ssh prompt
- [ ] ssh-agent
- [ ] ssh agent forwarding
- [ ] 


