
SSH is a protocol that creates a protected tunnel in which you can transfer various kinds of data. The most common usage for ssh is to connect to a terminal server or a shell to access a remote CLI (command line interface).

SSH is a client/server protocol which means that for any single ssh session there is a client, and a server. The client is the part that initiates the connection (establish a TCP connection) to the server, and the server is that part that waits for incoming connections on a TCP socket.

Which part is client or server is relative to a specific session. A *server* can be both a ssh server and a client, and the same goes for a client computer. The client/server roles are important to distinguish when you look into a specific session since there are many different configuration files that are specific to ssh clients or servers.

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


On the same server there can be files in the ~/.ssh folder that are related to when the machine is being used as an ssh client connecting to a remote server. Here is the content of the /home/jimmy/.ssh/ folder of the same server as above.

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



