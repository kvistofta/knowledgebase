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
