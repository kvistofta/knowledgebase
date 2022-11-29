# KVM-commands
These are KVM-related commands I regularily use.

For security-reason, never run these as root, but instead use sudo or another solution for granular access.

## Listing VMs


```shell
virsh list
```
List all started VM:s


```shell
virsh list --all
```
List all existing machines, even those turned off, like:

```
 Id    Name                           State
----------------------------------------------------
 2     vpn                            running
 5     fw                             running
 716   anothervm                      shut off
 ```
 

## Snapshots

```shell
virsh snapshot-list <vm-name>
```
List all existing snapshots for VM, like:

```
 virsh snapshot-list 66-kali
 Name                 Creation Time             State
------------------------------------------------------------
 baseline1            2021-11-14 16:18:07 +0100 running
 baseline2            2021-12-01 12:38:01 +0100 running

```


```code
virsh snapshot-create-as <vm-name> <snapshot-name>
```
Creates a new snapshot. Note that the state of a snapshot is stored so when reverting to a 'running' snapshot will make the VM running while reverting to a 'shut off' snapshot will leave the VM turned off.

```
virsh snapshot-revert <vm-name> <snapshot-name>
```


## Scripts and command snippets

 
I have a large number of vm:s that are clones of the same machine but with unique IP-addresses. I use these for teaching. In the below examples I use these machines:


```virsh list --all
 Id    Name              State
-----------------------------------
 397   linux-5-100       running
 398   linux-5-101       running
 399   linux-5-102       running
 400   linux-5-103       running
 401   linux-5-104       running
 ...
 ...
 ```
 

 Running the same virsh command on multiple VM:s using a simple bash loop
```bash
for X in $(seq 100 119); do virsh start linux-5-${X}; done

```


## VNC ports

The console of each VM can be reached via VNC and SSH port forwarding.

To find out which VNC port a VM is connected to, run the following commanf:
```shell
virsh vncdisplay <vm-name>
```

The output shows which socket the console is bound to:

```
sudo virsh vncdisplay linux-5-101
10.0.0.4:4101
```

The port number is a relative value where 0 means 5900 (standard port for VNC). To calculate the real port you must add 5900 to the number, in this case 5900+4101 = 10001


## SSH port forwarding

To reach the VNC port you can use SSH port forwarding (in my environment the socket 10.0.0.4:10001 is not reachable outside of the KVM host and in many other cases the socket is bound to localhost where port forwarding is the only way to reach that socket).

To create a port forward using SSH you can use the following command:
```shell
ssh -p <srvport> <user>@<srvip> -L 5900:10.0.0.4:10001
```
where:
* srvport is the ssh server port (if not 22)
* user@srvip is the way you connect to the KVM server
* 5900 is your local listening socket port on the ssh client
* 10.0.0.4 and 10001 (in my example above) is the VNC listening socket on the KVM server

After logging in, use any local VNC client and connect to **localhost:5900** to reach the console of the VM.
